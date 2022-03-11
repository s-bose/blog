---
title: A roadmap to CPP
date: 2022-02-12
keywords:
- cpp
clearReading: true
thumbnailImage: cpp.png
thumbnailImagePosition: left
metaAlignment: center
comments: false
meta: false
actions: false
---



<!-- more -->
A step-by-step guide for learning Modern C++ in 2022
<!-- excerpt -->


<!-- toc -->

## Introduction

C and more specifically, C++, have continued to be the running joke among the programming community, due to them being notoriously difficult to grasp, from the esoteric and weird syntax, the dreadful pointers and memory management (although that has gotten better), to templates and what not. Indeed they are not everyone's cup of tea, and while it is true that languages like [Rust](https://www.rust-lang.org/) are slowly starting to bridge the gap between code readability and code efficiency, C++ is still relevant and has a long way ahead, in areas such as Embedded Systems, Operating Systems, Kernel, or Driver development, Game development, Machine Learning, or basically any area where efficient and high-performant computing is required. Is it necessary to learn, or master C++ in 2022? Absolutely not, most of the modern languages provide a more than decent performance in most use-cases, and unless there is a specific need for performance, and other alternatives are for some reason not viable, you would be well-off without touching C++. But learning it, and learning it well is indeed a nice thing to have. Not only would you gain a clear understanding of the low-level internal workings, such as the memory management, the entire compilation process and the absolutely impressive workhorse that the C++ compiler is, you open up a new door to creativity, where you can start writing kernel modules, high-performant libraries, build your own compiler or interpreter and make your own programming language (and also the fact that you can flaunt your C++ superiority among your friends). The possibilities are endless. I've spent a good 4-5 months studying advanced C++, and even though I still don't consider myself to be proficient in C++, I am fairly well-versed with the language at this stage. I am writing this guide to hopefully, not only help you but also help myself get better at C++ in the days to come. The guide is nowhere near perfect and I'm sure I'll be missing a few important references here and there, but it has worked out for me so far, and I only hope it works out for you as well.



## Environments & Prerequisites

