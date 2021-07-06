# A Prime Opportunity for Ranges

It's been a while since my last blog. This isn't the blog that I promised, but it's still an
important one. If you haven't read [_Prepping Yourself to Conceptify Algorithms_][16] and
[_Transforming `std::find` into `std::ranges::find`_][17], please do go and read those: there are a
_lot_ of assumptions.

## Motivation: The limitations of vanilla C++98 through C++17

Take a look at the toy code demoed below, and try to work out what it is that I'm intending to do.

```cpp
// Vanilla C++17 code below
#include <cmath>
#include <iostream>

constexpr bool divides(int const x, int const y) noexcept { return y % x == 0; } // not to be confused with std::divides
constexpr bool is_even(int const x) noexcept { return divides(2, x); }
constexpr bool is_odd(int const x) noexcept { return not is_even(x); }
void handle_stream_state(std::istream& in, std::ios_base::iostate const validbits);

int main()
{
   for (auto input = 0; std::cin >> input;) {
      if (input == 2 or (input > 2 and is_odd(input))) {
         auto is_prime = true;
         switch (input) {
         case 2: [[fallthrough]];
         case 3:
            break;
         default:
            if (divides(3, input)) {
               is_prime = false;
               break;
            }

            for (auto n = 5; n * n < input; n += 6) {
                if (divides(n, input) or divides(n + 2, input)) {
                    is_prime = false;
                    break;
                }
            }
         }

         if (is_prime) {
            std::cout << input << '\n';
         }
      }
   }

   handle_stream_state(std::cin, std::ios_base::eofbit);
}
```

> * Read integers from the character input while the stream is good.
> * If `input` is odd (but not 1), or 2, then set `is_prime` to `true`.
> * If `input` is 2 or 3, then do nothing.
> * Otherwise, starting from 5, loop until `n` isn't less than the square-root of `input`, with a
>   step of 2, and exit the loop if `n` divides `input` (also set `is_prime` to `false`).
> * If `is_prime` is `true`, print out the input, plus a new line.
> * After the loop, check the state of `std::cin`.
> * Program terminates.

If this sounds like the first run-through of your analysis, then you've fallen victim to the
limitations of imperative programming. You might have eventually had an 'oh, I see, the program does
X', moment too. The above code doesn't describe _what_ is happening: rather it describes how the
computation is to be executed. A description of what is intended in the above code would read more
like this.

> Print all prime numbers read from the character input, stopping only when the EOF character is
> read. Report if an error came up.

The difference between the two summaries is that the former description never states what the
programmer wants to achieve, which makes it difficult to determine their intention, and painful to
determine whether or not their code is actually correct. This leads us to a question:

> Can we do better?

Certainly! There's two paths that we can take: refactor the code into functions, or model our
program in a different way. One scales; the other does not.

## Refactoring: A first attempt

```cpp
// Vanilla C++17 below
#include <cassert>
#include <cmath>
#include <iostream>
#include <stdexcept>

constexpr bool divides(int const x, int const y) noexcept { return y % x == 0; } // not to be confused with std::divides
constexpr bool is_even(int const x) noexcept { return divides(2, x); }
constexpr bool is_odd(int const x) noexcept { return not is_even(x); }

constexpr bool is_prime(int const input)
{
   if (input == 2 or (input > 2 and is_odd(input))) {
      auto is_prime = true;
      switch (input) {
      case 2: [[fallthrough]];
      case 3:
         break;
      default:
         if (divides(3, input)) {
            is_prime = false;
            break;
         }

         for (auto n = 5; n * n < input; n += 6) {
            if (divides(n, input) or divides(n + 2, input)) {
               is_prime = false;
               break;
            }
         }
      }

      if (is_prime) {
         return true;
      }
   }

   return false;
}

void handle_stream_state(std::istream& in, std::ios_base::iostate const validbits);

int main()
{
   try {
      std::cin.exceptions(std::ios_base::badbit);
      for (auto input = 0; std::cin >> input;) {
         if (is_prime(input)) {
            std::cout << input << '\n';
         }
      }

      handle_stream_state(std::cin, std::ios_base::eofbit);
   }
   catch (std::runtime_error const& e) {
      std::cerr << e.what() << '\n';
      return std::cin.bad() ? 1 : 2;
   }
}
```

`main` is a bit nicer to read, and it will be easier to deduce what the programmer's intention is.
That doesn't mean that the program is clearer overall: all we've done is shift the work into another
function (that might end up being banished to a library).

What if it were possible to describe what we're after without needing to tell the computer exactly
how to go about doing it? But first, an aside.

