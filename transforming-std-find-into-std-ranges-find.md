# Transforming `std::find` into `std::ranges::find`

The [last time we met](blog/2018/05/15/prepping-yourself-to-conceptify-algorithms.md),
we had some fun discovering what it means to write a concept. Specifically, we derived the concept
`ranges::EqualityComparableWith`, which allows us to describe algorithms that check for cross-type
equivalence (in other words, checking that two objects with potentially different types are
equivalent). Although we defined a concept, we never actually used it. That's where today's article
comes in.

The mathematics behind concepts is so vast that we can't cover it in a short discussion. Today,
we'll derive the C++ standard library linear search algorithm, `find`, as it is in the
[Ranges TS](https://timsong-cpp.github.io/cppwp/ranges-ts/alg.find). There is a strong chance that
this definition of `find` will find (ha!) its way into C++20. We will begin by defining some vital
prerequisites, before looking at `std::find` as it is today, and then _refining_ it so that it may
resemble what we can enjoy in the Ranges TS. As with last time, there is a lot of material to cover,
so make yourself that drink now: you won't want to interrupt the flow half-way through!

**Warning: this article is a direct continuation of [_Prepping Yourself to Conceptify
Algorithms_](blog/2018/05/15/prepping-yourself-to-conceptify-algorithms.md). You
may be left in the dark about how and why things work if you skip the first part.**

## Refining and weakening concepts

When we say that we refine (or strengthen) a concept, we mean that we are applying more requirements
to the concept. This makes the concept more specialised. For example, we refined
`WeaklyEqualityComparable` so that we could define a concept that describes equivalence relations;
`EqualityComparable` is thus a refinement over `WeaklyEqualityComparable`.

When we say that we weaken a concept, we mean that we are removing requirements to make it more
general. I didn't see the point in weakening concepts for a very long time, and we won't explore
the notion of weakening concepts in this article, but it does make for a central discussion in this
series' final instalment.

## Linear search

A linear search takes a sequence of elements in some data structure and an item to find, and returns
a position in the data structure to where the item is, or a predefined value to indicate that it
isn't in the structure. Below are examples of linear searches:

```cpp
// For vectors
template <typename T>
std::ptrdiff_t find(std::vector<T> const& v, T const& value) noexcept
{
   for (auto i = std::ptrdiff_t{}; i < ranges::distance(v); ++i) {
      if (v[i] == value) {
         return i;
      }
   }

   return ranges::distance(v);
}

// For std::arrays
template <typename T, std::ptrdiff_t N>
std::ptrdiff_t find(std::array<T, N> const& a, T const& value) noexcept
{
   for (auto i = std::ptrdiff_t{}; i < ranges::distance(a); ++i) {
      if (a[i] == value) {
         return i;
      }
   }

   return ranges::distance(a);
}

// For raw arrays (yuck!)
template <typename T, std::ptrdiff_t N>
std::ptrdiff_t find(T const(& a)[N], T const& value) noexcept
{
   for (auto i = std::ptrdiff_t{}; i < N; ++i) {
      if (a[i] == value) {
         return i;
      }
   }

   return N;
}

// For array-indexed pointers with a distance (ew!)
template <typename T>
std::ptrdiff_t find(T const* const a, std::ptrdiff_t const distance, T const& value) noexcept
{
   for (auto i = std::ptrdiff_t{}; i < distance; ++i) {
      if (a[i] == value) {
         return i;
      }
   }

   return distance;
}
```

If you're familiar with the `span` vocabulary-type, you can probably imagine how we might factorise
this into a _single_ function. A single function to handle this verbatim logic would be superb,
since we can then forget about everything above.

```cpp
// For vectors and arrays of all shapes and sizes!
template <typename T>
int find(gsl::span<T const> const s, T const& value) noexcept
{
   for (auto i = std::ptrdiff_t{}; i < ranges::distance(s); ++i) {
      if (s[i] == value) {
         return i;
      }
   }

   return ranges::distance(s);
}
```

What about linked-lists? If you took a data structures and algorithms class, you probably learnt
that a singly-linked list looks somewhat like this:

```cpp
// deliberately not std::forward_list, for expositional reasons
template <typename T>
struct snode {
   T value;
   snode* next = nullptr;
};

template <typename T>
struct slist {
   snode<T> head;
};

// linear search for a singly-linked list
template <typename T>
snode<T> const* find(slist<T> const& l, T const& value) noexcept
{
   for (auto const* i = &l.head; i != nullptr; i = i->next) {
      if (i->value == value) {
         return i;
      }
   }

   return nullptr;
}
```

Cool, that's a singly-linked list done. What about a doubly-linked list?

```cpp
template <typename T>
struct dnode {
   T value;
   dnode* next = nullptr;
   dnode* prev = nullptr;
};

template <typename T>
struct dlist {
   dnode<T> head;
};

// linear search for a doubly-linked list
template <typename T>
dnode<T> const* find(dlist<T> const& l, T const& value) noexcept
{
   for (auto const* i = &l.head; i != nullptr; i = i->next) {
      if (i->value == value) {
         return i;
      }
   }

   return nullptr;
}
```

Hmm... That's also verbatim text. Can we factorise it? Yes, we can.

```cpp
template <template <typename> typename Node, typename T>
Node<T> const* find_impl(Node<T> const* i, T const& value) noexcept
{
   for (; i != nullptr; i = i->next) {
      if (i->value == value) {
         return i;
      }
   }

   return nullptr;
}

template <typename T>
snode const* find(slist const& l, T const& value) noexcept
{
   return find_impl(&l.head, value);
}

template <typename T>
dnode const* find(dlist const& l, T const& value) noexcept
{
   return find_impl(&l.head, value);
}
```

So, we've been able to factorise our linked-list linear search too. Let's take a look at the
algorithms side-by-side, to see if we can factorise them into one function:

```cpp
template <typename T>
std::ptrdiff_t find(gsl::span<T const> const s, T const& value) noexcept
{
   for (auto i = std::ptrdiff_t{}; i < ranges::distance(s); ++i) {
      if (s[i] == value) {
         return i;
      }
   }

   return ranges::distance(s);
}

template <template <typename> typename Node, typename T>
Node<T> const* find_impl(Node<T> const* i, T const& value) noexcept
{
   for (; i != nullptr; i = i->next) {
      if (i->value == value) {
         return i;
      }
   }

   return nullptr;
}
```

The code is _nearly_ the same, but not quite. Where it differs is in how we determine the end of the
sequence, how we take the successor of our current element, and how we retrieve the value. While
this is conceptually the same, it's implementationally different. What about getting input from the
user?

```cpp
template <typename T>
std::ptrdiff_t find(std::istream& in, T const& value)
{
   auto t = T{};
   for (auto i = std::ptrdiff_t{}; in >> t;) {
      if (t == value) {
         return i;
      }
   }

   return -1; // I guess?
}
```

Again, similar at the concept level; and different in the implementation. This problem will arise
for every container-like data structure that you design. Given the current approach, you'll be able
to factorise out textual similarities, but we're not actually factorising all that much out. There's
still complete code redundancy here, and that's not okay.

Fortunately, iterators offer us a way to fold all of our `find` implementations into a single,
easy-to-understand algorithm that we'll discuss in a bit.

## Iterators

Iterators are a topic rich with mathematics. Because they are part of what makes generic programming
so powerful, this topic spans multiple chapters in books on generic programming[[1]][[2]].
Informally, you might know iterators to be the 'glue' between algorithms and data structures.
Without iterators, if there are N algorithms and M data strucutres, you can expect at most `N * M`
algorithmic implementations. With iterators, there need only be `N` algorithm implementations, but
there can be more (if you can optimise for a particular iterator concept).

More formally, iterators are a generalisation of pointers, and allow for abstracting how we address
elements in some data structure. All iterators let us either read from an element (via a source
function), write to an element (via a sink function), or both. All iterators also have a successor
function, which lets us find the 'next' element in the structure. In C++, for some iterator `i`, we
refer to both source and sink functions as `*i`, and the successor function as `++i`.

Going back to our discussion of linear searches, `*i` will represent `span[n]` and `n->value`. `++i`
represents `++n` (for integers) and `n = n->next` (for list nodes). Iterators can even abstract over
our `istream` example! This might look strange at first, but we now have a way to factorise all of
our `find` implementations into one generic algorithm that still concisely describes exactly what we
want.

### Input iterators

An input iterator is an iterator that can be read from. This might be from a vector of objects or
some other container, or even an iterator that abstracts over an input stream (such as the character
input). We may only read from an input iterator, and so only have a source function and a successor
function: there is no sink function. If we wish to write to some element in a range, we need at
least an output iterator.

One critical property of input iterators is that they are 'single-pass'. That means that once you
apply the successor function to an input iterator, you can never get the data that the iterator
offered back. Further, all copies of said iterator become 'invalid', and may no longer be read from.
This notion of 'single-pass' is relaxed in iterator concepts that refine input iterators, but
because the input iterator concept is _so_ general, it must be present to permit said generality. An
iterator that abstracted over retrieving keyboard input is an example of this 'single-pass' axiom.
Each time that you press a key is, from the algorithm's perspective, a unique occurrence.

#### Examples of input iterators

```cpp
std::vector<int>::const_iterator
std::istream_iterator<double>{std::cin}
```

#### What about other iterators?

If you're familiar with the STL, then you're probably aware of at least four other iterator
concepts. While there are other iterator concepts, they are not relevant for our discussion, so we
shan't consider them today.

### Ranges

One of the papers that proposing to integrate the Ranges TS with the Standard[[3]] defines a range
to be "an iterator and a sentinel that designate the beginning and end of the computation, or an
iterator and a count that the beginning and the number of elements to which the computation is
applied." We'll properly define what a sentinel is a little bit later on, but for _right now_, you
can think of it as an iterator delimiting the aforementioned computation: this is how Standard C++
has defined it up until the Ranges TS anyway.

We use a half-open interval notation to denote a range: `[i, s)` means that we can traverse from `i`
up to `s`, but we can never apply a source function on `s`, nor may we succeed it. If `i == s`, then
the range is empty. While `i != s`, it denotes the elements in some data structure spanning
`[i, s)`. We say that `s` is reachable from `i` if, and only if, we can apply the successor function
a finite number of times before `i == s` is satisfied. Not all ranges are finite, but they're for a
future blog (if at all). Because counted ranges are not relevant to the discussion, we'll omit them
too, for brevity.


## `std::find`

Now that we're familiar with the concept of input iterators and ranges, let's revisit our friend,
`find`.

```cpp
template <typename InputIterator, typename T>
InputIterator find(InputIterator first, InputIterator last, T const& value)
{
   for (; first != last; ++first) {
      if (*first == value) {
         return first;
      }
   }

   return last;
}
```

That's it! The algorithm is still very much the same: we provide some mechanism for determining how
to end the search, a way to succeed the current position, and a way to read the data. However, the
details of how to implement these for each structure are abstracted out of the algorithm. This is
actually a valid implementation for `std::find`. If you want to just pass a data structure, and not
worry about iterators, then you can overload a range-based `find`:

```cpp
template <typename InputRange, typename T>
auto find(InputRange const& rng, T const& value)
{
   return find(ranges::begin(rng), ranges::end(rng), value);
}
```

The beautiful thing about this generic implementation is that provided your data structure can be
generalised to be a range, these latest two iterations of `find` are _probably_ valid for your
purposes too. Notice that I said _probably_. Since this is such a general implementation for `find`,
there need to be some rules put in place. Some requirements, that we'll define below, by refining
`find`.

Using the `InputRange` version of `find` is hopefully a drop-in replacement for all cases of `find`,
provided that your data structure is also compatible with the `InputIterator` version. All standard
containers, `std::string`, Boost containers, and `gsl::span` are compatible. If you have two
pointers to an array, this is also compatible:

```cpp
constexpr auto size = 10;
auto p = std::make_unique<int[]>(size);
// ...
find(p.get(), p.get() + size, 0); // of course, you'd be using span<int> in real code.
```

Our linked-lists, however, are not compatible (yet). They don't have a notion of what an iterator
is, but it's fairly simple (but mechanical) [to give them one](https://godbolt.org/g/yzL3bC). In
production code, we would never implement our own list type (and we will rarely want to use a list
anyway): we'd use `std::forward_list` or `std::list`, so we won't be considering `slist` or `dlist`
from here on.

## Refinement One -- Locking Down Input Iterators

We've already discussed the concept of an input iterator, and the Ranges TS provides that for us. To
make sure that we're not going to try and pass in something that we can't iterate over, we ought to
make our first refinement that.

```cpp
template <ranges::InputIterator I, typename T>
I find(I first, I last, T const& value);
```

Now, silly code, such as `find(0, 1, 0)` won't be allowed, because integers do not model
`InputIterator`.

## Refinement Two -- Introducing Sentinels

Recall that in the ranges section, we briefly discussed this notion of a sentinel, and I dismissed
it as a delimiting iterator. That's not a complete definition, but it is what most C++ programmers
are familiar with, and it was necessary for the discussion to advance. We'll now approach the
definiton of a sentinel more carefully.

Sentinels are a generalisation of iterators, in such a way that they delimit the end of a range.
This means that while iterators may be sentinels, sentinels need not be iterators. The caveat is
that the sentinel must be default-constructible, copyable, and `WeaklyEqualityComparableWith` the
iterator whose range we wish to iterate over.

Axiomatically, given an iterator `i` and a sentinel `s` that denote the range `[i, s)`:
   1. `i == s` is well-defined
   2. Given `bool(i != s)`, `*i` is valid and `[++i, s)` also denotes a range.
   3. The domain of `==` can change over time, such that `i == s` may no longer be valid. This makes
      for an interesting discussion, but is probably best addressed in a future blog post.

A non-iterator example of a sentinel might be a [type that checks if certain data has been read from
an input stream, or if the stream has failed](https://github.com/appliedmoderncpp/amcpp/blob/master/include/amcpp/is_value_sentinel.hpp).

Let's refine `find`:

```cpp
template <ranges::InputIterator I, ranges::Sentinel<I> S, typename T>
I find(I first, S last, T const& value)
{
   for (; first != last; ++first) {
      if (*first == value) {
         break;
      }
   }

   return first;
}
```

Notice that we changed the implementation to _only_ return `first`: this is because we might not
always have a `last` that shares a compatible type with `I`, and so returning `last` could lead to
some compile-time errors at best, or strange run-time errors at worst.

## Refinement Three -- Adding `EqualityComparableWith`

'Finally!', I hear you cry. Yes, we'll now refine `find` to consider the `EqualityComparableWith`
concept that we laboured over in the last article. All iterators have a `value_type` and a
`reference` type, and the Ranges TS provides us with a mechanism to extract them from conforming
iterator types. Under the Ranges TS, we use `ranges::value_type_t` and `ranges::reference_t`, and
when merged with C++20, they'll likely be called `std::iter_value_t` and `std::iter_ref_t` (for
various reasons). Because an iterator's source function is supposed to return a reference to what
it addresses, we will use `reference_t`, not `value_type_t`.

```cpp
template <InputIterator I, Sentinel<I> S, EqualityComparableWith<reference_t<I>> T>
I find(I first, S last, T const& value);
```

And this stops us from committing silly mistakes like

```cpp
auto v1 = std::vector<int>{0, 1, 2};
auto v2 = std::vector<std::vector<int>>{ {0, 1, 2}, {3, 4, 5} };
auto i = find(begin(v1), end(v1), v2); // An honest mistake, really
```

## Aside -- Invocables

The `Invocable` concept is a generalisation of function types, and includes any type that `invoke`
can be applied to. As per the Ranges TS:

> The `Invocable` concept specifies a relationship between a callable type ... `F` and a set of
> argument types `Args...` which can be evaluated by the library function invoke....

In short, this means that given some function with a type `F`, and parameters with types `Args...`,
`Invocable` will let us know whether or not the function can be invoked. For example:

```cpp
void foo();
using foo_t = decltype(foo);
static_assert(ranges::Invocable<foo_t>); // okay, foo() is expected
// static_assert(ranges::Invocable<foo_t, int>); // error: foo(0) isn't possible

int rando(int, int = 0);
using rando_t = decltype(rando);
static_assert(ranges::Invocable<rando_t, int, int>); // okay, rando(0, 0) is expected
// static_assert(ranges::Invocable<rando_t, int>); // error: rando's signature is equivalent to int(int, int),
                                                   // but this checks for R(int)
// static_assert(ranges::Invocable<rando_t>); // error: rando() isn't possible
// static_assert(ranges::Invocable<rando_t, std::vector<int>>); // error: not possible
```

`Invocable` makes no assumptions on the return type of the callable, nor does it impose any axioms
on _what_ the function returns.

### `RegularInvocable`

Sometimes, we can't just rely on plain old `Invocable`. Semantically, an object with a type that
models `Invocable` is free to return whatever it likes. Under these rules, `square(1)` is allowed
to return a random number, for every call to `square(1)`. This is great for randomisation functions
and functions that get user input, but not at all strong enough for functions that should map to
the same result when provided with the same input.

`RegularInvocable` imposes the same constraints as `Invocable`, but axiomatically requires that the
function object be equality-preserving (the result is the same each time you pass the same
parameters), and that the arguments not be modified.

## Refinement Four -- Projections

### Introducing projections

Projections are easier to introduce with `sort` and `lower_bound` (kudos to Sean Parent for this
example). I'll follow up with projections and `find` after. We'll read a collection of `employee`
data from the character input, then sort them, before finding someone with the surname Smith.

```cpp
struct employee {
  int id;
  std::string surname;
  std::string first_name;
  double salary;
};

auto employees = std::vector<employee>{ranges::istream_iterator<employee>{std::cin},
   ranges::default_sentinel{}};

std::sort(employees,
   [](auto const& a, auto const& b) noexcept { return a.surname < b.surname; });

std::lower_bound(employees, "Smith",
   [](auto const& value, auto const& e) noexcept { return e.surname < value; });
```

The problem with this approach is that the lambdas are performing the same logic, on the same types;
yet they have distinct signatures. Any update to one of the lambdas very likely means that an update
to the other is also required. Logically speaking, we are computing `string < string`, but this is
orthogonal to the type system: `sort` expects a function that takes two `employee`s, and
`lower_bound` expects a function that takes a `T` and an `employee`. It would be nice if we could
factorise the lambdas into a single object, but that's not at all possible, because our algorithms
require different predicates. From the algorithms' point of view, the comparisons in `std::sort` and
`std::lower_bound` respectively resemble:

```cpp
if (std::invoke(pred, *lhs, *rhs)) { /* ... */ } // for std::sort
if (std::invoke(pred, *lhs, value)) { /* ... */ } // for std::lower_bound
```

What we want to achieve is logic that is analagous to the _text_:

```cpp
if (lhs->surname < rhs->surname) { /* ... */ } // for std::sort
if (lhs->surname < value) { /* ... */ } // for std::lower_bound
```

This is where _projections_ come in. A projection is a mapping from our iterator's reference type to
the type that we wish to perform the operation on. A projection may be anything that can be treated
as though it is a unary function of the iterator's reference type. This includes pointers-to-members
of the iterator's value type, as shown in the snippet below.

```cpp
auto predicate = std::less<>{};
auto projection = &employee::surname;
ranges::sort(employees, predicate, projection);
ranges::lower_bound(employees, "Smith", predicate, projection);
```

Here, we are demonstrating a projection of `employee::surname` onto our respective algorithms. While
a projection doesn't make the comparisons _literally_ look like our goal, the logic inside the
algorithms has now been customised to resemble it.

With this, we can eliminate redundancies, and make the algorithm logic simpler. If `predicate`
becomes `std::greater<>{}`, it's reflected in both algorithms automatically, rather than us needing
to remember to update both (and then verify that both are correct!).

It also makes it _possible_ to functionally reason about your code a little bit more. If we're just
using `lower_bound`, we _can_ swap the first snippet below for the second.

```cpp
// (1)
ranges::lower_bound(employees, "Smith",
   [](auto const& value, auto const& e) noexcept { return e.surname < value; });
// (2)
ranges::lower_bound(employees, "Smith", std::less<>{}, &employee::surname);
```

[C++ Reference](http://en.cppreference.com/w/cpp/algorithm/lower_bound) defines `lower_bound` as

> Returns an iterator pointing to the first element in the range [first, last) that is _not_ less
> than (i.e. greater or equal to) value, or last if no such element is found.

To me, the second snippet is easier to read, as we can almost read our lambda's logic as

> `lower_bound` on employees, where `"Smith"` is not `less` than the current `employee`'s `surname`.

Contrived? Sure. That doesn't make it any less true, which is why I make liberal use of projections.
Please don't interpret this to mean that recommended practice suddenly becomes "use projections
everywhere". It means that if you can factorise out one or more components in multiple algorithms,
then you should do so.

### Projections on `find`

If we want to find a specific employee in a range of employees, based on their surname, we can't
use `find`, because `find` doesn't let us discriminate. We have a value, and that's that. If we
want to do this today, we need to use `find_if`.

```cpp
std::find_if(begin(employees), end(employees), [](auto const& e) noexcept {
   return e.surname == "Smith"; });
```

We can again use projections to stick with `find`, similarly to my final `lower_bound` example.
Without further ado, let's introduce projections to `find`:

```cpp
template <InputIterator I, Sentinel<I> S, typename T, typename Proj = identity>
requires
   EqualityComparableWith<value_type_t<projected<I, Proj>>, T> &&
   EqualityComparableWith<reference_t<projected<I, Proj>>, T>
I find(I first, S last, T const& value, Proj proj = Proj{})
{
   for (; first != last; ++first) {
      if (ranges::invoke(std::ref(proj), *first) == value) {
         break;
      }
   }

   return first;
}
```

Well, this is a bit of a mouthful. We introduce an (apparently) unconstrained type called `Proj` and
set the default type to be `identity`, which is a function object that returns whatever it is
passed (no copying involved). The type `projected` is a convenience-type that checks that `I` can be
read from, and that `Proj` models `RegularInvocable`. Since we want `proj` to be invisible except
when necessary, we give it a default value.

`proj` has a signature equivalent to `auto&&(reference_t<I>)`, which means that it takes a
dereferenced iterator and returns an arbitrary type, so we must invoke it to get back whatever we
plan to compare against `value`. Note that the `auto&&` return type means that we can't pass a
function with a `void` return type.

Whew! We've come a long way since our first iteration of `find`, all the way at the top. We've
looked at so much, and you're forgiven if you thought that this was the end of the road but we have
one last stop....

### Intermission the Second

Just like our first intermission, this second one revolves around invocable concepts. This time, we
consider `Predicate` and `Relation`. `Predicate` refines `RegularInvocable` such that the return
type of the invocable object models `Boolean`.

A `Relation` is a `Predicate` that takes exactly two types that are somehow 'related', similarly to
how `EqualityComparableWith` 'relates' types. In fact, `EqualityComparableWith<T1, T2>` describes a
`Relation` for two types `T1` and `T2` on `==`.

## Fifth and final refinement -- indirect relations

The _requires-expression_ we established in the last section is quite verbose, has redundancy, and
is error-prone. Fortunately, there's a concept called `IndirectRelation`, which is simpler to read
than our mouthy friend, and also checks some things that beyond the scope of our discussion. Tim
Song hosts a [HTML-based version of the Ranges TS](https://timsong-cpp.github.io/cppwp/ranges-ts/indirectcallable.indirectinvocable),
where you can read it, if you're interested. There are two caveats to using `IndirectRelation`:

   1. It's not imposing an equivalence relation. `equal_to` ensures that there is an equivalence
      relation, but `IndirectRelation` imposes no such requirement. This is useful to know when
      implementing other algorithms that need `IndirectRelation`.
   1. It requires two input iterator-like types, so we need to hand it a `T const*` rather than
      plain-old `T`. This might seem like a misnomer, since our relation is `proj(*i) == value`, so
      one iterator type and a `T` looks better. You might be right in thinking this for _`find`_,
      but there are other algorithms -- such as `find_end` and `equal`-- that require types to model
      `IndirectRelation` and provide two input iterator-like types. Since this is a convenience
      concept, and because pointers model `InputIterator`, having two `IndirectRelation` concepts is
      a bit redundant.

```cpp
template <InputIterator I, Sentinel<I> S, typename T, typename Proj = identity>
requires
   IndirectRelation<equal_to<>, projected<I, Proj>, T const*>
I find(I first, S last, T const& value, Proj proj = Proj{});
```

And we've now finished transforming `std::find` into what `std::ranges::find` should look like!

## I thought the Ranges TS was about, you know, _ranges_

It is! Everything that we've just discussed is to do with finding elements in some range. What you
might be expecting are range-based algorithms, similar to the _range-for_ statement. We've pretty
much run out of time, but since we've done most of the derivation above, there's not much left to do
for a range-based `find`.

```cpp
template <InputRange Rng, typename T, typename Proj = identity>
requires
   IndirectRelation<equal_to<>, projected<iterator_t<Rng>, Proj>, T const*>
safe_iterator_t<Rng> find(Rng&& rng, T const& value, Proj proj = Proj{})
{
   return find(ranges::begin(rng), ranges::end(rng), value, std::ref(proj));
}
```

`InputRange` _subsumes_ (read: absorbs) both `InputIterator` and `Sentinel<InputIterator>`, and
we're left with a type called `Rng`. Because `Rng` isn't an iterator type, we can't directly apply
it to `projected`: we first need to extract its iterator type (which is a required type-name by
`InputRange`) and apply that instead.

The `safe_iterator_t` is a wrapper that says "hey, what you get back might actually be dangling. You
do the unsafe stuff, it's not _my_ responsibility". This in turn, lets us pass both lvalues and
rvalues to our range-based `find`. After that, it's simply a case of calling our iterator-based
overload.

Yes, this will be shipping as an overload for `std::ranges::find`.

## Conclusion

Today, we made lots of progress. We first established why iterators are a powerful tool that aid our
efforts to refactor algorithms that perform the same operation, but require different implementations,
into a single, general algorithm. Next, we generalised the concept of a delimiting iterator to a
sentinel, which determines when we've reached the end of a range. Following on, we applied
`EqualityComparableWith`, because it seemed appropriate at the time.

We then made our first detour to explore what `RegularInvocable` means, and then applied that
knowledge to further generalise our application of `*i` through the use of projections. After a
second detour to learn about predicates and relations, we simplified our use of the projection even
further.

### Where to from here?

Both the previous article and this one are expositional for the concluding discussion, where I
chronicle how I refine `std::accumulate` so that it might end up in C++23. Given that there's a lot
of history and maths behind the design of the Palo Alto Report, the Ranges TS, and what's (hopefully)
going to work its way into C++20, I couldn't just start there. It's very important that you actually
understand what is happening in the revised algorithms found in namespace `std::ranges`, and more
importantly, _why_ the decisions made are so, before continuing on to something novel and completely
unreviewed (as far as I'm aware, there haven't been any committee discussions on numeric algorithms,
and it's a non-trivial task to refine them).

### CppCon 2018 class

Although I've written this three-part series to talk about my ideas for conceptifying `accumulate`,
now is probably as good a time as any to mention that I'm teaching a class at CppCon 2018, titled
[_Generic Programming 2.0 with Concepts and Ranges_](https://cppcon.org/generic-programming-2.0-with-concepts-and-ranges).
If you find the contents of this series interesting, and can make it to Bellevue for the end of
September, you will probably enjoy this class.

Even if you don't think the class is for you, you should definitely consider attending the conference!
Conferences -- especially CppCon -- are a great way to network with engineers working on cool
technologies (that you might use or one day use), a great way to promote your company, and of course,
to learn about what speakers wish to share. If you'd like to present on a main track at a conference
one day, CppCon also has lightning talks (which span for up to fifteen minutes), and open-content
sessions (which span between thirty and ninety minutes). They're a great way for you to practice with
minimal pressure, and usually spark very interesting discussions.

### RSS Feed

It's 2018, and I've just learnt about what RSS is (I first heard about it in 2009 or 2010). I've set
up an [RSS feed](feed.xml) in case you wish to follow more of my blog posts.

### Comments and feedback

I haven't yet set up Disqus for this site. If you'd like to discuss what you've read here, please
head to this [GitHub issue](https://github.com/cjdb/cjdb.github.io/issues/5). I'm always open to
feedback!

### Acknowledgements

I would like to thank Arien Judge, Callum Howard, Duncan McBain, Gordon Brown, Kate Gregory, Matt
Stark, Nicole Mazzuca, Shafik Yaghmour, Sy Brand, and Tristan Brindle for taking the time out to
review this article.

[1]: http://elementsofprogramming.com
[2]: http://www.fm2gp.com
[3]: https://wg21.link/p1037
