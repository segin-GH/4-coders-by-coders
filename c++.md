# C++ Core Guidelines

## Abstract

This document is a set of guidelines for using C++ well. The aim of this document is to help people to use modern C++ effectively. By “modern C++” we mean effective use of the ISO C++ standard (currently C++20, but almost all of our recommendations also apply to C++17, C++14 and C++11). In other words, what would you like your code to look like in 5 years’ time, given that you can start now? In 10 years’ time?

### Express ideas directly in code
***Reason*** Compilers don’t read comments (or design documents) and neither do many programmers (consistently). What is expressed in code has defined semantics and can (in principle) be checked by compilers and other tools.

##### Example

```
class Date {
public:
    Month month() const;  // do
    int month();          // don't
    // ...
};

```

The first declaration of  `month`  is explicit about returning a  `Month`  and about not modifying the state of the  `Date`  object. The second version leaves the reader guessing and opens more possibilities for uncaught bugs.

***Example, bad*** This loop is a restricted form of  `std::find`:

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

***Example, good*** A much clearer expression of intent would be:

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
A well-designed library expresses intent (what is to be done, rather than just how something is being done) far better than direct use of language features.

A C++ programmer should know the basics of the standard library, and use it where appropriate. Any programmer should know the basics of the foundation libraries of the project being worked on, and use them appropriately. Any programmer using these guidelines should know the  [guidelines support library](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#S-gsl), and use it appropriately.

***Enforcement*** Very hard in general.

-   use  `const`  consistently (check if member functions modify their object; check if functions modify arguments passed by pointer or reference)
-   flag uses of casts (casts neuter the type system)
-   detect code that mimics the standard library (hard)

### Express intent

 ***Reason*** Unless the intent of some code is stated (e.g., in names or comments), it is impossible to tell whether the code does what it is supposed to do.

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
Sometimes better still, use a named algorithm. This example uses the  `for_each`  from the Ranges TS because it directly expresses the intent:

```
for_each(v, [](int x) { /* do something with the value of x */ });
for_each(par, v, [](int x) { /* do something with the value of x */ });

```
The last variant makes it clear that we are not interested in the order in which the elements of  `v`  are handled.

A programmer should be familiar with

-   [The guidelines support library](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#S-gsl)
-   [The ISO C++ Standard Library](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#S-stdlib)
-   Whatever foundation libraries are used for the current project(s)
##### Note

Alternative formulation: Say what should be done, rather than just how it should be done.

##### Note

Some language constructs express intent better than others.

##### Example

If two  `int`s are meant to be the coordinates of a 2D point, say so:

```
draw_line(int, int, int, int);  // obscure
draw_line(Point, Point);        // clearer
```
