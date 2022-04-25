---
layout: post
title: Be careful when using const reference with std::thread / std::async in C++.
  Otherwise you may be in soup if you don't know this ! :)
date: 2022-04-25 13:42 +0530
---

From the basics of C and C++. we all know that when we use address-of operator to the variable, then it is an alias of the original variable it points to, and shares the same address.

Example:

```cpp
#include <iostream>

void foo(const int& b) 
{
    std::cout << "Address of variable 'b' in foo  is " << &b << "\n";
}

int main()
{
    int a = 10;
    foo(a);

    std::cout << "Address of variable 'a' in main is " << &a << "\n";

    return 0;
}
```

output

```bash
Address of variable 'b' in foo  is 0x7ffcb65f3084
Address of variable 'a' in main is 0x7ffcb65f3084
```

From the above example you can see address of `b` used in function foo is same as address of variable `a` used in main function because we used reference (address-of operator) `const int& b`

Also the value of `b` cannot be changed, since its a `const` variable

#### Fun begins now, lets call the function `foo` in a thread using `std::thread` and see the output of below code

```cpp
#include <iostream>
#include <thread>

void foo(const int& b) 
{
    std::cout << "Address of variable 'b' in foo  is " << &b << "\n";
}

int main()
{
    int a = 10;
    std::thread t(foo, a);


    std::cout << "Address of variable 'a' in main is " << &a << "\n";


    t.join();
    return 0;
}
```
output

```bash
Address of variable 'a' in main is 0x7ffc4d4126cc
Address of variable 'b' in foo  is 0x563b96627eb8
```

#### What the heck? what's happening here?

Here we have passed the value `a` as argument to function `foo` and received as reference too. but what happened? Before answering to the question lets remove `const` from the argument of function `foo` and see what happens

```cpp
void foo(int& b)
{
    std::cout << "Address of variable 'b' in foo  is " << &b << "\n";
}
```

output

```bash
bhavith $  g++ test.cpp -pthread
In file included from m2.cpp:2:
/usr/include/c++/9/thread: In instantiation of ‘std::thread::thread(_Callable&&, _Args&& ...) [with _Callable = void (&)(int&); _Args = {int&}; <template-parameter-1-3> = void]’:
m2.cpp:13:25:   required from here
/usr/include/c++/9/thread:120:44: error: static assertion failed: std::thread arguments must be invocable after conversion to rvalues
  120 |           typename decay<_Args>::type...>::value,
      |                                            ^~~~~
/usr/include/c++/9/thread: In instantiation of ‘struct std::thread::_Invoker<std::tuple<void (*)(int&), int> >’:
/usr/include/c++/9/thread:131:22:   required from ‘std::thread::thread(_Callable&&, _Args&& ...) [with _Callable = void (&)(int&); _Args = {int&}; <template-parameter-1-3> = void]’
m2.cpp:13:25:   required from here
/usr/include/c++/9/thread:243:4: error: no type named ‘type’ in ‘struct std::thread::_Invoker<std::tuple<void (*)(int&), int> >::__result<std::tuple<void (*)(int&), int> >’
  243 |    _M_invoke(_Index_tuple<_Ind...>)
      |    ^~~~~~~~~
/usr/include/c++/9/thread:247:2: error: no type named ‘type’ in ‘struct std::thread::_Invoker<std::tuple<void (*)(int&), int> >::__result<std::tuple<void (*)(int&), int> >’
  247 |  operator()()
      |  ^~~~~~~~
```

What the hell? We are getting compilation error. The important error message to observe is

```bash
/usr/include/c++/9/thread:120:44: error: static assertion failed: std::thread arguments must be invocable after conversion to rvalues
  120 |           typename decay<_Args>::type...>::value,
      |                                            ^~~~~
```

it is complaining that the value passed as a second argument to `thread` is not a `rvalue`, we need to convert to `rvalue` before sending. What is `rvalue` is an another topic and discuss later.

How to convert the argument to `rvalue`?

Just we need to use `std::ref` class to convert to `rvalue`. i.e. while calling in thread we need to use like below

```cpp
std::thread t(foo, std::ref(a));
```
Note: we wrapper `a` with `std::ref`. In this case we created a `std::ref` object and passed to the argument of function `foo` in thread.

Now lets see the result after fixing the compilation error

```bash
Address of variable 'a' in main is 0x7ffc6e26dab4
Address of variable 'b' in foo  is 0x7ffc6e26dab4
```
What the hell it is? Now address of both `a` and `b` is same, as soon as we remove the `const` from argument variable

### In order to understand this behavior we need to understand two things that how intelligently the std::thread is implemented

#### When `const` with argument is used how std::thread behaves ?

When `const` with argument is used, we know we are not going to modify the variable any way inside function `foo` and also compiler will not allow you to do so, then making a reference or alias to original variable may not be thread safe even though if we lock the variable. Because, during the execution of the thread, the passed variable to function `foo` may changed by `main` function. Therefore in order to make thread safe `std::thread` makes a copy of the variable and uses it. So that the copied variable is local to the function and all the local variable to the function is always thread safe

#### When `const` is not used with the argument how std::thread behaves ?

When `const` is not used with the argument of function `foo`, then `std::thread` will not make a copy of the variable, Because making a copy will make no sense because changing the variable in `main` function will not reflect in the function `foo` and the entire program will not work as expected. Now, in this case it leaves to user of `std::thread` to take care of thread safety. That means you know what you are doing.

### The behavior is same w.r.t std::async

*With `const` reference*

```cpp
#include <iostream>
#include <future>

void foo(const int& b)
{
    std::cout << "Address of variable 'b' in foo  is " << &b << "\n";
}

int main()
{
    int a = 10;
    auto f = std::async(foo, a);


    std::cout << "Address of variable 'a' in main is " << &a << "\n";


    f.get();
    return 0;
}
```
output

```bash
Address of variable 'a' in main is 0x7ffd413ceedc
Address of variable 'b' in foo  is 0x55758197bef8
```
Addresses are different

*Without `const` reference*

```cpp
#include <iostream>
#include <future>


void foo(int& b)
{
    std::cout << "Address of variable 'b' in foo  is " << &b << "\n";
}


int main()
{
    int a = 10;
    auto f = std::async(foo, std::ref(a));


    std::cout << "Address of variable 'a' in main is " << &a << "\n";


    f.get();
    return 0;
}
```
output
```bash
Address of variable 'a' in main is 0x7ffcd6428a94
Address of variable 'b' in foo  is 0x7ffcd6428a94
```

Shares the same address

### Summary

> Be careful when working with reference and `std::thread` / `std::async`. I recommend to always to use `const` reference since it makes sure shared variables are thread safe provided you don't want to modify the variable during the execution of the thread.


| Function argument | Variable passed to thread as | Address            |
|-------------------|------------------------------|--------------------|
| const reference   | a                            | different address  |
| const reference   | std::ref(a)                  | same address       | 
| reference         | std::ref(a)                  | same address       |