---
layout: post
title: How member function of a class accesses its data member (Technically, how this pointer works)
date: 2022-05-18 21:58 +0530
tags: [c++, cpp, pointers, this-pointer]
categories: [c++ or cpp]
---

We all know that there is only one copy of function exist's for N number of objects or instance of a given class.

If you don't understand the above sentence let me first explain with some example

```cpp
class Laptop
{
public:
    auto setName(const std::string& name) -> void
    {
        m_name = name;
    }
private:
    std::string m_name = "Dell";
};
```
Here we have a class called `Laptop`, and we can create 'N' number of instances from this class. Then we have 'N' number of `std::string` variable get's created with name `m_name` (Technically name mangled `m_name`). That means if we create two instances, then these two instance has a two `std::string` variable (Technically object) gets created one for each instance. **But tricky part or the fact is**, This two instances hash only one copy of the function and it is shared between two instances.

Let me give some more clarification with example.
```cpp
int main()
{
    Laptop instance1;
    Laptop instance2;
    instance1.setName("Lenovo");
    instance2.setName("Asus");
    return 0;
}
```

In the above example we have created two instances `instance1` and `instance2`. These two instances has its own copy of `std::string` object with name `m_name`. But these two instances shares the same function. 

![img-description](/assets/images/this_pointer.png)

### Now the question is how function know's which instance's `m_name` to use?
In order to know this we need to understand what compiler does internally during compilation.

What compiler does? 

- Changes the function protoype and body like this 
```cpp
auto setName(Laptop* const this, const std::string& name) -> void
{
    this->m_name = name;
}
```
- Changed the caller of function like this 
```cpp
Laptop instance1;
setName(instance1, "Lenovo");
```

Now it's easy isn't it? because function knows which object's `m_name` to use. because its been passed as a first argument to all the member function of the class.

This is the beauty of `this` pointer. That's why you could able to use `this->` in all your member function of class which out being aware from where this magical `this` comes from.

### Hey that fine, what about static function?

Yes, static function doesn't have a `this` pointer because `static` function doesn't belongs to instance of the class, rather it belongs to class. 

**What does it mean?**

Let me show you an example by making `setName` as static function
```cpp
static auto setName(const std::string& name) -> void
{
    m_name = name;
}
```

As soon as you do this, you'r IDE will prompt with error pointing at `m_name` as 

> a nonstatic member reference must be relative to a specific objectC/C++(245)
{: .prompt-danger }

Even then if you go ahead and try to compile you get this error
```bash
bhavith@bhavith:~$ g++ main.cpp
main.cpp: In static member function ‘static void Laptop::setName(const string&)’:
main.cpp:10:9: error: invalid use of member ‘Laptop::m_name’ in static member function
   10 |         m_name = name;
      |         ^~~~~~
main.cpp:13:17: note: declared here
   13 |     std::string m_name = "Dell";
      |                 ^~~~~~
```

From this we know that static function cannot have a `this` pointer as a first argument to its function and it does not belongs to any of the instances rather its an independent function exist in class and can access only static member variables.
