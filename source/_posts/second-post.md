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

| Item                      | Description     | Version         |
|:------------------------- |:--------------- |:--------------- |
| `gcc/g++`                 | Compiler        | `>=11.0`        |
| `make/cmake`              | Build utilities | `>=4.3, >=3.18` |
| `vscode/vim/sublime-text` | Code Editor     | `Any`           |
| `Rubber Duck`             | Debugging       | `Any`           |

## Test the waters with C

As Linus Torvalds said, 

> "[Nothing better than C...](https://www.youtube.com/watch?v=CYvJPra7Ebk)"

I do believe that in order to fully grasp C++, if you are coming from a completely different environment such as Python, or JavaScript and haven't touched a penny of C/C++ in your life, then C should be the first stop before you venture further. Although C does not provide a lot of features that C++, or any of those obvious features that we take for granted, it is still the most effective and intensive way to learn the internal nitty-gritties of the compilation process. 
The best book to accompany you in this scenario is [The C Programming Language](https://www.amazon.com/Programming-Language-2nd-Brian-Kernighan/dp/0131103628) by the creators of the language themselves.
Its a relatively short book, you should be fairly comfortable with the language environment in a few weeks.
If you're interested in learning C++, I would suggest to not spend too much time mastering C but instead focus on the following key areas which only help you accelerate the learning process of C++.

### Key Topics

* C/C++ Compilation Process
* Primitive Datatypes & Bit Manipulation
* Pointers & Memory Management (Allocation, Dellocation)
* Structs and Function Pointers


## Chapter 1: Introduction to C++

I have been learning C++ from [this course](http://www.icce.rug.nl/edu/) offered by Dr. Frank B. Brokken and Prof. Jurjen Bokma of University of Groningen, and it is one of the most intensive and in-depth courses I have ever participated in. The course is divided into three parts, each taking around 2 months to complete. I would highly recommend to check out the course for further information. 
Apart from that,I've thoroughly followed the book [CPP Annotations](https://fbb-git.github.io/cppannotations/cppannotations/html/index.html), written by Dr.Frank B. Brokken.

I would suggest [CPP Annotations](https://fbb-git.github.io/cppannotations/cppannotations/html/index.html) to be the primary literature study, moving forward with C++. 
Alternatively, [The C++ Programming Language](https://www.amazon.com/C-Programming-Language-4th/dp/0321563840) by Bjarne Stroustrup, creator of C++ is also a good choice.
And last but not least, the [CppReference](https://en.cppreference.com/w/cpp/language) is your friend.

I will provide the full list of bibliography at the end. What we will be mainly focusing on are the concepts and strategies in your learning.


In the following sections I will talk about topics that you would need to focus on that gets progressively complex to provide a streamlined approach in studying C++.

**Book Suggestions**
* [The C++ Programming Language](https://www.amazon.com/C-Programming-Language-4th/dp/0321563840) by Bjarne Stroustrup
* [Effective Modern C++](https://www.amazon.com/dp/1491903996/?_encoding=UTF8&pd_rd_w=qdkPa&pf_rd_p=9aa30bae-d685-4626-879d-c38f81e830a3&pf_rd_r=QFN3PY1WWZ9YMA0PEK4D&pd_rd_r=cb3e5711-cb4c-4f9b-99f2-647a071d3986&pd_rd_wg=7bcAU&ref_=bd_tags_dp_rec) by Scott Meyers.

### Namespaces, Streams & Standard Library

C++ has namespaces, a way to group together classes, functions and variable definitions from different sources under a single umbrella. The namespace `std` contains all the tools that are in the C++ standard. It is the named scope for all standard implementations of pretty much everything you would need for general-purpose programming, scattered across a number of different headers such as `<iostream>` (and their corresponding source files).
A header file provides an interface, a catalog of the things provided by a library, which are then implemented in separate source files. This header file oriented program structure should be recognized in great detail since this clear distinction between interface and implementation is at the heart of good C++ program design.
Objects like `cout`, or `cin` is a type of `stream`, which is a new concept in C++. Instead of having a specific function that prints to the console, the stream-based IO is more generic in the sense that we can write polymorphic code that can write to any `stream`, not just the console, alongside the fact that we can achieve almost the same task of formatted-strings with `cout`, as well as being [type-safe](https://stackoverflow.com/questions/17789396/how-cout-is-more-typesafe-than-printf).
You should spend some time studying the different types of `stream`s and their class-hierarchy since most of the IO happens over streams.

### Memory Management, Pointers & References

C++, much like C, has manual garbage collection*, using `new` and `delete` and their variants. The key difference is that we don't have to allocate raw memory and provide the size of the memory block. `new` and `delete` are more type-safe. Learning this new memory management is crucial in situations where you have to manage your own data. Consequently, pointers also play an important part. References are new to C++, which can be thought of as named aliases to another variable.

**Note***: There is a way to do automatic garbage allocation, known as RAII (Resource Allocation Is Initialization), it is a principle that is used to build most of the standard container classes, (and you can, too), which states that an object that is initialized with some allocated resource is in charge of that resource, and will relinquish the resource as soon as it goes out of scope (aka, destroyed).

### Types, Values, Macros & Smart Pointers

You need to understand primitive types, pointer-types, references, and rvalue references, their sizes in memory, their lifetimes and `cv-qualification (const-volatile)`.

C++ also has a number of value categories, such as:
  
  ```
          glvalue    rvalue
           /     \   /   \
        lvalue  xvalue   rvalue        
  ```
  
These are important to remember when considering copying and moving data.

Typecasting is required from time to time. Instead of using C-style castings, use `static_cast`, `reinterpret_cast`(dangerous), or `dynamic_cast`.

Avoid Macros, as they are not type-safe. Instead, use `constexpr`.
Use smart pointers, i.e, RAII-adhering wrapper classes such as `unique_ptr` or `shared_ptr`, whenever possible. 

    

### Object Oriented Programming

The major advantage of C++ is its adaptation of Object-oriented programming.
Here are some of the things you need to remember when working with OOP in C++.

* Encapsulation & data hiding is achieved via access specifiers such as `public`, `private`, and `protected`.
* Have a clear separation between a class's public interface & private (or sometimes protected) implementations.
* If you have to expose a data-member as public, if class-specific, make it `static` , else, provide public-getters to `private` data, don't expose data members directly.
* Function overloading/overriding is not Polymorphism, and it is achieved in compile-time by creating unique function signatures.
* C++ has **Inheritance**, (and multiple inheritance). Inheriting from a Base class can be done through all three access-specifiers, although their behaviors vastly differ.
* **Composition** is another useful concept, and should be preferred over Inheritance to reduce code coupling.
* **Polymorphism** can be static (compile-time, using CRTP), or dynamic (run-time, using Vtables and Vptrs). The most common type of polymorphism, i.e, run-time is achieved via `virtual` method overriding, and then substituting a Base object with a Derived one to "polymorphize" the method, commonly known as Liskov Substitution Principle (LSP).
* **Abstract Base Class (ABC)** are used to further enforce dynamic polymorphism.
* Learn about `friend`s, and free functions, (such as `operator<<`)


## Chapter 2: Advanced Topics
This section deals with some intermediate topics in C++.

**Book Suggestions**
* [Modern C++ Design: Generic Programming and Design Patterns](https://www.amazon.com/Modern-Design-Generic-Programming-Patterns/dp/0201704315) by Andrei Alexandrescu.

* [C++ Coding Standards](https://www.amazon.com/Coding-Standards-Rules-Guidelines-Practices/dp/0321113586) by Andrei Alexandrescu.

* [Exceptional C++](https://www.amazon.com/Exceptional-Engineering-Programming-Problems-Solutions/dp/0201615622/ref=sr_1_1?crid=PERBM6MENIUR&keywords=exceptional+c%2B%2B&qid=1647878499&s=books&sprefix=exceptional+c%2B%2Cstripbooks-intl-ship%2C408&sr=1-1) & [More Exceptional C++](https://www.amazon.com/More-Exceptional-Engineering-Programming-Solutions/dp/020170434X/ref=sr_1_1?crid=1553V6BH6EB9N&keywords=more+exceptional+c%2B%2B&qid=1647878523&s=books&sprefix=morexceptional+c%2B%2B%2Cstripbooks-intl-ship%2C343&sr=1-1) by Herb Sutter.

* [C++ Concurrency in Action](https://www.amazon.com/C-Concurrency-Action-Practical-Multithreading/dp/1933988770), by Anthony Williams.

### Standard Containers & Generic Algorithms 

C++ `std::` provides a wide-range of tools to fit pretty much every need.
One of the most important are the containers, a bunch of well-defined standard data structure implementations such as `map`, `deque`, `vector`, etc. They are generic and provides a flexible and memory-safe interface for working with data.

Closely related are the generic algorithms, these are utility functions that work on any container type, such as `sort`, `copy`, `accumulate`, or `for_each`, which are preferable alternatives to reinventing wheel, and also reduces clutter in your project.


### Exceptions

Writing exception-safe code is not easy. In a language like C++ it is even more important, otherwise you risk getting extremely long unintelligible error messages, and memory leak.
There are certain functions in C++ that won't ever throw exceptions, and there are some that might. You need to know which part of your code is exception-prone and handle it properly. 
Exception handling is particularly crucial when you're working with some form of resource which might remain in an inconsistent state once the exception occurs.
Writing your own exception class is also encouraged for better documentation and readability.

### Multithreading (Optional)

Multithreading is not everyone's cup of tea, due to it being difficult to wrap your head around, and the extra performance gain is not really that big of a deal given today's hardware. However, if you want to get your hands dirty with multithreading, some of the useful libraries in C++ std are `thread`, `mutex`, and `chrono`. You have to spend some time learning about the inner workings of a multithreaded application, the lifecycle of a thread, hooks that you can use on the current thread, and preserving data integrity by using mutex locks.
This opens up a new door to asynchronous programming using `async`, `future` and `promise`, some of these terms might be familiar to people coming from JavaScript background.




## Chapter 3: More Advanced Topics
This section is all about templates. Templates are underrated but they can be extremely versatile and performant if used right. Templates are also [Turing Complete](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.14.3670) and there is a whole new domain called template metaprogramming just for that, where one does most of the computations during compile-time so that we end up with significantly less overhead in runtime.

**Book Suggestions**
* [C++ Template Metaprogramming](https://www.amazon.com/Template-Metaprogramming-Concepts-Techniques-Beyond/dp/0321227255) by David Abrahams.


* [C++ Templates: The Complete Guide](https://www.amazon.com/C-Templates-Complete-Guide-2nd/dp/0321714121) by by David Vandevoorde, Nicolai Josuttis, Douglas Gregor.

### Templates
Templates are recipes, or blueprints to constructing a class or a function given to the compiler for an arbitrary type. The class or function is only instantiated when the types are specified (broadly speaking, however there's also Template Argument Deduction and other methods for instantiating templates). Standard containers like `std::vector` are examples of template classes.
You can use templates to write generic functions and classes. You can also specify non-type template arguments which are literal integral values of characters. Here are some of the topics that you need to study to get yourself properly introduced to templates.

* Learn about function template declarations and instantiations. 
* Template specialization, where you provide special-case implementation for a specific type.
* Learn about template argument deduction and how compiler deduces the correct function signatures.
* `constexpr` and `consteval (CPP20)` and template variables.
* Study class templates, and partial specializations.
* Variadic templates.
* Static Polymorphism using CRTP.


### Template Metaprogramming, Concepts

Metaprogramming is in my opinion the strongest feature that C++ provides. You can perform pretty much anything using template metaprogramming in compile-time. By using template recursion and base-case specialization you can rewrite iterations with a recursive call. To give you an idea, here is an implementation of fibonacci using metaprogramming.


```cpp
template <size_t N>
struct Fibonacci
{
    enum { value = Fibonacci< N-1 >::value + Fibonacci< N-2 >::value };
};

template <>
struct Fibonacci<0>
{
    enum { value = 1 };
};

template <>
struct Fibonacci<1>
{
    enum { value = 1 }; 
};
```

Concepts are introduced in C++20 that allow us some fine-grained control over which types we allow in our templates. This is useful for documenting exactly what your template needs (for example, you define custom iterative algorithm that works on any object that has a `begin()` and `end()` iterator).
You can also do compile-time type-checking, constrain the types you allow, use `<type_traits>` to check whether the template types satisfy certain conditions.
All of these only solidify your code structure so that most of the errors can be figured and fixed during compile-time (and also be readable, not become a 200 line long clutter) and the runtime runs flawlessly. 
Another reason why metaprogramming is important is to reduce the computation overhead for computing runtime constants which can be done in compile-time.
An amazing example of metaprogramming I found recently is [static_math](https://github.com/Morwenn/static_math), which I highly recommend taking a look at.


## Chapter 4: Outside C++

Here I have compiled a bunch of resources, not directly related to C++ that might be beneficial to know alongside. Some of these are integral to development in general, like learning UNIX/Linux.

* Learning UNIX/Linux. [Unix Hater's Handbook](https://web.mit.edu/~simsong/www/ugh.pdf) is a good reference.
* Knowing design patterns. The original [Gang-of-four](https://www.amazon.com/Design-Patterns-Elements-Reusable-Object-Oriented/dp/0201633612) is incomparable, however the language in the book is a bit hard to grasp IMO. A really good website I recently found is [this one](https://refactoring.guru/design-patterns) that explains the design patterns with beautiful visualizations and easy-to-follow explanations, which I'd recommend.


## Next Steps

Since you've come this far, I thank you for your patience. After all these studying and grinding you might be wondering where to go from there (or you might not, maybe you've already decided, good for you). The answer to that is, **projects**, and not just now, you need to keep building small mini-projects from the start and gradually increase in complexity and scale. C++ is a messy language, and not keeping in touch with the language for long enough will make even the most versatile C++ programmer succumb. 

Two of the go-to places for finding project ideas as well as tutorials are:
1. https://github.com/practical-tutorials/project-based-learning
2. https://github.com/danistefanovic/build-your-own-x

They're filled with language-specific tutorials and I'd highly recommend you to spend some time on these.

Subreddit [r/cpp](https://www.reddit.com/r/cpp) is also filled with interesting discussions about C++, new community-created projects to get inspired from, and in general get help from. Stackoverflow is also good but I would advise against asking questions there and simply look for your questions that's already been answered, if you're just beginning, chances are, someone already faced the issue.

[HackerNews](https://news.ycombinator.com/) is a forum for technology and you'll find cool articles or project showcases every now and then.

Overall, the internet is bigger and better than ever with almost every resource at your disposal. If you're starting off with serious intention of mastering C++, you will need to accept the weirdness of a really old language and keep the train moving. Only by doing stuffs, building projects, solving exercises, asking questions, and actively engaging can you finally be able to write a really cool kernel module that nobody understands but, hopefully, help land a rover on Mars. And as  Shia LaBeouf once said:

![just do it](https://media.giphy.com/media/HJPhN12JVDe4o/giphy.gif)