## Programming paradigms

A paradigm is a way of thinking, and a programming paradigm is a way to classify a programming
language based on how it enables us to think[[1][1]]. There are two general programming paradigms:

* [**Imperative programming**][2], which is stateful, and describes _how_ computations are to be
  executed in order to solve some problem. Languages such as C and Pascal are imperative languages.
  C++ lets us write imperative programs (although we don't need to do so).
* [**Declarative programming**][3], which describes _what_ the solution to a problem is, and how to
  achieve it. Declarative programming is split into many categories, including, but not strictly
  limited to:
    * [Markup languages][6], such as HTML, LaTeX, and Markdown, are a way to annotate a text
      document to describe what you would like to communicate. Nowhere, in a Markdown file, do you
      consider yourself explaining how your web browser should render **bold** text, yet it does.
      This is because you don't really care about how the software achieves it; only that it does.
    * [Functional programming][4], which focuses on computation as evaluating mathematical
      functions. State is avoided wherever possible, and immutable expressions are provided instead.
      Languages that primarily focus on functional composition include Haskell, Lisp, Scala, Kotlin,
      and Idris.
    * [Logic programming][5], which is based around [formal logic][7]. The building blocks for
      imperative programming and functional programming are respectively statements and expressions:
      for logic programs, the building blocks are facts and rules. Prolog is an example of a logic
      programming language.

## Back to the prime number program

Many C++ programs are written in an imperative format, but we don't need to do this. It's possible
to functionally compose C++ programs by relying on lightweight abstractions (which C++ has been
designed to do well). Right now, _how_ these are implemented is of less importance than getting out
a program that does _what_ we want (we'll look at performance later).

Vanilla C++17 doesn't let us achieve this, so we'll get some help from the third-party library,
[range-v3][20], which implements ranges and [range adaptors][8] in a fashion that is compatible with
C++14 and C++17.

```cpp
// Using C++17 with range-v3
#include <iostream>
#include <range/v3/all.hpp>

constexpr bool divides(int const x, int const y) noexcept { return y % x == 0; } // not to be confused with std::divides
constexpr bool is_even(int const x) noexcept { return divides(2, x); }
constexpr bool is_odd(int const x) noexcept { return not is_even(x); }

void handle_stream_state(std::istream& in, std::ios_base::iostate const validbits);

int main()
{
   namespace view = ranges::view;
   auto const divisible_by_2_or_3 = [](auto const x) noexcept {
      return (x != 2) and (x != 3) and (x < 2 or is_even(x) or divides(3, x)); };

   auto const non_prime = [divisible_by_2_or_3](auto const x) noexcept {
      auto divisors = view::iota(5)
                    | view::stride(6)
                    | view::take_while([x](auto const d) noexcept { return d * d < x; });
      return divisible_by_2_or_3(x) or
         ranges::any_of(divisors, [x](auto const d) noexcept {
            return divides(d, x) or divides(d + 2, x);
         });
   };

   auto primes = ranges::istream<int>(std::cin) | view::remove_if(non_prime);
   handle_stream_state(std::cin, std::ios_base::eofbit)
   ranges::copy(primes, ranges::ostream_iterator<int>(std::cout, "\n"));
}
```

If this looks like foreign magic to you _right now_, don't worry. First, note that we don't actually
describe how computation should look at any point in our program. There's no state at all: just a
handful of declarations and function calls.

### What is going on?

You might be scratching your head if you've not seen this style of programming. Let's try to change
that. To begin, let's translate the program to English.

> * Let `divisible_by_2_or_3` be a unary predicate that checks if its input is divisible by 2 or 3,
>   but isn't equal to 2 or 3.
> * Let `non_prime` be a unary predicate that checks if its input satisfies `divisible_by_2_or_3`,
>   or is divisible by any of the integers in the set `{5, 7, 11, 13, 17, 19, ...}`, up to the
>   square root of the input.
> * Let `primes` be a range of integers read from the character input, where any that satisfy
>   `non_prime` are removed from the range.
> * Copy `primes` to the character output, such that each integer is on a separate line.

Notice that we've four declarations and an expression: no explicitly managed state! This is a
high-level description of what we want our program to do. There's no mention of how we get integers
from the character input, nor how we remove integers that aren't prime. Similarly, our `non_prime`
is quite short when compared to the larger `is_prime` in the previous sections.

Note that state is still present, but it's behind the scenes. For example, we're querying the state
of `std::cin`, but not actually in charge of what happens.

### Range adaptors

According to [The One Ranges Proposal][9], which seeks to incorporate ranges into C++20, range
adaptors

> are utilities that transform a `Range` into a `View` with custom behaviours. These adaptors can be
> chained to create pipelines of range transformations that evaluate lazily as the resulting view is
> iterated.
>
> ...
>
> The bitwise or operator is overloaded for the purpose of creating adaptor chain pipelines. The
> adaptors also support function call syntax with equivalent semantics.

We've already covered what a `Range` is, but not a `View`. A `View` is a `Range` that has
constant-time copy, move, and assignment operators. This means that a pair of iterators in some
wrapper is a view, because iterators are cheaply copyable, movable, and assignable. A container is
not a view, because it copies each element when the container is copied, which is at least a linear
operation.

The term 'lazily' means that no computation is performed until it is requested. This is in contrast
to an eager operation, which is evaluated immediately. Eager operations are the motivators for lazy
ones.

We use five range adaptors:

<table>
   <tr>
      <th>Range adaptor</th>
      <th>Description</th>
   </tr>
   <tr>
      <td><code>view::iota</code></td>
      <td>
         Lazily creates a range of integers from the provided number to inifinity.
      </td>
   </tr>
   <tr>
      <td><code>view::remove_if</code></td>
      <td>
         Lazily discards an element in the range if it satisfies the given predicate.
      </td>
   </tr>
   <tr>
      <td><code>view::stride</code></td>
      <td>
         Lazily moves <i>n</i> elements away from the current element. Similar to
         <code>i += n</code>.
      </td>
   </tr>
   <tr>
      <td><code>view::take_while</code></td>
      <td>Lazily takes elements from a range while the given predicate is satisfied.</td>
   </tr>
   <tr>
      <td><code>ranges::istream</code></td>
      <td>
         Wraps an <code>istream</code> object inside a range adaptor so that it can be used like a
         range.
      </td>
   </tr>
</table>

### What's the bitwise or doing? Chaining???

```cpp
auto divisors = view::iota(5)
              | view::stride(6)
              | view::take_while([x](auto const d) noexcept { return d * d < x; });
// ...
auto primes = ranges::istream<int>(std::cin) | view::remove_if(non_prime);
```

If you're familiar with the Unix shell or Microsoft's PowerShell, then you'll be familiar with the
pipe operator. If you're not familiar with either, then in a nutshell, the pipe operator allows us
to feed data from one operation to another without need for any intermediate state. This is known as
a _composition operator_, and overloads the bitwise or operator.

The former reads

> Compose a range of integers called `divisors`, which starting from 5, contains every sixth
> integer, and continues while the square of the <i>n</i>th integer is less than `x`.

You can read the latter as

> Compose a range that reads integers from the character input and then removes integers if they
> satisfy `non_prime`.

### And how is that laziness evaluated? What are the eager operations?

Both `ranges::copy` and `ranges::all_of` are eager operations. The former has the same semantics as
`std::copy`, but takes a range: that is, it copies the contents of one range to another. The latter
works like `std::all_of`, but takes a range.

### Almost the same, but not quite

Just as it's possible to express an imperative program in different ways, it's also possible to
express our functionally-composed program differently.

```cpp
// Using C++17 with range-v3
#include <iostream>
#include <range/v3/all.hpp>

constexpr bool divides(int const x, int const y) noexcept { return y % x == 0; }; // not to be confused with std::divides
constexpr bool is_even(int const x) noexcept { return divides(2, x); };
constexpr bool is_odd(int const x) noexcept { return not is_even(x); };

int main()
{
   namespace view = ranges::view;
   auto const divisible_by_2_or_3 = [](auto const x) noexcept {
      return (x != 2) and (x != 3) and (x < 2 or is_even(x) or divides(3, x)); };

   auto const is_prime = [divisible_by_2_or_3](auto const x) noexcept {
      auto divisors = view::iota(5)
                    | view::stride(6)
                    | view::take_while([x](auto const d) noexcept { return d * d < x; });
      return not divisible_by_2_or_3(x) and
         ranges::none_of(divisors, [x](auto const d) noexcept {
            return divides(d, x) or divides(d + 2, x);
         });
   };

   auto primes = ranges::istream<int>(std::cin) | view::filter(is_prime);
   ranges::copy(primes, ranges::ostream_iterator<int>(std::cout, "\n"));
}
```

Not that different, eh? The difference between this program and the previous is that instead of
having a predicate `not_prime`, we have a predicate `is_prime`, which checks for the complement
condition. To compensate for this, we turn our `remove_if` into a `filter`, which only accepts
elements of ranges that _satisfy_ the predicate (so it's also a complement).

### I want the semantics, but I don't want the syntax

Alas, some people aren't a fan of using the composition operator. If you think it's weird, I
strongly recommend you use it for a while (say, for a full project), and the syntax will eventually
grow on you. If your concern is that some people might not know what the composition operator means,
please keep in mind that there's always something new to learn, and people pick things up quickly,
especially when they can see examples and try things out. After all, at one point, no one knew how
to write programs!

If you're still not convinced, there's a non-pipe syntax. I thoroughly discourage its use,
especially since we need to build our range in reverse order.

```cpp
// alternative 1
auto divisors = view::take_while(view::stride(view::iota(5), 6), [x](auto const d) noexcept { return d * d < x; });

// alternative 2
auto five_up = view::iota(5);
auto step_six = view::stride(five_up, 6);
auto divisors = view::take_while(step_six, [x](auto const d) noexcept { return d * d < x; });
```

## Performance

### Program size

I've put both one of the range-based programs and the vanilla C++ program into [Compiler Explorer][10]
and compiled using Clang 6, both with libstdc++ (the C++ Standard Library that ships with GCC) and
libc++ (the LLVM C++ Standard Library). Due to timeouts, GCC wasn't a viable option, but the results
can be checked [locally][11], if you're interested.

The compiler output on the left uses libstdc++; the output on the right uses libc++. On top, we have
the implementation using ranges, and the bottom is vanilla C++. All four implementations were
compiled using `-O3 -DNDEBUG`. It's easy to see that the code on the bottom is smaller than the code
on the top: about 55% of the size of the ranges solution when using libstdc++, and 80% of the size
when using libc++.

That's program size. What about runtimes?

### Run-time performance

As I'm not familiar with macrobenchmarking, we'll only be looking at microbenchmarking, using Google
Benchmark.

#### Making things fair

Reading input from the character input requires communication with the operating system, which can
be non-deterministic, so it's not exactly a fair endeavour to use `std::cin` for this section.
Instead, we'll provide a pre-loaded `istringstream` object that contains the one hundred million
integers in a random order (the seed remains consistent, so program re-runs will remain consistent).

Similarly, writing to any output stream requires communication with the OS, so we'll avoid using
both `std::cout` and `ostringstream`. Instead, we'll use a pre-loaded `vector`.

Finally, we'll need to do an operation on our output so that the optimiser doesn't see that we're
not using it and remove code that we need to benchmark. This is achieved by reading over the output
twice and reporting the number of primes and non-primes: that way, we can confirm that the output
is consistent across both programs.

You can find the changes in [this instance][12] of Compiler Explorer.

#### Benchmarking apparatus

Everything was compiled and run using an Intel Core i7-8700 CPU @ 3.2GHz on this [Docker image][13]
with six cores, 26'368 MB memory (clocked at 2133MHz), and 512 MB swap space allocated to the
container. The operating system was Windows 10.0.17134 Build 17134.

The exact commands to the Ninja build system included:

```
/usr/bin/clang++ \
   -stdlib=libc++ \
   -O3 \
   -DNDEBUG \
   -flto=thin \
   -stdlib=libc++ \
   -lc++abi \
   benchmark/CMakeFiles/amcpp.gp2.is_prime_vanilla.dir/is_prime_vanilla.cpp.o \
   -o benchmark/amcpp.gp2.is_prime_vanilla \
   /root/.conan/data/google-benchmark/1.4.1/mpusz/stable/package/468fddffcd89d717847492001b37f7099807a722/lib/libbenchmark.a \
   -lpthread \
   /usr/lib/x86_64-linux-gnu/librt.so
```

#### Method

The programs were run in an alternating fashion, with the ranges implementation run first, then the
vanilla implementation.

#### Results

The raw output of the results can be found on [Pastebin][14]. A summary is tabulated below:

<table>
   <tr>
      <th>Run</th>
      <th>Vanilla C++ CPU (sec)</th>
      <th>Ranges-based C++ CPU (sec)</th>
   </tr>
   <tr>
      <td><center>1</center></td>
      <td><center>54.809</center></td>
      <td><center>53.246</center></td>
   </tr>
   <tr>
      <td><center>2</center></td>
      <td><center>55.245</center></td>
      <td><center>52.720</center></td>
   </tr>
   <tr>
      <td><center>3</center></td>
      <td><center>54.990</center></td>
      <td><center>53.390</center></td>
   </tr>
   <tr>
      <td><center>4</center></td>
      <td><center>54.766</center></td>
      <td><center>53.054</center></td>
   </tr>
   <tr>
      <th>Average</th>
      <th>54.953</th>
      <th>53.103</th>
   </tr>
</table>

The vanilla solution runs at about 1.03x as long as the ranges-based implementation, and this scaled
when I took my samples, starting from 5'000'000 integers all the way to the 100'000'000 recorded
here.

## Where can I learn more?

I'll be hosting a two-day post-conference class at CppCon 2018, titled [Generic Programming 2.0 with
Concepts and Ranges][21]. Ranges aren't the only topics we'll cover: there will be some exploration
into what makes a concept, and the conditions where you can cultivate one, but we do spend a fair
amount of time looking into ranges and even look at how a few range adaptors are implemented. You
can find more infromation in the aforementioned link, or in this [reddit post][22].

[Eric Niebler][25] also delivered ['Ranges for the Standard Library'][26] at CppCon 2015, and
['Introducing the Ranges TS'][27] at code::dive 2017. Eric is the code owner for the range-v3
library that was used in our examples, and you can play around with his tests and examples in the
range-v3 repository to learn how things are used.

Finally, there's a discussion channel over on cpplang.slack.com called #ranges. If you're interested
in chatting about this stuff, it would be worth heading over there.

## Conclusion

In summary, a functionally-composed solution lets us describe what our intentions are, rather than
how our code ought to be executed (that's very much an implementation detail). Additionally, the
functionally-composed code consistently takes ever-so-slightly less time to run! Note that this
boost is without compiler support, so when ranges finally hit the Standard Library in C++20, they
may be even better-suited to the task than they are now. Finally, other research has demonstrated
that [ranges lend themselves to parallelism quite well][15], which means that we can get even more
performance gains than previously.

Of course, this article only explores determining if an integer is prime or not prime, and doesn't
claim that using ranges will _always_ be a better choice for performance than imperative code. All
of these point toward using ranges, but if performance is critical, it is important to benchmark
your code. For non-performance-critical sections of code, I strongly encourage that you use ranges
to functionally compose your data wherever possible.

### Discussion

If you'd like to offer any feedback, please head to this specific [GitHub issue][18], as I haven't
yet had the time to embed something such as Disqus into the blog. Please be aware of the [code of
conduct][19] before posting.

## Acknowledgements

I'd like to offer thanks to [Corentin Jabot][23], Daniel Soutar,
[Gordon Brown][24], [JC van Winkel][29], Kostas Kyrimis, and [Tristan Brindle][28] for their
feedback on this material.

[1]: https://en.wikipedia.org/wiki/Programming_paradigm
[2]: https://en.wikipedia.org/wiki/Imperative_programming
[3]: https://en.wikipedia.org/wiki/Declarative_programming
[4]: https://en.wikipedia.org/wiki/Functional_programming
[5]: https://en.wikipedia.org/wiki/Logic_programming
[6]: https://en.wikipedia.org/wiki/Markup_language
[7]: https://en.wikipedia.org/wiki/Mathematical_logic
[8]: #range-adaptors
[9]: https://wg21.link/p0896
[10]: https://godbolt.org/z/wNzT0A
[11]: https://github.com/mattgodbolt/compiler-explorer
[12]: https://godbolt.org/z/Ut-Qq_
[13]: https://hub.docker.com/r/cjdb/amcpp-stdlib/
[14]: https://pastebin.com/FtN6Jktt
[15]: https://wg21.link/p0836
[16]: https://www.cjdb.com.au/blog/2018/05/15/prepping-yourself-to-conceptify-algorithms
[17]: https://www.cjdb.com.au/transforming-std-find-into-std-ranges-find
[18]: https://github.com/cjdb/cjdb.github.io/issues/6
[19]: https://github.com/cjdb/cjdb.github.io/blob/master/CODE_OF_CONDUCT.md
[20]: https://github.com/ericniebler/range-v3
[21]: https://cppcon.org/generic-programming-2.0-with-concepts-and-ranges/
[22]: https://www.reddit.com/r/cpp/comments/9e4ci4/cppcon_generic_programming_20_with_concepts_and/
[23]: https://cor3ntin.github.io/
[24]: http://aerialmantis.co.uk/
[25]: http://ericniebler.com/
[26]: https://www.youtube.com/watch?v=mFUXNMfaciE
[27]: https://www.youtube.com/watch?v=LNXkPh3Z418
[28]: https://github.com/tcbrindle
[29]: https://www.linkedin.com/in/jc004/
