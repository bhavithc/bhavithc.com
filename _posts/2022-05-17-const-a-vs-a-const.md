---
layout: post
title: Pointer to a `const` or a `const` pointer
date: 2022-05-17 22:15 +0530
tags: [c++, cpp, pointers]
categories: [c++ or cpp]
---

Many of the times we often see some function, where arguments to functions are like `const Animal* pAnimal` or `Animal* const pAnimal`. Our head spins for while especially if we are new to cpp (Often happens to experienced cpp developers as well (lol))

```cpp
auto foo(const Animal* pAnimal) -> void
{
    ...
}

Vs

auto foo(Animal* const pAnimal) -> void
{
    ...
}
```

Let's dig in deeper with some example `Animal` class

```cpp
class Animal
{
public:
    auto setName(const std::string& name) -> void
    {
        m_name = name;
    }

    auto getName() -> std::string
    {
        return m_name;
    }

private:
    std::string m_name = "Anonymous"; 
};
```

### Lets see '*const Animal\* pAnimal* '

In the above example, `Animal` class has setter and getter for animal name. Now let's write a function which takes pointer to an Animal object and print it's name

```cpp
void printAnimalName(Animal* pAnimal)
{
    if (pAnimal) {
        std::cout << "Animal name is " << pAnimal->getName() << "\n";
    }
}

int main()
{
    Animal* p = new Animal;
    p->setName("Lion");
    printAnimalName(p);
    return 0;
}
```
Save the above program as `main.cpp`

Let's compile and execute it
```bash
bhavith@bhavith:~$ g++ main.cpp
bhavith@bhavith:~$ ./a.out
Animal name is Lion
```

In function `printAnimal`,  the argument is `Animal* pAnimal` so we can call it's member function and even modify the `Animal` inside function since it is pointer.

If we change the argument from `Animal* pAnimal` to `const Animal* pAnimal` then we cannot change the value of `Animal` inside `printAnimalName`

**What does it mean that we cannot change the value of `Animal`?**

Let's modify the `printAnimal` and try to change (set) the value, i.e. name of the Animal
```cpp
void printAnimalName(const Animal* pAnimal)
{
    // Change the name of Animal
    pAnimal->setName("Tiger");

    if (pAnimal) {
        // I will talk about this typecast below, as of now forget the below typecast
        std::cout << "Animal name is " << ((Animal*)pAnimal)->getName() << "\n";
    }
}
```
Let's compile and see what happens

```bash
bhavith@bhavith:~$ g++ main.cpp
main.cpp: In function ‘void printAnimalName(const Animal*)’:
main.cpp:24:21: error: passing ‘const Animal’ as ‘this’ argument discards qualifiers [-fpermissive]
   24 |     pAnimal->setName("Tiger");
      |     ~~~~~~~~~~~~~~~~^~~~~~~~~
main.cpp:7:10: note:   in call to ‘void Animal::setName(const string&)’
    7 |     auto setName(const std::string& name) -> void
      |          ^~~~~~~
```

Compiler is complaining that user is trying to pass `const Animal` to function `setName` as `this` argument where `this` pointer is expecting `non const` Animal object. In simple, user is passing constant value to non const `this` pointer which is the first argument to `setName` (Blog on this pointer is coming soon)

But we can compile without any error in two ways
1. Type cast the `pAnimal` from `const` to non `const`
2. Pass `-fpermissive` flag for compiler


**1. Let's try with typecast**

```cpp
void printAnimalName(const Animal* pAnimal)
{
    // typecast
    ((Animal*)pAnimal)->setName("Tiger");
    if (pAnimal) {
        // typecast
        std::cout << "Animal name is " << ((Animal*)pAnimal)->getName() << "\n";
    }
}
```

compile and run

```bash
bhavith@bhavith:~$ g++ main.cpp
bhavith@bhavith:~$ ./a.out 
Animal name is Tiger
```
everything works smooth now

1. Let's try with `-fpermissive`
> `-fpermissive`: Downgrade some diagnostics about nonconformant code from errors to warnings. Thus, using -fpermissive will allow some nonconforming code to compile.
{: .prompt-tip }

