# C programming techniques and Neovim-specific guidance

## Unsigned or signed? Integer overflow/underflow

There are a few very important things to keep in mind while choosing between signed and unsigned integral types:

Firstly, **unsigned overflow is defined**, while **signed overflow is not**. This is an unfortunate historical oversight stemming from a time where it wasn't sure what [representation a signed integer would have](http://stackoverflow.com/questions/18195715/why-is-unsigned-integer-overflow-defined-behavior-but-signed-integer-overflow-is). The C standard decided that it shouldn't/couldn't specify what would happen when a signed integer overflows, because each representation would have different behaviour. In modern times, signed integers are always represented in [two's complement](http://en.wikipedia.org/wiki/Two's_complement) form, which has many advantages. 

> An interesting thing to note is that it is possible to force gcc and clang to view signed overflow as defined (wraparound, like unsigned), by [passing the -fwrapv flag](http://stackoverflow.com/a/4712784/558819). This is, unfortunately, non-standard.

What does this mean, defined vs. undefined? If an unsigned integer overflows, it wraps around back to zero (it's modulo addition). Yet, if a signed integer overflows, $deity only knows what will happen. More specifically: we _know_ what would happen if a two's complement signed integer would overflow, but [the compiler can do whatever it wants, because the standard says it is undefined](http://stackoverflow.com/a/18195756/558819). As a consequence, an optimizing compiler will often assume that a signed integer _cannot_ overflow and [optimize out some if-branches or comparisons](https://cryptoservices.github.io/fde/2018/11/30/undefined-behavior.html). This behavior can cause loops to run forever. Note that if the conditions are not written carefully, even the well-defined wraparound overflow of unsigned integers can cause non-terminating loops, `(U)INT_MAX/MIN` are your friends.

Thus it would seem that unsigned arithmetic is superior, because it has defined over- and underflow. But that's not always true. There's a good reason why many languages (like Java) don't expose unsigned types: they can cause difficult to spot errors. The most common form of under/overflow is **underflow in unsigned arithmetic**. Subtracting 1 from `unsigned int num = 0;` will make it wrap around to `UINT_MAX`. [This is much more common than one would think](http://www.soundsoftware.ac.uk/c-pitfall-unsigned). For this reason alone, it is usually **much** safer to use a plain `int` as a loop counter instead of `uint32_t`/`size_t`/... or another unsigned type. Even seasoned programmers find it difficult to avoid writing unsigned code that doesn't underflow in some cases.

**Problem**: correct signed code is easier to write, but you have to use casts when comparing to `size_t` (which happens often, as it is the return type of `sizeof`, `strlen` and many others). Casts are ugly and should be avoided if at all possible. But we cannot avoid them everywhere. Sometimes, a trade-off has to be made. See [previous `-Wconversion` PRs](https://github.com/neovim/neovim/pulls?q=is%3Apr+is%3Aclosed+wconversion) for examples.

**Conclusion**: 

- if there is any chance of underflow or the loop in question is small (definitely less than `2^31` items), use signed arithmetic and a guard before the loop.
- if there is any chance of overflow, use unsigned arithmetic and possibly guards.
- if there is a chance of both underflow and overflow, be extremely careful and paranoid (guards/asserts).

## Tools and articles

- [Secure C Coding](https://wiki.sei.cmu.edu/confluence/display/c/SEI+CERT+C+Coding+Standard)
- [Modern source-to-source transformation with Clang and libTooling](http://eli.thegreenplace.net/2014/05/01/modern-source-to-source-transformation-with-clang-and-libtooling/)
- [How Should You Write a Fast Integer Overflow Check?](http://blog.regehr.org/archives/1139)
- [Stubborn and ignorant use of int where size_t is needed](http://ewontfix.com/9/)

## Undefined behavior

- https://cryptoservices.github.io/fde/2018/11/30/undefined-behavior.html

# C type refactoring

## Introduction
In many different tasks, the need arises to refactor integer types, mainly to:
- avoid errors because of mixing of signed/unsigned types and implicit conversions.
- emphasize size semantics where appropriate.
- conform with system functions returning `size_t` values without the need of casting. 
- ensure no unneeded limits are imposed on sizes of things we can operate on.
- avoid possible data truncation because of explicit casting.
- etc.

It's important to agree on how this kind of refactoring should be done.
However: 

- This is a difficult thing to get completely right. No hard-and-fast set of rules will match every possible situation.
- **This set of guidelines intends to be a framework establishing principles** to help new people to confront these problems, and for more experienced people to have a consensus so that code is homogeneous. 
- **It does not intend to be a set of mechanical rules to be applied without further thinking, or to be dogmatic about**.
- Every particular case should be carefully analyzed before taking actions on it.

That said, here goes the advice:

## Int types
- `int` with size semantics:
    * signedness conversion easy --> `size_t` <br/>
       (check for signedness conversion usual problems).
    * signedness conversion difficult --> `ssize` <br/>
       (for example, complicated code involving subtractions) 
- `int` without size semantics --> `int`

## Special cases
In spite of the general advice above, there are some cases where we prefer more tight typing.

### Struct fields
We should try to keep structs small, if possible. To that end:
- Use fixed width types (`int32_t`, `uint32_t`, etc.), if possible. This is:
- Do that only if you can be sure that the specified width will always be enough. So:
- Please don't impose arbitrary/unneeded limits on fields. 
- For example: If a field has clear size semantics and there's no particular reason to impose a restriction on it, use `size_t`/`ssize`.

### External interfaces
- For functions interfacing other processes over a transport/serialization mechanism, fixed-width types are preferred. For example, in `msgpack_rpc.h`:

```c
bool msgpack_rpc_integer_result(uint32_t result,
                                msgpack_object *req,
                                msgpack_packer *res);
```

- For functions part of a public API, native types are preferred. For example, in a hypothetical `libneovim.h`:

```c
int neovim_get_current_buffer(void);
```

## Type cascading
Once you have some input variable you have to deal with (be it a struct field, a function parameter, or even a global), the issue arises whether to cascade that type in code dealing with it, or using a wider type if considered better for some reason. Typical example is, once you have a fixed-width struct field, code dealing with it (function variables/parameters) should also use fixed-width types, or could types we widened to some other enclosing type? In principle, we say:
- If access to input variable is read-only, then it's safe to use wider types if some other reason makes them preferable, as you will be only upcasting the value.
- If access to input variable can be read/write, then intermediate variables/params should try to maintain input variable's type as much as possible. If not, you will end up with a downcast somewhere that will require an assert/some other kind of guard/error handling.

## Loops
We find [this](http://gustedt.wordpress.com/2013/07/15/a-praise-of-size_t-and-other-unsigned-types/) pretty convincing, taking great care not to incurr in errors described [here](http://www.eschertech.com/articles/items/art100407.html). From that, we conclude:

In loops, we have a *counter* variable and a *limit* expression (*condition* being a comparison between *counter* and *limit*). Issues can arise mainly because of implicit conversions in *limit* expression, as well as mixing different-signedness types in *condition* (i.e., when *counter* and *limit* have types of different signedness). To avoid/reduce implicit conversion and type-signedness-mixing problems:

- If possible, try to avoid different-signedness types in variables within *limit* expression (many errors are because implicit conversion from signed type to unsigned one).
- In principle, *limit* expression's type determines *counter*'s type. If *limit* expression is `size_t`, so is counter. If *limit* expression is `ssize`, so is counter. And so on.
- If resulting type of *counter* and *limit* is unsigned:
    * Check *limit* expression  for frontier values (e.g., when size is zero).
    * Avoid *condition* using substractions (unless guarded so that it can be proved for result to always be positive). Prefer equivalent condition using additions on the other side.
- As an optimization, you could use plain `int` instead of `ssize`, or `unsigned int` instead of `size_t`, but only if you are sure that those types will be enough always. Please try not to impose arbitrary/unneeded limits.

