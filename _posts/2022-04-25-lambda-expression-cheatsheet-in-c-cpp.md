---
layout: post
title: Lambda Expression Cheatsheet in C++/CPP
date: 2022-04-25 13:37 +0530
tags: [c++, lambda]
---

With out much theory on what is lambda and what is the history behind it. Lets get straight to the point.

I see lot of the developers get confused when they want to use lambda, because they feel irritation by seeing its syntax.  But those developers coming from C#, javascript or some other functional programming language they feel happy when they see lambda.

C++ developer keep going through lot of videos on YouTube and stack overflow at last end up in a big confuse and stop using it, especially a programmer with 2 or 3 years of experience.

To help a new bees in c++ programming I have created a cheat sheet which covers most of the scenarios with Lambda expressions and can improve it further with std::for_each  std::bind etc. and do it your self

I assume that reader has little knowledge on function pointers in C. Its good to know function pointer to understand lambda better

In simple you can use Lambda function where ever normal function is used.

Now go through below program which contain all scenarios of lambda


Hear is the cheat sheet for lambda cpp

```cpp
#include <iostream>
#include <functional>

using namespace std;

void foo()
{
}

void hello()
{
    cout << "Hello" << endl;
}

int main()
{
    int a = 0;
    int b = 0;
    
    // Equivalent to foo (Think it as function without any name)
    [](){};
    
    // Equivalent to hello
    [](){
        cout << "Hello" << endl;
    };
    
    // But above lambda will not print any thing because we did not called lambda

    // How to call ? 
    // just use () after lamba like calling regular function
    [](){
        cout << "Hello printed" << endl;
    }(); // () -> calls the lambda 
    
    
    // 1. Calculate sum of a and b using lambda 
    a = 10;
    b = 20;
    int sum = [](int x, int y) {
        return x + y;
    }(a, b); // It calls lambda like function and store the result in variable sum
    
    cout << "sum of " << a << " and " << b << " is " << sum << endl;
    
    // 2. Specify the return type of lambda 
    int sum2 = [](int x, int y) -> int {
        return x + y;
    }(a, b);
    cout << "sum of " << a << " and " << b << " is " << sum << endl;
    
    
    // 3. How to call lambda later instead of immediate call
    // std::function needs #include <functional>
    std::function<int(int, int)> mySum = [](int x, int y) -> int 
    {
        return x + y;
    };
    
    cout << "sum of " << a << " and " << b << " is " << mySum(a, b) << endl;
    
    
    // 4. Use function poiner instead of std::function 
    int (*pMySum)(int, int) = [](int x, int y) -> int
    {
        return x + y;
    };
    cout << "sum of " << a << " and " << b << " is " << pMySum(a, b) << endl;
    
    
    // 5. Point 3 and 4 is hard way, the simpler way is using auto
    auto autoSum = [](int x, int y) -> int 
    {
        return x + y;
    };
    cout << "sum of " << a << " and " << b << " is " << autoSum(a, b) << endl;
    
    // 6. Still easy way, i.e. every where auto and no return type required
    auto stillEasy = [](auto x, auto y)
    {
        return x + y;
    };
    cout << "sum of " << a << " and " << b << " is " << stillEasy(a, b) << endl;
    
    
    // 7. How to calculate sum using variable a and b without
    //    passing as a parameter ? 
    // Here value a and b are passed a value
    auto sum3 = [a, b]()
    {
        return a + b;
    };
    // Observe that we called sum3() not sum3(a, b)
    cout << "sum of " << a << " and " << b << " is " << sum3() << endl; 
    
    // 8. How to save two variable creations using reference 
    // Here values a and b are passed as reference
    auto sum4 = [&a, &b]()
    {
        return a + b;
    }(); // -> Here I used () so it already called and 
         // no need to call like sum4() just use sum4 as variable

    // Observe that we called sum4 not sum4()
    cout << "sum of " << a << " and " << b << " is " << sum4 << endl; 
    
    
    // 9. What if we want all the variable to be passed to lambda from main scope
    // Then use [=] for pass by value and [&] for pass by reference.
    // 5.1 pass all variable (a and b) to lambda by value
    auto sum5 = [=]()
    {
        return a + b;
    }();
    // Observe that we called sum5 not sum5()
    cout << "sum of " << a << " and " << b << " is " << sum5 << endl; 
    
    // 5.2 pass all variable (a and b) to lambda by reference
    auto sum6 = [&]()
    {
        return a + b;
    };
    // Observe that we called sum6() not sum6 just for a combinations
    cout << "sum of " << a << " and " << b << " is " << sum6() << endl; 
    
    return 0;
}
```
In Lambda

| Symbol | Description |
|---------|-------------|
|[ ] | Capture list where we can capture list of variable either by reference or by value|
|( ) | Arguments similar to function argument|
|{ } | Lambda body which is similar to lambda body|

#### Bonus:
int a, b;

| Symbol | Description |
|---------|-------------|
|[=] | Capture all the variables (a and b) as value |
|[&] | Capture all the variables (a and b) as reference |
|[&a] | Capture only a as reference and access to b in lambda gives compilation error|
|[&a, b] | Capture variable a as reference and b by value|

We can pass any number of items in capture list like `[&a, b, c, &d]`
We can pass all the values by reference expect one Ex:` [&, b]` -> All the values are captures by reference except `b`

Return type:
```cpp
[ ]( )->return_type
{
};
```
Here return_type is optional