```cpp
void printAnimalName(const Animal* pAnimal)
{
    pAnimal->setName("Tiger");
    if (pAnimal) {
        std::cout << "Animal name is " << ((Animal*)pAnimal)->getName() << "\n";
    }
}
```

compile and run
```bash
bhavith@bhavith:~$ g++ main.cpp -fpermissive
main.cpp: In function ‘void printAnimalName(const Animal*)’:
main.cpp:24:21: warning: passing ‘const Animal’ as ‘this’ argument discards qualifiers [-fpermissive]
   24 |     pAnimal->setName("Tiger");
      |     ~~~~~~~~~~~~~~~~^~~~~~~~~
main.cpp:7:10: note:   in call to ‘void Animal::setName(const string&)’
    7 |     auto setName(const std::string& name) -> void
      |          ^~~~~~~
bhavith@bhavith:~$ ./a.out 
Animal name is Tiger
```
Yes, compiler warn's that your are doing some thing wrong, but since we used `-fpermissive` it allowed us to do wrong (lol)

### Moral of `const Animal* pAnimal` 
We cannot change the value of the animal when it is passed as `const Animal*` to a function. Since we made Animal as a constant, constant cannot be changed. But as we had seen that we can change by  either typecasting or by forcing the compiler by using `-fpermissive` flag


### Lets see '*Animal\* const pAnimal* '

With the same `printAnimalName` function let's change the signature from `const Animal* pAnimal` to `Animal* const pAnimal` and compile it

```cpp
void printAnimalName(Animal* const pAnimal)
{
    pAnimal->setName("Tiger");
    if (pAnimal) {
        std::cout << "Animal name is " << ((Animal*)pAnimal)->getName() << "\n";
    }
}
```
compile and run

```
bhavith@bhavith:~$ g++ main.cpp
bhavith@bhavith:~$ ./a.out 
Animal name is Tiger
```

Hey, what's this? compiler is not complaining about changing the value of `Animal` (name of the `Animal`). We would have used this kind of signature in our first example so that there was no need of typecast neither `-fpermissive` flag usage while compiling.

> This is the big catch that every cpp developer get stuck and confused and some how avoid it and move on once it worked.
{: .prompt-warning}

**What is the real meaning of `Animal* const pAnimal` ?**

What it tells is, pointer (`Animal*`) is a constant, more specifically it is read-only. In simple, to make understanding much more easy, consider variable `pAnimal` is read-only variable. That means we cannot reassign this variable to other variable of same type. i.e. other variable of `Animal` type in this case.

Let's try to reassign the variable

```cpp
void printAnimalName(Animal* const pAnimal)
{
    // Create a new Animal object and try to assign this to pAnimal
    Animal *pAnotherAnimal = new Animal;
    pAnimal = pAnotherAnimal; //re-assign to new variable (pointer)
    pAnimal->setName("Tiger");
    if (pAnimal) {
        std::cout << "Animal name is " << ((Animal*)pAnimal)->getName() << "\n";
    }
}
```

Compile it
```
bhavith@bhavith:~$ g++ main.cpp
main.cpp: In function ‘void printAnimalName(Animal*)’:
main.cpp:25:13: error: assignment of read-only parameter ‘pAnimal’
   25 |     pAnimal = pAnotherAnimal;
      |     ~~~~~~~~^~~~~~~~~~~~~~~~
```

From the above example you can see that pointer is a constant, i.e. read-only. We cannot assign this variable (`pAnimal`) to any other object.

### Moral of `Animal* const pAnimal` 
We cannot change the value of pointer since pointer is constant here. There is no way that typecast or compiler flag works here.

> This is what the signature of `this` pointer looks i.e. `Animal* const this`. i.e. `this` cannot point to any other object.
{: .prompt-tip }


### Comparison and Conclusion

|`const Animal* pAnimal` | `Animal* const pAnimal`|
|--|--|
|Animal object is a constant <br/> we cannot change the animal | Animal pointer is a constant<br/> we cannot assign the pointer to another object |
|Used when user don't want to change the value | Used as argument to ensure pointer is not pointing <br/> to any other object in the life time of the function or function scope|