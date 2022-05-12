---
title: prototype-pattern
date: 2022-03-22 23:19:04
categories:
- programming
tags:
- design pattern
- cpp
- inheritance
clearReading: true
thumbnailImage: clone.png
thumbnailImagePosition: left
metaAlignment: center
comments: true
meta: false
actions: false
---

<!-- more -->

A discussion on the Prototype design pattern and basic implementation in C++

<!-- excerpt -->

<!-- toc -->


# Introduction


While studying a bit of design pattern, one particular pattern I liked was the Prototype pattern. It's premise is very simple. You'd want to be able to make proper copies of objects without the need of knowing its class.
Copying in general is fairly straightforward, you have to define a copy constructor, and the assignment operator (optionally, move-aware versions of them). But that requires knowing the underlying class name to create copies of the same class-type from existing (or expiring) objects.

But in a case where the actual type of the object is not inferrable, primarily when polymorphic classes are used in the form of Base ptrs (or refs), we only have class information about `Base`. Therefore, naively applying copy or assignment would simply copy the `Base` part of the object, commonly known as slicing.

We might be tempted to make the constructor function polymorphic, but therein lies the trouble, and hence, the pattern.

# Virtual Constructors (?)

Well, it does not make sense (atleast in C++). It goes against the very principle of virtual functions and polymorphism.
We create virtual functions so that we can get different versions of them depending on the object they're called from. It cannot happen if the object doesn't exist yet.

Additionally, if you know how polymorphism is implemented in C++, you'd know that each polymorphic class has a static `virtual table` containing the addresses of its virtual functions, and each time the class is instantiated, the object's internal `virtual pointer` points to its own class's `vtable`.
However, according to Bjarne Stroustup, you may not have a pointer to the constructor, but you also cannot have the `v_ptr` point to the `vtable` unless the object is constructed, so it creates a paradox.

Finally, to clear things up, this quote from the man himself on [why we can't have virtual ctor](https://www.stroustrup.com/bs_faq2.html#virtual-ctor):
> A virtual call is a mechanism to get work done given partial information. In particular, "virtual" allows us to call a function knowing only any interfaces and not the exact type of the object. To create an object you need complete information. In particular, you need to know the exact type of what you want to create. Consequently, a "call to a constructor" cannot be virtual.

For similar reasons, the variants of constructors, such as copy or move constructors also cannot be made virtual.


# Solution

However, if you are really into making copies of polymorphic objects through a unified Base interface,then Prototype pattern is a workaround for this sort of problem.

In this protocol, we make the Base class an abstract base class (ABC), with atleast one pure virtual function, and it does not have a copy constructor.

Generally, it has two functions, a public interface `clone()`, and a private pure virtual function, `copy()` signalling reimplementation in Derived classes.

```cpp
class Base
{
	public:
		Base(Base const &) = delete;
		virtual ~Base();
		Base *clone() const;

	private:
		virtual Base *copy() = 0;
};

inline Base *Base::clone() const
{
	return copy();
}

// not inline
Base::~Base()
{}
```

We offer a Wrapper class over Base that provides the copy functionalities:

```cpp
class Wrapper
{
	Base *d_bp;
	
	public:
		Wrapper();
		explicit Wrapper(Base *bp);
		~Wrapper();
		Wrapper(Wrapper const &other);
		Wrapper(Wrapper &&tmp);

		Wrapper &operator=(Wrapper const &other);
		Wrapper &operator=(Wrapper &&tmp);
		Base &getBase() const;
};

inline Wrapper::Wrapper()
:
	d_bp(0)
{}

inline Wrapper::Wrapper(Base *bp)
:
	d_bp(bp)
{}

inline Wrapper::~Wrapper()
{
	delete d_bp;
}

inline Wrapper::Wrapper(Wrapper const &other)
:
	d_bp(other.d_bp->clone())
{}

inline Wrapper::Wrapper(Wrapper &&tmp)
:
	d_bp(tmp.d_bp)
{
	tmp.d_bp = 0;
}

inline Base &Wrapper::getBase() const
{
	return *d_bp;
}

Wrapper &Wrapper::operator=(Wrapper const &other)
{
	Wrapper tmp(other);
	std::swap(d_bp, tmp.d_bp);
	return *this;
}

Wrapper &Wrapper::operator=(Wrapper &&tmp)
{
	std::swap(d_bp, tmp.d_bp);
	return *this;
}
```

Notice the copy constructor for `Wrapper`.
It calls the `clone()` public interface of the inner `Base*` ptr, which will call the correct virtual `copy()` function, determined at runtime because the derived object to which Base* is pointing to, is already constructed, and now we can easily create copies of the pointed Derived object, via the Base* interface.

The Wrapper class exists to provide a unified copy-able interface for the entire family that derives from Base, all the Derived class needs to do is implement its `virtual copy()` method to return a new pointer to a copy of `this`.

```cpp
class Derived: public Base
{
	public:
		~Derived() override;
	private:
		Base *copy() const override;
};

inline Base *Derived::copy() const
{
	return new Derived(*this);
}
```

A good use-case is when we want to use polymorphic base classes in abstract containers.
We cannot just use `std::vector<Base>` and insert `Derived` objects in there, because `push_back()` tries to copy / move the `Derived` object and will fail (no proper copy strategy from `Derived` to `Base` exists).

Instead, in a `std::vector<Wrapper>`, we can `push_back` both a `Wrapper` object initialized with a `Base*`, and a `Derived*`, and at the time of insertion, it will copy construct the internal pointed-to object properly (`other.d_bp->clone()` will call `Base::copy()` and `Derived::copy()` respectively and return a pointer to a deep-copied `Base` or `Derived` object from the passed one).


# Conclusion
Prototype pattern is somewhat complex, but nevertheless pretty useful. Off the top of my head, one implementation would be to facilitate Java-like generics such as `ArrayList<T>`, where all classes can be made subclasses to a common Base class, and provide safe copying (although I don't see the need of that in C++ since abstract containers and templates already exist and are much more powerful).
We could also use this pattern to enforce a guideline to provide the classes inheriting from the Base with `copy` and `clone` functionality to be able to integrate with some Base configuration, while preserving the Derived object's structure.
Lastly, closely related to the Factory pattern, when your Base class has multiple hierarchies of polymorphic derivations, instead of providing a factory method for each of the classes, we can make each class in the inheritance chain a prototype and provide them with the `clone` function which the clients can use to create copies of whichever type they are working with.

# Further Reading

1. [Why do we not have a virtual constructor in C++? [StackOverflow]](https://stackoverflow.com/questions/733360/why-do-we-not-have-a-virtual-constructor-in-c)
2. [A really good explanation of Prototype Pattern with examples in various languages](https://refactoring.guru/design-patterns/prototype)
3. [Details about Virtual Tables](https://www.learncpp.com/cpp-tutorial/the-virtual-table/)
4. [CppAnnotations, Chapter 14.12](http://www.icce.rug.nl/documents/cplusplus/)
