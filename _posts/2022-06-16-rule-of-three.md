---
layout: post
title: Rule of three in c++
date: 2022-06-16 23:05 +0530
tags: [c++, cpp, modern-c++]
categories: [c++]
---

There are three special functions in C++ class 
- `destructor`
- `copy constructor`
- `overload assignment operator`

If one of these functions is not defined by programmer, then compiler defines it for you with default behavior

What are those default behaviors
- `destructor` - It calls destructor of the member variables
- `copy constructor` - Copy states of other object to this class and do shallow copy that means, if pointer is the state of other object, then it copies the pointer and doesn't create a new pointer and copy the content
- `overload assignment operator` - It is similar to copy constructor

**If class has a pointer, then we have to follow this rule of thumb and define all of these three member function explicitly, So that we can avoid shallow copy**

Let's see with example 

```cpp
enum class Color 
{
    Red,
    Black,
    Silver,
    Invalid
};

const char* colorToStr(const Color& color)
{
    switch (color)
    {
    case Color::Black:
        return "Black";
    case Color::Red:
        return "Red";
    case Color::Silver:
        return "Silver";
    case Color::Invalid: 
    default:
        return "Invalid Color";
    }
}

class Car 
{
public:

    Car()
    {
        std::cout << "Default ctor called \n";
        m_pColor = new Color(Color::Invalid);
    }

    Car(const Color& color)
    {
        std::cout << "Parameterized ctor called \n";
        m_pColor = new Color(color);
    }

    ~Car()
    {
        std::cout << "Dtor called \n";
        if (m_pColor) {
            delete m_pColor;
        }
    }

    Car(const Car& other)
    {
        std::cout << "Copy ctor called \n";
        if (other.m_pColor) {
            m_pColor = new Color(*other.m_pColor);
        }
    }

    void operator=(const Car& other)
    {
        std::cout << "Overload assignment operator called \n";
        if (other.m_pColor) {
            if (m_pColor) {
                *m_pColor = *other.m_pColor;
            }
        }
    }

    void displayColor() const
    {
        if (m_pColor) {
            std::cout << "Car color is " << colorToStr(*m_pColor) << "\n";
        }
    }

private:
    Color* m_pColor;
};

int main()
{
    Car blackCar(Color::Black); // Parameterized ctor called
    Car anotherBlackCar = blackCar; //Copy ctor called also `Car anotherBlackCar(blackCar);` is valid syntax
    Car thirdBlackCar; // Default ctor called
    thirdBlackCar = anotherBlackCar; // overload assignment operator is called

    blackCar.displayColor();
    anotherBlackCar.displayColor();
    thirdBlackCar.displayColor();

    return 0;
}
```
Output:
```shell
bhavith@bhavith:$ g++ hello.cpp
bhavith@bhavith:$ ./a.out 
Parameterized ctor called 
Copy ctor called 
Default ctor called 
Overload assignment operator called 
Car color is Black
Car color is Black
Car color is Black
Dtor called 
Dtor called 
Dtor called
```

In the above example we can see, `Car` has a member variable as a pointer to `Color` enum class, So according to rule of three, We have to define `destructor`, `copy constructor` and `overload assignment operator` so that we can do deep copy.

**What if we use `RAII` for example shared pointer as a member variable?**

In such case we can have only rule of two, that is `copy constructor` and `overload assignment operator` and `destructor` is useless

Example
```cpp
enum class Color 
{
    Red,
    Black,
    Silver,
    Invalid
};

const char* colorToStr(const Color& color)
{
    switch (color)
    {
    case Color::Black:
        return "Black";
    case Color::Red:
        return "Red";
    case Color::Silver:
        return "Silver";
    case Color::Invalid: 
    default:
        return "Invalid Color";
    }
}

class Car 
{
public:

    Car()
    {
        std::cout << "Default ctor called \n";
        m_pColor = std::make_shared<Color>(Color::Invalid);
    }

    Car(const Color& color)
    {
        std::cout << "Parameterized ctor called \n";
        m_pColor = std::make_shared<Color>(color);
    }

    ~Car()
    {
        std::cout << "Dtor called \n";
    }

    Car(const Car& other)
    {
        std::cout << "Copy ctor called \n";
        if (other.m_pColor) {
            m_pColor = other.m_pColor;
        }
    }

    void operator=(const Car& other)
    {
        std::cout << "Overload assignment operator called \n";
        if (other.m_pColor) {
            if (m_pColor) {
                m_pColor = other.m_pColor;
            }
        }
    }

    void displayColor() const
    {
        if (m_pColor) {
            std::cout << "Car color is " << colorToStr(*m_pColor) << "\n";
        }
    }

    void refCnt() const
    {
        std::cout << "Ref count is " << m_pColor.use_count() << "\n";
    }

private:
    std::shared_ptr<Color> m_pColor;
};

int main()
{
    Car blackCar(Color::Black); // Parameterized ctor called
    blackCar.refCnt(); // 1

    Car anotherBlackCar = blackCar; //Copy ctor called
    anotherBlackCar.refCnt(); // 2 since copied

    Car thirdBlackCar; // Default ctor called
    thirdBlackCar.refCnt(); // 1 

    thirdBlackCar = anotherBlackCar; // overload assignment operator is called
    thirdBlackCar.refCnt(); // 3 since copied

    blackCar.displayColor();
    anotherBlackCar.displayColor();
    thirdBlackCar.displayColor();

    return 0;
}
```
Output:
```shell
bhavith@bhavith:$ g++ hello.cpp
bhavith@bhavith:$ ./a.out 
Parameterized ctor called 
Ref count is 1
Copy ctor called 
Ref count is 2
Default ctor called 
Ref count is 1
Overload assignment operator called 
Ref count is 3
Car color is Black
Car color is Black
Car color is Black
Dtor called 
Dtor called 
Dtor called 
```

**Note:** Observer how reference counts are increasing, It is also a shallow copy, but the life time of the shared pointer is when last object is destructed. That is the beauty of smart pointers