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
thumbnailImage: cpp.png
thumbnailImagePosition: left
metaAlignment: center
comments: true
meta: false
actions: false
---

# Introduction


Some questions first, we know that in order to implement polymorphism the right way, i.e, dynamic binding and LSP (Liskov Substitution Principle), we have to make the functions `virtual`, sometimes pure virtual, and then reimplement them in the Derived class with an `override` keyword.

We also need to separate the interface from the implementation, i.e, make the virtual function to be overriden `private`, and make a proxy `public` interface function in Base that simply calls the private function. We only need to reimplement the `private` `virtual` function in Derived. A simple example is shown below.

```cpp
class Base
{
	public:
		void call() const;
	private:
		virtual void vCall(); // override in derived
		// virtual void vCall() = 0; // pure virtual, if base has nothing to do
};

inline void Base::call() const
{
	vCall();
}

void Base::vHello()
{
	std::cout << "Base vHello called\n";
}

class Derived: public Base
{
	private:
		void vHello() override;
};

void Derived::vHello()
{
	std::cout << "Derived vHello called\n";
}

int main()
{
	Base *bp = new Derived;
	Base *bp1 = new Base;
	bp->hello(); // Derived
	bp1->hello(); // Base
}
```

This is pretty standard when it comes to providing polymorphic support for some member functions. In addition, if Base class acquires some dynamic memory it also needs to free them, therefore providing a destructor, but the problem is, if Base only has a normal destructor, then using LSP, the Base pointer pointing to a Derived object, once out of scope, will trigger the destructor for Base class only, since there is no v_table entry for destructor, thus if Derived also has some dynamic allocation, they cannot be freed, leading to memory leak. Thus, we also have virtual destructors which work pretty much the same way.

## Virtual Constructor???

Well, it does not make sense. In order for virtual constructor to work, we need to have a v_ptr entry for the address of the constructor function.

but the constructor of a class should have the same type as the class itself. But at the time of construction, the type does not exist yet, as the class object hasn’t been created yet, so we don’t know what the v_ptr should point to, it creates a paradox.

At the same time, v_ptr and vtables are items instantiated for each class objects. There is no vtable before the object creation.

at the time of calling the constructor, **the [virtual table](https://www.learncpp.com/cpp-tutorial/the-virtual-table/) would not have been created** to resolve any virtual function calls. Thus, a virtual constructor itself would not have anywhere to look up to.

Such is the reason why the variants of constructors, such as copy or move constructors also cannot be made virtual.

But we can get around that problem using Prototype design pattern. We want to be able to create copies of a Derived object through a Base ptr or ref to that Derived obj (using LSP).

In this protocol, we make the Base class an abstract base class (ABC), with atleast one pure virtual function, and it does not have a copy constructor.

Generally, it has two functions, a public interface `clone()` and a private pure virtual, `copy()` signalling reimplementation in Derived classes.

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
		explicit Wrapper(Wrapper *bp);
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

inline Wrapper::Wrapper(Wrapper *bp)
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

The Wrapper class exists to provide a unified copy-able interface for the entire family that derives from Base, all the Derived class needs to do is implement its `virtual copy()` method to return a new pointer to a copy of `this` 

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

## No Copy/move constructor for polymorphic Base?

According to C++ guidelines, C.67, ****A polymorphic class should suppress public copy/move****

[C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Rc-copy-virtual)

This is because, whenever the polymorphic Base class has implicitly defined copy/move constructors in place, 

If it is accidentally passed by value, with the implicitly generated copy constructor and assignment, we risk slicing: only the base portion of a derived object will be copied, and the polymorphic behaviour will be corrupted.

This is why, we have to either delete the implicit copy/move constructor like we did in the last example, if the Base class does not have any data, or else make the constructors protected, so the copying can only be done from the context of Derived class.

For more example, the link is useful, check C.67 for this particular issue. -->