I haven't personally used MSVC C++ for Windows, so I can't really form an opinion on how viable it is to learn C++ on Windows. However, with the addition of WSL you can use standard GNU G++ (gcc/g++) compilers on Windows. Personally, I use [Pop!OS](https://pop.system76.com/), an Ubuntu-based Linux distribution as my daily driver which comes with the default GNU G++ Compiler. I use version 11.2.0.

In order to get the most out of it, we need to have a compiler that supports the features from c++2020 standard.
[This table](https://en.cppreference.com/w/cpp/compiler_support/20) provides the list of all available compiler versions that support some or all features of C++20. Preferably, you should use the latest g++ version available with your Linux distro (or clang++ or MSVC).

Here is a list of prerequisites that I recommend:

|Item|Description|Version|
|:---|:----------|:------|
|`gcc/g++`|Compiler|`>=11.0`|
|`make/cmake`|Build utilities|`>=4.3, >=3.18`|
|`vscode/vim/sublime-text`|Code Editor|`Any`|
|`Rubber Duck`|Debugging|`Any`|


## Test the waters with C


As Linus Torvalds said, 
> "[Nothing better than C...](https://www.youtube.com/watch?v=CYvJPra7Ebk)"

I do believe that in order to fully grasp C++, if you are coming from a completely different environment such as Python, or Javascript and haven't touched a penny of C/C++ in your life, then C should be the first stop before you venture further. Although C does not provide a lot of features that C++, or any of those obvious features that we take for granted (in that sense, C is truly a low-level language), it is still the most effective and intensive way to learn the internal nitty-gritties of the compilation process. 
The best book to accompany you in this scenario is [The C Programming Language](https://www.amazon.com/Programming-Language-2nd-Brian-Kernighan/dp/0131103628) by the creators of the language themselves.
Its a relatively short book, and paired with all the examples and exercises and a bit of practice you should be fairly comfortable with the language environment in a few weeks. Do keep in mind that except from syntactical similarities and similar compilation processes, C++ has turned into a completely different language, even though it initially started off as a superset of C.
If you're interested in learning C++, I would suggest to not spend too much time mastering C but instead focus on key areas which only help you accelerate the learning process of C++.

### Key Topics

* C/C++ Compilation Process
* Primitive Datatypes & Bit Manipulation
* Pointers & Memory Management (Allocation, Dellocation)
* Structs* and Function Pointers

**Note***: Definition of a `struct` in C and C++ is different.


## Introduction to C++

I have been learning C++ from [this course](http://www.icce.rug.nl/edu/) offered by Dr. Frank B. Brokken and Prof. Jurjen Bokma of University of Groningen, and it is one of the most intensive and in-depth courses I have ever participated in. The course was divided into three parts, each taking around 2 months to complete. I would highly recommend to check out the course for further information. 
Apart from that,I've thoroughly followed the book [CPP Annotations](https://fbb-git.github.io/cppannotations/cppannotations/html/index.html), written by Dr.Frank B. Brokken.

I would suggest [CPP Annotations](https://fbb-git.github.io/cppannotations/cppannotations/html/index.html) to be the primary literature study, moving forward with C++. 
Alternatively, [The C++ Programming Language](https://www.amazon.com/C-Programming-Language-4th/dp/0321563840) by Bjarne Stroustrup, creator of C++ is also a good choice.
And last but not least, the [CppReference](https://en.cppreference.com/w/cpp/language) would be your bread-and-butter.


I will provide the full list of bibliography at the end. What we will be mainly focusing on are the concepts and strategies in your learning.


Let's take a look at a Hello World program.

```cpp
#include <iostream>

using namespace std;

int main()
{
    cout << "Hello World" << endl;
}
```

The first thing to notice about C++ is how similar yet different it is from C. The compiler is g++, not gcc. The `#include` header is `<iostream>`, not `<stdio.h>`, instead of using an obvious `print()` function we are seemingly bit-shifting `cout`. These are some of the quirkiness of C++ that you need to get accustomed to if you are coming out of a long exposure of C.

In the following sections I will talk about topics that you would need to focus on that gets progressively complex to provide a streamlined approach in studying C++.


### Namespaces, Streams & Standard Library

C++ has namespaces, a way to group together classes, functions and variable definitions from different sources under a single umbrella. The namespace `std` contains all the tools that are in the C++ standard. It is the named scope for all standard implementations of pretty much everything you would need for general-purpose programming, scattered across a number of different headers such as `<iostream>` (and their corresponding source files).
The header file provides an interface, a catalog of the things provided by a library, which are then implemented in separate source files. This header file oriented program structure should be recognized in great detail since this clear distinction between interface and implementation is at the heart of good C++ program design.
The object `cout` is a type of `stream`, which is a new concept in C++. Instead of having a specific function that prints to the console, the stream-based IO is more generic in the sense that we can write polymorphic code that can write to any `stream`, not just the console, alongside the fact that we can achieve almost the same task of formatted-strings with `cout`, as well as being [type-safe](https://stackoverflow.com/questions/17789396/how-cout-is-more-typesafe-than-printf).
You should spend some time studying the different types of `stream`s and their class-hierarchy since most of the IO happens over streams.

### Memory Management, Pointers & References

C++, much like C, has manual garbage collection*, using `new` and `delete` and their variants. The key difference is that we don't have to allocate raw memory and provide the size of the memory block. `new` and `delete` are more type-safe. Learning this new memory management is crucial in situations where you have to manage your own data. Consequently, pointers also play an important part. References are new to C++, which can be thought of as named aliases to another variable.

**Note***: There is indeed a way to do automatic garbage allocation, known as RAII (Resource Allocation Is Initialization), it is a principle that is used to build most of the standard container classes, (and you can, too), which states that an object that is initialized with some allocated resource is in charge of that resource, and will relinquish the resource as soon as it goes out of scope (aka, destroyed).


### Object Oriented Programming

The major advantage of C++ is its adaptation of Object-oriented programming. But unlike `Java`, it is not mandatory to make pretty much everything about your program a class. As mentioned earlier, C++ handles `struct` differently. Apart from the syntactical differences, `struct`s are just the same as `class`, except all the data members in `struct` are by-default `public`, whereas in `class` they are `private`. These are Access-specifiers that let us know the scope of accessibility of a member in a class.
The usual concepts of encapsulation and data hiding 