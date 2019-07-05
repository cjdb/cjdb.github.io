# A Quick Look at What P1754 Will Change (Part 1)

**Date published:* 2019-07-08

[P1754], or _Rename concepts to standard_case for C++20, while we still can_ is a proposal with a
single, abundantly clear goal outlined in its name: to change the naming convention for all standard
concepts from `PascalCase` to `snake_case`. Examples in P1754 are sadly lacking: it would have been
nice to see an algorithm or two with the differences displayed side-by-side. I was curious about
what it would look like, so I decided to contrast the two using the library that's benefited from
concepts the most: our algorithms library.

```cpp
// Status quo
template<InputIterator I, Sentinel<I> S, class T, class Proj = identity>
requires IndirectRelation<ranges::equal_to, projected<I, Proj>, T*>
constexpr I find(I first, S last, const T& value, Proj proj = {});

// P1754's suggestion
template<input_iterator I, sentinel_for<I> S, class T, class Proj = identity>
requires indirect_relation<ranges::equal_to, projected<I, Proj>, T*>
constexpr I find(I first, S last, const T& value, Proj proj = {});
```

```cpp
// Status quo
template<ForwardIterator I, Sentinel<I> S, class T, class Proj = identity,
         IndirectStrictWeakOrder<const T*, projected<I, Proj>> Comp = ranges::less>
constexpr I upper_bound(I first, S last, const T& value, Comp comp = {}, Proj proj = {});

// P1754's suggestion
template<forward_iterator I, sentinel_for<I> S, class T, class Proj = identity,
         indirect_strict_weak_order<const T*, projected<I, Proj>> Comp = ranges::less>
constexpr I upper_bound(I first, S last, const T& value, Comp comp = {}, Proj proj = {});
```

```cpp
// Status quo
template<InputIterator I1, Sentinel<I1> S1, RandomAccessIterator I2, Sentinel<I2> S2,
         class Comp = ranges::less, class Proj1 = identity, class Proj2 = identity>
requires IndirectlyCopyable<I1, I2> && Sortable<I2, Comp, Proj2> &&
         IndirectStrictWeakOrder<Comp, projected<I1, Proj1>, projected<I2, Proj2>>
constexpr I2 partial_sort_copy(I1 first, S1 last, I2 result_first, S2 result_last,
                               Comp comp = {}, Proj1 proj1 = {}, Proj2 proj2 = {});

// P1754's suggestion
template<input_iterator I1, sentinel_for<I1> S1, random_access_iterator I2, sentinel_for<I2> S2,
         class Comp = ranges::less, class Proj1 = identity, class Proj2 = identity>
requires indirect_copyable<I1, I2> && sortable<I2, Comp, Proj2> &&
         indirect_strict_weak_order<Comp, projected<I1, Proj1>, projected<I2, Proj2>>
constexpr I2 partial_sort_copy(I1 first, S1 last, I2 result_first, S2 result_last,
                               Comp comp = {}, Proj1 proj1 = {}, Proj2 proj2 = {});
```

The underscores serve as spaces in the names, which in my opinion, make the concepts significantly
easier to read -- particularly in the case of `partial_sort_copy`, which is quite a mouthful. I've
also applied the `snake_case` concepts to my numeric algorithms proposal, and it's helped to improve
that document's readability there too. I imagine production code using the `snake_case` concepts
will benefit even more than the spec.

## Concerns and the status quo

I've seen many people express distaste for the proposed change side due to the fact that 'we've
always named our concepts using `PascalCase`'. We haven't: we have a set of tables in the standard
library that outline requirements, and we name these requirements `EqualityComparable`, and so on.
While they do use `PascalCase`, those names are used to alias tables that appear only to aid in
understanding the standard prose, and don't represent any standard identifier.

Secondly, it's easy to come to the conclusion that because there's a named requirement called
`EqualityComparable` and a concept called `EqualityComparable`, of course the concept is just a way
for us to express our requirements in a compiler-checkable way. This is misguided: the concept
`EqualityComparable` goes way deeper than the named requirement `EqualityComparable`. It is left as
an exercise to the reader to distinguish the differences between the two. (One should note that the
named requirement has been rebranded as _Cpp17EqualityComparable_ to signify that it's different to
the concept, and that the font face has changed from `verbatim` to _italics_, which means that there
shouldn't be any more names in the standard spelt using `PascalCase`.)

Finally, our current templates look like this:

```
template<class I>
template<typename I>
template<auto N>
template<int N>
```

They do not look like this:

```
template<Class I>    // error: template type parameter declared using 'class' or 'typename'
template<Typename I> // error: template type parameter declared using 'class' or 'typename'
template<Auto N>     // error: template non-type parameter declared using 'auto' or an integral type
template<Int N>      // error: template non-type parameter declared using 'auto' or an integral type
```

In other words, my take on this, is that P1754 aims to _restore_ what we've always done, which is to
use a synonym of `typename` to declare a type parameter in `snake_case`, and we name our type
parameter in `PascalCase`. When I pointed this out to Herb, he mentioned that this is already
articulated in §1.1.3.

## Who's co-authoring it?

All of the major authors for both concepts and ranges have co-authored P1754, and are unanimous that
this should happen. Being the architects for these features, it is likely that they collectively and
individually have the most experience of anyone with these names. We should solemnly acknowledge
this experience, and at least _heavily_ experiment with their proposal before digging our heels in
and claiming that the aberration in the standard library should remain status quo.

## Conclusion

Some of the names, such as `has_common_reference` and `range_type` are a bit controversial, but can
be bikeshedded ('workshopped') in the committee meeting next week. If I get around to it, I'll throw
up a Part 2 concerning my thoughts about the less savoury names, but I want to make my position
unambiguously clear: I do not think that the current P1754 names should be a showstopper for letting
this proposal be standardised.

I understand that there's concern around renaming these concepts -- and P1754's authors are also
aware of this -- but I fear that the concern is misplaced. We should embrace the change that P1754
seeks to make, so that our code doesn't become too squashed, like `partial_sort_copy`.

At the very least, please write a non-trivial example with the new names, so that you've got
something to back you up when you say "I don't like it".

[P1754]: https://wg21.link/p1754
[alg.req]: https://timsong-cpp.github.io/cppwp/alg.req
[indirectcallable]: https://timsong-cpp.github.io/cppwp/indirectcallable
