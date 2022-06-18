---
layout: post
title: Track heap allocation as well check memory leaks in C++ programmatically
date: 2022-06-18 17:49 +0530
tags: [c++, cpp, operator-overloading]
categories: [c++]
---

#### How can we track number of bytes allocated in heap for the given object.

We know that allocating a memory in heap is costly, if we want to know exactly how much memory is allocated on heap we can use valgrind tool and execute our executable. But how can we do it programmatically? 

Yes, you are correct, programmatically, and it is not difficult as you think and it is very much easy and fast compare to valgrind (I agree it is not much efficient as valgrind) but it servers our purpose.

Let's stop the story and dig into example. In order to track the heap allocation we need to overload two operator's of the class which needs to be tracked. That is 
- `new operator`
- `delete operator`

Also in order to track allocation of all the objects we need to have `static` variable since static variable does not belongs to any object.

That's all you need to do. Now let's see with full implemented example
```cpp
#include <iostream>

class Animal
{
public:

    void* operator new (size_t size)
    {
        std::cout << "Inside new operator \n";
        m_size += size;
        return malloc(size);
    }

    void operator delete (void* ptr)
    {
        std::cout << "Inside delete operator \n";
        if (ptr) {
            m_size -= sizeof(*((Animal*)ptr));
            free(ptr);
        }
    }

    Animal()
    {
        std::cout << "Ctor called \n";
    }

    ~Animal()
    {
        std::cout << "Dtor called \n";
    }

    static long getHeapAllocation()
    {
        return m_size;
    }

private:
    int m_height = 0;
    int m_weight = 0;
    static long m_size;
};

long Animal::m_size = 0;

int main()
{
    Animal* p = new Animal;
    std::cout << "1 - " << p->getHeapAllocation() << "\n";
    Animal* q = new Animal;
    std::cout << "2 - " <<  q->getHeapAllocation() << "\n";
    delete p;
    std::cout << "3 - " << p->getHeapAllocation() << "\n";
    delete q;
    std::cout << "4 - " << q->getHeapAllocation() << "\n";
    return 0;
}
```
Output:
```bash
bhavith@bhavith:$ ./a.out 
Inside new operator 
Ctor called 
1 - 8
Inside new operator 
Ctor called 
2 - 16
Dtor called 
Inside delete operator 
3 - 8
Dtor called 
Inside delete operator 
4 - 0
```
Here we can see how memory is allocating and de-allocating.

Now let's see user forget to delete pointer `q`. Now how can we find how much memory is allocated and freed. For that let's alter a program and keep track of it

Update code 
```cpp
#include <iostream>

// Track the number of bytes allocated on heap section
class Animal
{
public:

    void* operator new (size_t size)
    {
        m_allocation += size;
        return malloc(size);
    }

    void operator delete (void* ptr)
    {
        if (ptr) {
            m_deallocation += sizeof(*((Animal*)ptr));
            free(ptr);
            ptr = nullptr;
        }
    }

    static void checkMemoryLeakage()
    {
        std::cout << "\n";
        std::cout << "--------------------------------------\n";
        if (m_allocation > m_deallocation) {
            std::cout << "==== " << m_allocation - m_deallocation << " byte(s) leaked ====\n";
        } else {
            std::cout << "No memory leaks found !\n";
        }
        std::cout << "--------------------------------------\n";
    }

private:
    int m_height = 0;
    int m_weight = 0;
    static long m_allocation;
    static long m_deallocation;
};

long Animal::m_allocation = 0;
long Animal::m_deallocation = 0;

#define CHECK_MEMORY_LEAKS Animal::checkMemoryLeakage();

int main()
{
    Animal* p = new Animal;
    Animal* q = new Animal;
    delete p;
    delete q;

    CHECK_MEMORY_LEAKS;
    return 0;
}
```

As you can see, just we need to add a macro `CHECK_MEMORY_LEAKS` before `return`. I agree this not only the place, also we can use handler as soon as program exit's and do memory checks there as well. 

let' see the output when no memory leaks 

```bash
bhavith@bhavith:$ ./a.out 

--------------------------------------
No memory leaks found !
--------------------------------------
```

Now let try commenting the `delete q` and see the output
```cpp
int main()
{
    Animal* p = new Animal;
    Animal* q = new Animal;
    delete p;
    // delete q; -> memory leak

    CHECK_MEMORY_LEAKS;
    return 0;
}
```

Output:
```bash
bhavith@bhavith:$ ./a.out 

--------------------------------------
==== 8 byte(s) leaked ====
--------------------------------------
```

Here we can see 8 bytes are not freed. 

Is it not look simple to implement your own valgrind. Yes right?
