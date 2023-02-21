---
layout: post
title: Is const reference always thread safe when used as function parameter?
date: 2023-02-21 23:30 +0530
tags: [c++, threading]
categories: [c]
---
Many people has a fear of thread safety when using **const reference** as parameter to a function. As usual lets start with some examples and understand the thread safety of const reference

Lets consider simple Test object

```cpp
struct Test
{
    Test()
    {
    }

    void name(std::string t)
    {
        m_name = t;
    }

    std::string name() const
    {
        return m_name;
    }

    std::string m_name{};
};
```

Let’s create a function `foo` which has `void foo(const Test& testObj, const int threadNr);` as signature with thread safety using `lock_guard` and create two thread in `main`

```cpp
std::mutex g_mtx;

void foo(const Test& testObj, const int threadNr)
{
    std::lock_guard<std::mutex> lock(g_mtx);

    if (threadNr == 1) {
        std::this_thread::sleep_for(std::chrono::seconds(1));
    }

    std::cout << "Thread " << threadNr << " name is " << testObj.name() << "\n";
}

int main()
{
    Test t;
    t.name("test1");
		// Start thread 1
    std::thread t1 {foo, t, 1};

    // update the name
    t.name("test2");
    std::this_thread::sleep_for(std::chrono::milliseconds(1000));

		// Start thread 2
    std::thread t2 {foo, t, 2};

    if (t1.joinable()) {
        t1.join();
    }

    if (t2.joinable()) {
        t2.join();
    }

    return 0;
}
```

**Output:**

```bash
bhavith@bhavith:~/testing$ ./a.out
Thread 1 name is test1
Thread 2 name is test2
```

Output looks fine right?

Lets make some modification, instead of **reference** let’s update to **pointer**

```cpp
void foo(const Test* testObj, const int threadNr)
{
    std::lock_guard<std::mutex> lock(g_mtx);

    if (threadNr == 1) {
        std::this_thread::sleep_for(std::chrono::seconds(1));
    }

    std::cout << "Thread " << threadNr << " name is " << testObj->name() << "\n";
}
```

```cpp
// Update t to &t since we need to pass address
std::thread t1 {foo, &t, 1}; 
std::thread t2 {foo, &t, 2};
```

**Output:**

```cpp
bhavith@bhavith:~/testing$ ./a.out
Thread 1 name is test2
Thread 2 name is test2
```

Expected:

**Thread 1 name is test1**

Actual:

**Thread 1 name is test2**

Why is behavior so? I know you people have already guessed it. Yes, pointer in both the thread points to the same Test object. 

**But the question is, why reference did not have same behavior as pointer?** 

In order to understand why reference does not have same behavior as pointer, let’s add copy constructor to `Test` class and get back `foo` to use reference signature

`void foo(const Test& testObj, const int threadNr)` 

`std::thread t1 {foo, t, 1};`

`std::thread t2 {foo, t, 2};` 

```cpp
    Test(const Test& t)
    {
				std::cout << "Copy ctor \n";
        m_name = t.m_name;
    }
```

Now let’s re-execute the code.

**Output:**

```cpp
bhavith@bhavith:~/testing$ ./a.out
Copy Ctor
Copy Ctor
Thread 1 name is test1
Thread 2 name is test2
```

Can you observe here, every time, when function is called, a brand new `Test` object is created by invoking copy constructor, but this is not the case with pointer. 

Let’s prove it by adding some cout’s 

```cpp
void foo(const Test& testObj, const int threadNr)
{
    std::lock_guard<std::mutex> lock(g_mtx);

    std::cout << "Object address in thread " << threadNr << " is " << &testObj << "\n";

    if (threadNr == 1) {
        std::this_thread::sleep_for(std::chrono::seconds(1));
    }

    std::cout << "Thread " << threadNr << " name is " << testObj.name() << "\n";
}

int main()
{
    Test t;
    std::cout << "Original object address is " << &t << "\n";
    t.name("test1");
    std::thread t1 {foo, t, 1};

    // update the name
    t.name("test2");
    std::this_thread::sleep_for(std::chrono::milliseconds(1000));

    std::thread t2 {foo, t, 2};

    if (t1.joinable()) {
        t1.join();
    }

    if (t2.joinable()) {
        t2.join();
    }

    return 0;
}
```

Output:

```cpp
bhavith@bhavith:~/testing$ ./a.out
Original object address is 0x7ffc05d84690
Copy Ctor
Object address in thread 1 is 0x5610595982d0
Copy Ctor
Thread 1 name is test1
Object address in thread 2 is 0x561059598440
Thread 2 name is test2
```

Here you can see that `Test` object is local to the function `foo`. From this it is understood that, we can use **const reference** without any fear of thread safety. 

What about using `shared_ptr` as a parameter to function `foo`. Please let me know in comment section.
