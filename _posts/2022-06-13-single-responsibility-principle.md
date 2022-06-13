---
layout: post
title: Single Responsibility Principle
date: 2022-06-13 07:57 +0530
tags: [c++, cpp, design-pattern]
categories: [design-pattern]
---

There are five design principles and one among them is SRP (Single Responsibility Principle). it stated that **Every class should have only one reason to change**

What does it means the **only one reason to change**? When ever we see this statement it sound simple but very much confused. 

Suppose I have a class and I want do modify it, At that particular moment, I have only one reason to change it and I am doing it what's the problem and what's the big deal?

That is where many people get confused. In order to make it simple, let me rephrase it in another way. **A class should be created to do only one kind of job not many**, So that, that class is **expertize** in doing that particular job.

As a real life example each individual is expert in one thing. For example plumber is very good in repairing the taps, TV repair guy know how to repair a TV. But plumber can learn the TV repair and he can repair the TV, but nobody believes plumber and give their TV to repair.

***Similarly in OOPs its better to make a class expert in one thing.***

Let's see the example 

```cpp
class Person
{
public:
    void repairTap();
    void repairTelevision();
};
```

Now in this above example, we can see `Person` is doing two jobs and it breaks the **SRP (Single Responsibility Principle)**. Its better to break and `Person` class into two classes namely `Plumber` and `TVTechnician` so each classes are expert in there own field. 

New example
```cpp
class Plumber
{
public:
    void repairTap();
    void repairValve();
    void fixPipeline();
};
```

```cpp
class TVTechnician
{
public:
    void repairTelevision();
    void fixTelevisionDisplay();
};
```

The splitting of the classes into their respective job is called **separation of concern**

So, if we want to change the class `Plumber` my main **reason** or **intent** is to do something with plumping not with TV. That is your single reason to update the class. 

Now, let see one more real time example.

```cpp
class Document
{
public:
    Document(const std::string& name)
        : m_name(name)
    {

    }

    void write(const std::string content)
    {
        m_content = content;
    }

    std::string read() const
    {
        return m_content;
    }

    void save(const std::string& fileName)
    {
        std::ofstream f(fileName, std::ofstream::out);
        if (f.is_open())
            f << m_content << "\n";
    }

private:
    std::string m_name = "Untitled";
    std::string m_content = "";
};

int main()
{
    Document d("Resume");
    d.write("This is my resume");
    d.save("resume.txt");

    return 0;
}
```
Output
```bash
bhavith@bhavith:$ g++ hello.cpp 
bhavith@bhavith:$ ./a.out 
bhavith@bhavith:$ cat resume.txt 
This is my resume
```

In the above example we can see that `Document` class is trying to do two jobs
- Operations w.r.t documents i.e. setting the name, writing and reading
- Operations w.r.t to saving the documents

Since `Document` class is doing two jobs it is **breaking SRP**.

Suppose in future if we want to add one more function called `saveAsPdf` then we are keep on adding those kind of functionalities to `Document` and again it's **breaking the SRP.**


The better way is to split the `Document` class into two, and make two experts to do their respective job correctly. One can be `Document` and another class can be `PersistentManager`

Let's see how new example looks now

```cpp
#include <iostream>
#include <string>
#include <fstream>

class Document
{
public:
    Document(const std::string& name)
        : m_name(name)
    {

    }

    void write(const std::string content)
    {
        m_content = content;
    }

    std::string read() const
    {
        return m_content;
    }

private:
    std::string m_name = "Untitled";
    std::string m_content = "";
};

class PersistentManager
{
public:
    void save(const Document& doc, const std::string& fileName)
    {
        save(DocumentType::TXT, doc, fileName);
    }

    void saveAsPdf(const Document& doc, const std::string& fileName)
    {
        save(DocumentType::PDF, doc, fileName);
    }

private:
    enum class DocumentType {TXT, PDF,};

    void save(const DocumentType docType, const Document& doc,
        const std::string& fileName) const
    {
        std::string fileNameWithExtension = fileName;
        switch (docType)
        {
        case DocumentType::PDF:
            fileNameWithExtension += ".pdf";
            break;
        case DocumentType::TXT:
            fileNameWithExtension += ".txt";
            break;
        default:
            break;
        }

        std::ofstream f(fileNameWithExtension, std::ofstream::out);
        if (f.is_open())
            f << doc.read() << "\n";
    }
};

int main()
{
    PersistentManager pm;

    Document doc("Resume");
    doc.write("This is my resume");

    pm.save(doc, "resume");
    pm.saveAsPdf(doc, "resume");
    return 0;
}
```

Output:
```bash
bhavith@bhavith:$ g++ hello.cpp 
bhavith@bhavith:$ ./a.out 
bhavith@bhavith:$ cat resume.txt 
This is my resume
bhavith@bhavith:$ cat resume.pdf 
This is my resume
```

Can you see now, how the responsibility is split into two. Now you have only one reason or having one expert to do better job. 

That is all about **Single Responsibility Principle**