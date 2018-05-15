# Progressing the numeric algorithms toward Ranges! (Part 1)

Ever since I found out about concepts, I’ve been fascinated with them. The other day, I was playing
a party game, and the question the game asked some of my friends was “If CHRIS had a nightclub, what
would their secret password be?”. The first answer was “typename T”, and the winning answer (by
popular vote) was "Throw a shrimp on the barbie. Also concepts are cool”. Firstly, as an Australian,
I’m legally obliged to say that we don’t throw shrimp on the barbie; primarily because we don’t have
shrimp in Australia. Secondly, concepts *are* cool!

Something even more fascinating to me than concepts, however, are their application in the [Palo
Alto Technical Report](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2012/n3351.pdf) and the
[Ranges TS](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2017/n4671.pdf). Now that the
Concepts TS has been partially merged into the C++20 Working Paper, you’ve probably started to hear
more about them. You might also have heard about the efforts to migrate the Ranges TS into the C++20
Working Paper too. If you’re not aware of what the Ranges TS brings to the table, sit back: you’re
in for a crash course.

## Concepts Lite
In July 2011, not long before C++11 was ratified, Bjarne Stroustrup and Andrew Sutton published a
paper called [Design of Concept Libraries for C++](http://stroustrup.com/sle2011-concepts.pdf) This
document outlines what it means to be a concept, and how to design one. This has served as the
groundwork for subsequent designs such as the Palo Alto TR and the Ranges TS, both of which are
outlined below.

A very quick summary of this document is that **constraints** are requirements imposed on *syntax*,
**axioms** are requirements imposed on *semantics*, and **concepts** are both constraints *and*
axioms together. Constraints on their own are fairly weak, but are checkable by the compiler. While
axioms are expected to hold forever, they cannot be statically checked. Concepts are essentially
predicates that, when satisfied, guarantee prescribed constraints are met, and that prescribed
axioms hold, for some generic algorithm. We'll explore this more in the example section.

## The Palo Alto Technical Report

Shortly after the dust settled on C++11, Alex Stepanov, the original designer of the STL, asked some
of the most well-known names in the C++ community to a meeting in Palo Alto: Jon Kalb, Paul McJones,
Sean Parent, Dan Rose, Bjarne Stroustrup, and Andrew Sutton, to name just a few. In this week-long
meeting, they studied and discussed the design behind most of the algorithms that make up the STL.

This analysis looked at how to provide concepts that can help us describe which types are suitable
for the algorithms in `<algorithm>`. It's a rather long report, but it informs the community about
how we can go about describing things such as `copy` and `sort`. Note that the numeric algorithms
are _not_ covered by the Palo Alto Report, and we'll be looking into some Palo Alto-like thinking in
Part Two of this blog.

A large portion of the Palo Alto Report is derived from Stepanov's original working on the STL, and
from his and McJones' book, _Elements of Programming_.

## The Concepts TS

The Concepts TS is the specification for how concepts can look in C++. A compiler can follow the
technical directions of this paper, and claim that they are ISO/IEC conforming.

### Defining a concept

A concept looks like this

```cpp
template <typename T>
concept bool Weakest = true;

template <typename T1, typename T2 = T>
concept bool has_equals = // poorly-defined concept, see below, do not use this at home!
   requires(T1 const& t1, T2 const& t2) {
      {t1 == t2} -> bool;
   };
```

This `requires` block, called a _requires-expression_, is a way of saying "if all the expressions
in the block are well-formed, return true; otherwise, return false". The `{t1 == t2} -> bool` is
essentially saying `t1 == t2` must be well-formed _and_ the expression must be implicitly
convertible to `bool`.

### Using a concept

While we won't be using concepts in _Part One_, we'll be using them in _Part Two_, so it's a good
idea to quickly cover how to use them.

```cpp
template <typename T>
requires
   has_equals<T>
bool operator!=(T const& a, T const& b) noexcept
{
   bool const eq = a == b;
   return !eq;
}

template <has_equals T>
bool operator!=(T const& a, T const& b) noexcept
{
   bool const eq = a == b;
   return !eq;
}
```

In the first one, we provide the requirements outside of the template header, and in the second one,
it replaces the `typename`. Both are equivalent, but the second one is less-error prone, and is
preferred when possible. When a type satisfies the predicate outlined by a concept, we say that it
_models_ the concept.

### C++20 concepts

There are some changes between the Concepts TS and what C++20 offers. To the best of my knowledge,
everything in this blog is compatible with C++20's version of concepts, with the exception of
`concept bool`, which becomes just `concept`.

## The Ranges TS

The Ranges TS is a successful attempt at moving the Palo Alto Report from an informational document
to an implementation and technical specification. It started with Eric Niebler's
[range-v3](https://github.com/ericniebler/range-v3), which is like the prototyping playground for
the Ranges TS. If you're not familiar with range-v3, go and watch ["Ranges for the Standard
Library"](https://youtu.be/mFUXNMfaciE), which is a keynote at CppCon by Eric on how range-v3 can be
applied to make cool things possible.

Like the Palo Alto Report, the Ranges TS takes just the algorithms part of range-v3, and brings them
to life in the Standard. As with the Palo Alto TR, there are some changes to the algorithms in
`<algorithm>`, and they actually reasonably check the concepts that our beloved algorithms have been
imposing since 1994 at compile-time through standardised concepts.

The reference implementation for the Ranges TS is called
[cmcstl2](https://github.com/CaseyCarter/cmcstl2), which can be found on Casey Carter's GitHub.

### Efforts to merge the Ranges TS into C++20

Right now, thanks to [P0896](https://wg21.link/p0896), [P0898](https://wg21.link/p0898), and
[P1037](https://wg21.link/p1037), work is being made to move the Ranges TS into C++20, so that
everyone can enjoy them, not just those with the luxury of turning on experimental features.

## An example: `EqualityComparableWith`

Let's build the `EqualityComparableWith` concept from the Ranges TS, from the ground up.

It doesn't make sense for `has_equals`, defined above, to be a concept. This is a constraint: we can
check, at compile-time, that `a == b` is valid. It doesn't mean anything at all, because it doesn't
tell us what `operator==` should be doing. What we need are a set of rules that help to define what
it means for operator== to work. Constraints don’t offer this, but *axioms* do.

So, what can axioms say, that our constraint hasn't already? Quite a lot, actually: our `has_equals`
has zero semantics right now, so even `vector<int>{} == 0` is valid (provided someone writes the
appropriate `operator==`). This comparison makes no sense, so let's give it some meaning.

For starters, `a == a` should always be true. This means that our `has_equals` needs to impose
reflexivity on its types, with respect to equality. Secondly, `(a == b) == (b == a)` should hold.
This is known as commutativity; we can update `has_equals` to constrain `b == a`, since we don't
presently do that:

```cpp
template <typename T1, typename T2>
constexpr bool has_equals =
   std::is_same_v<bool, decltype(std::declval<T1>() == std::declval<T2>())> &&
   std::is_same_v<bool, decltype(std::declval<T2>() == std::declval<T1>())>;
```

Next, we it stands to make sense that, for some `T1 c` or `T2 c`, if, and only if, `a == b` and
`b == c`, does the expression `a == c` hold. This is known as transitivity, and with these three
axioms, we can say that `has_equals` models the concept of [_equivalence_](https://en.wikipedia.org/wiki/Equivalence_relation). Let's update our `has_equals` so that it
reflects our axioms:

```cpp
template <typename T1, typename T2>
concept Equivalence =
   std::is_same_v<bool, decltype(std::declval<T1>() == std::declval<T2>())> &&
   std::is_same_v<bool, decltype(std::declval<T2>() == std::declval<T1>())>;
   // axiom: (a == a)
   // axiom: ((a == b) == (b == a))
   // axiom: ((a == b) && (b == c) && (a == c)) || ((a == b) && !(b == c) && !(a == c))
```

We provide our axioms as comments, because we don't have a way of checking them (and may never have
a general way of doing so). Nevertheless, they still need to hold whenever we say that a type models
equivalence. We're not done with our concept. Notice that we say `!(b == c)`: this can be rewritten
as `b != c`. Whenever there is an inverse (or some alternative) to an operation, our concept should
capture that it is possible to do that alternative too. We'll relax the equivalence bit to start
with, and make it more complicated with a second concept (counterintuitively, this is actually for
brevity). To speed things up, I'm going to take the definition from the
[Ranges TS](https://timsong-cpp.github.io/cppwp/ranges-ts/concepts.lib.compare.equalitycomparable).

```cpp
template <typename T1, typename T2>
concept WeaklyEqualityComparable =
   requires(T1 const& a, T2 const& b) {
      {a == b} -> Boolean&&; 
      {b == a} -> Boolean&&;
      {a != b} -> Boolean&&;
      {b != a} -> Boolean&&;
      // Boolean is a concept that lets us generalise `bool`.
      // tl;dr is that if it walks like a `bool`, and quacks like a `bool`,
      // then it models the Boolean concept.
   };
   // axiom: (bool(a == b) == bool(b == a))
   // axiom: (bool(a != b) == bool(!(a == b)))

template <typename T1>
concept EqualityComparable = WeaklyEqualityComparable<T1, T1>;
   // axiom: (a == a)
   // axiom: (bool(a == b) && bool(b == c) && bool(a == c)) || (bool(a == b) && bool(b != c) && bool(a != c))
```

Note that non-equality is _irreflexive_: `a != a` is nonsensical. We _still_ aren't done! Nowhere,
in any of the above, do we explain what it means for `vector<int>{} == 0` to make sense. We were
just laying the groundwork for that discussion.

When `T1` and `T2` are different, we first need to make sure that they are comparable to themselves:
`EqualityComparable<T1> && EqualityComparable<T2> && WeaklyEqualityComparable<T1, T2>`. After all,
if your type can't provide a basis for equivalence against _itself_, then how can you provide a
basis for equivalence against anything else?

```cpp
template <typename T1, typename T2>
concept EqualityComparableWith =
   EqualityComparable<T1> &&
   EqualityComparable<T2> &&
   WeaklyEqualityComparable<T1, T2>;
```

Now we have a way to check `vector<int>{} == 0`, but we still haven't provided any meaning to
what the expression might mean. That's because the expression doesn't make sense at any level.
Comparing a container to a non-container is like comparing an apple to a truck designed to carry
apples: there's nothing remotely similar about them, so we shouldn't be allowed to do so. What
we need to do is refine our concept to prevent us from comparing unrelated types to one another.

The Ranges TS offers a concept called `CommonReference`, which checks that we can boil `T1` and `T2`
down to the same reference type. If such a type exists, then that type should _also_ be
`EqualityComparable`. Here's the Ranges TS definition of `EqualityComparableWith`:

```cpp
template <class T, class U>
concept bool EqualityComparableWith =
   EqualityComparable<T> &&
   EqualityComparable<U> &&
   CommonReference<
      const remove_reference_t<T>&,
      const remove_reference_t<U>&> &&
   EqualityComparable<
      common_reference_t<
         const remove_reference_t<T>&,
         const remove_reference_t<U>&>> &&
   WeaklyEqualityComparableWith<T, U>;
   // using C = common_reference_t<const remove_reference_t<T>&,
   //                              const remove_reference_t<U>&>;
   // axiom: bool(t == u) == bool(C(t) == C(u))
```

This is the C++ description of our English reasoning for the _whole_ section.

### What, you're not going to define `CommonReference` too?

Nope! As much as I love `CommonReference`, defining it isn't part of our conversation. All we need
to understand is that there's a type called `common_reference_t<T1, T2>`, which works out what the
'gcd' for `T1` and `T2` is, and that `CommonReference` checks that `common_reference_t<T1, T2>`
exists.

### Why did you suddenly start casting to `bool` everywhere in your axioms?

According to Casey Carter, because we don't check that `decltype(a == b)` and `decltype(b == a)` are
the same type, we can't reliably check that `(a == b) == (b == a)`. The only way to keep the code
sensible is to convert both operands to `bool` and ensure that axiom holds.

## Conclusion

What you should take away from _Part One_ is that concepts aren't meant to only check syntax: that's
just the tip of the iceberg. In addition to constraint checking (which can be employed by
type-traits), your concepts should also provide axiomatic rules that define what it means for a type to model your
concept.

I didn't intend to go into _so_ much detail about concepts, but in order for you to appreciate why
this blog exists, you do need that detail. Let's wrap it up here, so you can digest what's been said
without reflux. I'll post _Part Two_ in the next few days, where we'll investigate how to approach
designing and adding concepts to an algorithm.

Till then!

## Thanks

This blog wouldn't have been possible without the combined input from Arien Judge, Casey Carter,
Gordon Brown, Matt Egan, Morris Hafner, and Simon Brand. Thank you!
