---
layout: post
title: Simple way to implement Singleton design pattern without using pointers in c++
date: 2022-05-10 00:02 +0530
tags: [c++, cpp, design-pattern]
---

## A simple way to create a singleton design pattern

There are hell lot of example out there online, that how to create a singleton design pattern using `pointer` and `static` variable. But here I want to show you how can you create a singleton design pattern without even having a `pointer` in it. Looks amazing isn't it? 

Let's dive directly into the coding. let say we have `Student` class below and we want to make it as a singleton. I know this example is not a best example for singleton. For simplicity let's assume we want to make this `Student` class a singleton

```cpp
// Student.cpp
class Student
{
public:
    Student(const std::string& name) : m_name(name)
    {

    }
    ~Student()
    {
        m_name = "";
        m_books.clear();
    }

    void addBook(const std::string& bookName)
    {
        m_books.push_back(bookName);
    }

    void printAllBooks() const
    {
        for (const auto& book : m_books) {
            std::cout << book << "\n";
        }
    }

private:
    std::string m_name;
    std::vector<std::string> m_books {};
};

int main()
{
    Student s("Bhavith");
    s.addBook("Learn C++");
    s.addBook("For the love of physics");
    s.printAllBooks();
    return 0;
}
```

Now let's make this as a singleton

```cpp
// Student.cpp
class Student
{
public:

    static Student& instance(const std::string& name)
    {
        static Student instance(name);
        return instance;
    }

    void addBook(const std::string& bookName)
    {
        m_books.push_back(bookName);
    }

    void printAllBooks() const
    {
        for (const auto& book : m_books) {
            std::cout << book << "\n";
        }
    }

private:
    Student(const std::string& name) : m_name(name)
    {

    }
    ~Student()
    {
        m_name = "";
        m_books.clear();
    }
    std::string m_name;
    std::vector<std::string> m_books {};
};

int main()
{
    auto& s = Student::instance("Bhavith");
    s.addBook("Learn C++");
    s.addBook("For the love of physics");
    s.printAllBooks();
    return 0;
}
```

In the above example, we moved the constructor and the desctructor private, So that we removed the access to creation of `Student` object. In order to create a instance, we created an interace called `instance` which assist in creating the `Student` instance or object. 

This approach of creating the singleton is **thread-safe** because, by default `static` variable declaration is completely thread-safe in c++, especially after in modern-cpp (c++ onwards)

> Always go with this approach while making singleton design pattern, which is much more easier, simpler and thread-safe
{: .prompt-tip }

```cpp
static Student& instance(const std::string& name)
{
    static Student instance(name);
    return instance;
}
```

This way of implementing the singleton design pattern is called Meyers' singleton design pattern in simple

> **Spoiler alert:** What happen if object is been copied to other object using `=` operator, then it breaks the singleton design pattern. But how to achieve it is for future post.
{: .prompt-danger}

