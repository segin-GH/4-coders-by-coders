# C++ Core Guidelines

## Abstract

> ***The aim of this repo is to help C++ programmers to write simpler, more efficient, more maintainable code.(as per the c++17, c++20 std) .***

# P: Philosophy[](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#p-philosophy)

The rules in this section are very general. ( For most of the programmer's )

Philosophy rules summary:

-   [ Express ideas directly in code](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Rp-direct)
-   [ Express intent](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Rp-what)
-   [P.4: Ideally, a program should be statically type safe](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Rp-typesafe)
-   [P.5: Prefer compile-time checking to run-time checking](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Rp-compile-time)
-   [P.6: What cannot be checked at compile time should be checkable at run time](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Rp-run-time)
-   [P.7: Catch run-time errors early](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Rp-early)
-   [P.8: Don’t leak any resources](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Rp-leak)
-   [P.9: Don’t waste time or space](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Rp-waste)
-   [P.10: Prefer immutable data to mutable data](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Rp-mutable)
-   [P.11: Encapsulate messy constructs, rather than spreading through the code](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Rp-library)
-   [P.12: Use supporting tools as appropriate](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Rp-tools)
-   [P.13: Use support libraries as appropriate](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Rp-lib)

### Express Ideas directly in code
- No kidding programmers dont like to reading commnets. 
- Your code should explain itself through sensible functional naming. Your code should explain itself so comments are not necessary.
- The rule we use is only comment if you need to explain why you are doing something and never what you are doing. You should only have to explain why your code is doing what it's doing if it's something unusual or unexpected, like there are external behaviours that are not obviously involved.

##### Example, bad

This loop is a restricted form of  `std::find`:

```
void f(vector<string>& v)
{
    string val;
    cin >> val;
    // ...
    int index = -1;                    // bad, plus should use gsl::index
    for (int i = 0; i < v.size(); ++i) {
        if (v[i] == val) {
            index = i;
            break;
        }
    }
    // ...
}

```

##### Example, good

A much clearer expression of intent would be:

```
void f(vector<string>& v)
{
    string val;
    cin >> val;
    // ...
    auto p = find(begin(v), end(v), val);  // better
    // ...
}
```

##### Enforcement

Very hard in general.

-   use  `const`  consistently (check if member functions modify their object; check if functions modify arguments passed by pointer or reference)
-   flag uses of casts (casts neuter the type system)
-   detect code that mimics the standard library (hard)

##### Enforcement

***Use an up-to-date C++ compiler (currently C++20 or C++17) with a set of options that do not accept extensions.***

###  Express intent

##### Reason

Unless the intent of some code is stated (e.g., in names or comments), it is impossible to tell whether the code does what it is supposed to do.

##### Example

```
gsl::index i = 0;
while (i < v.size()) {
    // ... do something with v[i] ...
}

```

The intent of “just” looping over the elements of  `v`  is not expressed here. The implementation detail of an index is exposed (so that it might be misused), and  `i`  outlives the scope of the loop, which might or might not be intended. The reader cannot know from just this section of code.

Better:

```
for (const auto& x : v) { /* do something with the value of x */ }

```

Now, there is no explicit mention of the iteration mechanism, and the loop operates on a reference to  `const`  elements so that accidental modification cannot happen. If modification is desired, say so:

```
for (auto& x : v) { /* modify x */ }
```

##### Enforcement

Look for common patterns for which there are better alternatives

-   simple  `for`  loops vs. range-`for`  loops
-   `f(T*, int)`  interfaces vs.  `f(span<T>)`  interfaces
-   loop variables in too large a scope
-   naked  `new`  and  `delete`
-   functions with many parameters of built-in types
### Ideally, a program should be statically type safe[](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#p4-ideally-a-program-should-be-statically-type-safe)

##### Reason

Ideally, a program would be completely statically (compile-time) type safe. Unfortunately, that is not possible. Problem areas:

-   unions
-   casts
-   array decay
-   range errors
-   narrowing conversions

##### Note

These areas are sources of serious problems (e.g., crashes and security violations). We try to provide alternative techniques.

##### Enforcement

We can ban, restrain, or detect the individual problem categories separately, as required and feasible for individual programs. Always suggest an alternative. For example:

-   unions – use  `variant`  (in C++17)
-   casts – minimize their use; templates can help
-   array decay – use  `span`  (from the GSL)
-   range errors – use  `span`
-   narrowing conversions – minimize their use and use  `narrow`  or  `narrow_cast`  (from the GSL) where they are necessary

### Prefer compile-time checking to run-time checking[](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#p5-prefer-compile-time-checking-to-run-time-checking)

##### Reason

Code clarity and performance. You don’t need to write error handlers for errors caught at compile time.

##### Example

```
// Int is an alias used for integers
int bits = 0;         // don't: avoidable code
for (Int i = 1; i; i <<= 1)
    ++bits;
if (bits < 32)
    cerr << "Int too small\n";

```

This example fails to achieve what it is trying to achieve (because overflow is undefined) and should be replaced with a simple  `static_assert`:

```
// Int is an alias used for integers
static_assert(sizeof(Int) >= 4);    // do: compile-time check

```

Or better still just use the type system and replace  `Int`  with  `int32_t`.

#####

**Alternative formulation**: Don’t postpone to run time what can be done well at compile time.

##### Enforcement

-   Look for pointer arguments.
-   Look for run-time checks for range violations.


### What cannot be checked at compile time should be checkable at run time[](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#p6-what-cannot-be-checked-at-compile-time-should-be-checkable-at-run-time)

##### Reason

Leaving hard-to-detect errors in a program is asking for crashes and bad results.

##### Note

Ideally, we catch all errors (that are not errors in the programmer’s logic) at either compile time or run time. It is impossible to catch all errors at compile time and often not affordable to catch all remaining errors at run time. However, we should endeavor to write programs that in principle can be checked, given sufficient resources (analysis programs, run-time checks, machine resources, time).

##### Example, bad

```
// separately compiled, possibly dynamically loaded
extern void f(int* p);

void g(int n)
{
    // bad: the number of elements is not passed to f()
    f(new int[n]);
}

```

Here, a crucial bit of information (the number of elements) has been so thoroughly “obscured” that static analysis is probably rendered infeasible and dynamic checking can be very difficult when  `f()`  is part of an ABI so that we cannot “instrument” that pointer. We could embed helpful information into the free store, but that requires global changes to a system and maybe to the compiler. What we have here is a design that makes error detection very hard.

##### Example, bad

We can of course pass the number of elements along with the pointer:

```
// separately compiled, possibly dynamically loaded
extern void f2(int* p, int n);

void g2(int n)
{
    f2(new int[n], m);  // bad: a wrong number of elements can be passed to f()
}

```

Passing the number of elements as an argument is better (and far more common) than just passing the pointer and relying on some (unstated) convention for knowing or discovering the number of elements. However (as shown), a simple typo can introduce a serious error. The connection between the two arguments of  `f2()`  is conventional, rather than explicit.

Also, it is implicit that  `f2()`  is supposed to  `delete`  its argument (or did the caller make a second mistake?).

##### Example, bad

The standard library resource management pointers fail to pass the size when they point to an object:

```
// separately compiled, possibly dynamically loaded
// NB: this assumes the calling code is ABI-compatible, using a
// compatible C++ compiler and the same stdlib implementation
extern void f3(unique_ptr<int[]>, int n);

void g3(int n)
{
    f3(make_unique<int[]>(n), m);    // bad: pass ownership and size separately
}

```

##### Example

We need to pass the pointer and the number of elements as an integral object:

```
extern void f4(vector<int>&);   // separately compiled, possibly dynamically loaded
extern void f4(span<int>);      // separately compiled, possibly dynamically loaded
                                // NB: this assumes the calling code is ABI-compatible, using a
                                // compatible C++ compiler and the same stdlib implementation

void g3(int n)
{
    vector<int> v(n);
    f4(v);                     // pass a reference, retain ownership
    f4(span<int>{v});          // pass a view, retain ownership
}

```

This design carries the number of elements along as an integral part of an object, so that errors are unlikely and dynamic (run-time) checking is always feasible, if not always affordable.

#####