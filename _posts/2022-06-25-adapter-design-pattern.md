---
layout: post
title: Adapter design pattern
date: 2022-06-25 06:59 +0530
tags: [c++, cpp, design-pattern]
categories: [design-pattern]
---
### Definition: Adapter is a structural design pattern that allows objects with incompatible interfaces to collaborate.

You may have browsed already many other places and might have watched lot of videos in order to understand it, lot of places there are N number of examples and you may fed up understanding it or might be confused especially if you are new to design patterns. Since design patterns are programming language agnostic. Each of the online tutorial uses different programming languages. Most of the times you end up seeing examples on Java or C#. 

In this post I am going to explain about Adapter design pattern using some story and I use C++ programming language.

You are working in a UI development team for a fitness application. End product is a small hand held display especially made for software developer (lol), where he can click on developer option and can see all activities in XML format


We have two character in this story
- Srinivas (Manager)
- Bhavith (Developer)
  
**Srinivas (Manager):** Hey Bhavith, could you please develop a developer mode for our activity screen, where user can toggle developer option and can see all the activity in XML format?

**Bhavith (Developer):** Yes Srini, I do some analysis and get back to you tomorrow. 

**Bhavith:** On next day, Hey Srini, Since we have less time and cannot develop XML parser ourself due to time constraint, So I am planning to use 3rd party library for XML parser is that fine with you?

**Srinivas:** Yes, please go ahead since we are near to release and don't reinvent the wheel, go ahead and use 3rd party

**Bhavith:** Again after few hours, Srini, I collected all available XML parser library and found that `JacksonXmlParser` supports for C++ which is very suits best for our use case. But there is one catch we need to pay a little amount for that 

**Srinivas:** How much?

**Bhavith:** It's not too high, **$5** one time. 

**Srinivas:** Hey, please use our corporate credit card and pass over the bill via email. Rest I gonna take care. 

**Bhavith:** Next morning will full josh, Let's start coding.

```cpp
const std::string& dummyXml =         
        "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n"\
        "<Agenda type=\"gardening\">\n"\
        " <Activity type=\"Watering\">\n"\
        "  <golf-course time=\"6:00\"/>\n"\
        "  <yard time=\"7:00\"/>\n"\
        " </Activity>\n"\
        " <Activity type=\"cooking\">\n"\
        "  <lunch time=\"12:00\"/>\n"\
        " </Activity>\n"\
        "</Agenda>\n";

/// Jackson parser library pseducode
class JacksonXmlParser
{
public:
    JacksonXmlParser(const std::string xmlFile)
        : m_xmlFile(xmlFile)
    {

    }

    std::string jacksonParseXml()
    {
        return dummyXml;
    }

private:
    std::string m_xmlFile = "";
};

class Renderer
{
public:
    void renderXmlData()
    {
        JacksonXmlParser jx("data.xml");
        const auto data = jx.jacksonParseXml();
        std::cout << data << "\n";
    }
};

int main()
{
    Renderer renderer;
    renderer.renderXmlData();

    return 0;
}
```
Output:
```bash
bhavith@bhavith:$ ./a.out 
<?xml version="1.0" encoding="UTF-8"?>
<Agenda type="gardening">
 <Activity type="Watering">
  <golf-course time="6:00"/>
  <yard time="7:00"/>
 </Activity>
 <Activity type="cooking">
  <lunch time="12:00"/>
 </Activity>
</Agenda>
```

**Bhavith:** Srini, can you please look at this output? 

**Srinivas:** Woow, beautiful this is what I expected. great man keep it up

Now developers are happy and customer is happy. Every thing was going smooth. One fine day, there was a email from `jacksonXmlParser`

```
From: jackson@jacksonxmlparser.com

Hey Bhavith,
    Hope you are doing great and enjoying our JacksonXmlParser library. 
    we have some news for you. Since our company is no more Jackson 
    and we are now a part of Michael group. 

    From next year onwards, our pricing plans have changed, Now on there
    won't be any one time subscription, rather annual subscription and 
    the annual subscription price is $150.

    Since you are our loyal customer we have a offer and you can pay just $100.

Regards
Team Michael (Former Jackson)
```

**Bhavith:** Damn, how could they do these things to us, they would have told at the time when we purchased

Bhavith went to Srinivas 

**Bhavith:** Hey Srini, We need to move to annual subscription plan for our Jackson library. Can I use corporate
credit card to make payment 

**Srinivas:** No, please wait. Since we don't have enough budget to afford such a big amount and that to every year. Also what if they change the plan next year and ask us to pay $250. Please think of something else.

**Bhavith:** Browsed entire day and found one more library at the end of the google search called `OpenXmlParser` and surprisingly it was an open source library

**Bhavith**: Hey Srini, I have a good news for you, there was a open source project running from last year on Xml parser and it is completely free, why can't we incorporate that in our project 

**Srinivas:** Sounds, cool please go ahead once we deliver the product to customer, we can contribute to `OpenXmlparser` as well. Good job!

Bhavith is now happy and looked into a API's. He found there was a change in signature and he wan't to reimplement the solution. Hey came to office on weekends and tagged current version to `jackson-xml` and reimplemented the solution

```cpp
class OpenXmlParser
{
public:
    std::string openParser(const std::string& xmlFile)
    {
        return dummyXml;
    }
};

class Renderer
{
public:
    void renderXmlData()
    {
        OpenXmlParser openXmlParser;
        const auto data = openXmlParser.openParser("data.xml");
        std::cout << data << "\n";
    }
};

int main()
{
    Renderer renderer;
    renderer.renderXmlData();

    return 0;
}
```
Output:
```bash
bhavith@bhavith:$ ./a.out 
<?xml version="1.0" encoding="UTF-8"?>
<Agenda type="gardening">
 <Activity type="Watering">
  <golf-course time="6:00"/>
  <yard time="7:00"/>
 </Activity>
 <Activity type="cooking">
  <lunch time="12:00"/>
 </Activity>
</Agenda>
```

**Bhavith:** Srini, look at this, we got the same output as we expected.
**Srinivas:** Good job, Bhavith, please take 20% hike this time !

> After few day, end user started getting bugs, XML was jumbled up one on another. Finally testing team figured out there is a bug on XML rendering. Now developer reproduced it and found that there is a defect in xml parser and also there is open issue going on `OpenXmlParser`
{: .prompt-danger }

Now again **Srinivas** came to **Bhavith** and asked him, **Bhavith** please revert back the xml parser to `JacksonXmlParser` and I will pay it as of now, we cannot lose customer. 

Now Bhavith has to rewrite the whole software again, and that to everything he has to revert back. During the development again **Srinivas** came back to **Bhavith** and asked him to support both, We give paid customer with `JacksonXmlParser` and `OpenXmlparser` to free users. 

Now Bhavith is completely in soup and changes his status to **open to work** in Linkedin. But his friend came to him and asked, what is the problem.

The problem is both library has different interfaces
```cpp
class OpenXmlParser
{
public:
    std::string openParser(const std::string& xmlFile);
};
```

and 

```cpp
class JacksonXmlParser
{
public:
    JacksonXmlParser(const std::string xmlFile);
    std::string jacksonParseXml();
private:
    std::string m_xmlFile = "";
};
```

**Bhavith:** See bro here, both the classes have different signature to parse, how can I solve this one. I cannot create two executable for two different customers 

**Loyal friend:** Bro, just create your interface to prase and create a signature how ever you want, and pass to me. I gonna solve it 

Now Bhavith created a interface for XML parser, how he wants.

```cpp
class IXmlParser
{
public:
    virtual std::string parseXml(const std::string& xmlFile) = 0;
};


```

**Bhavith:** Bro, look at here, I created one you asked, Please help me.

**Loyal friend** took some time and refactor the code

```cpp
class JacksonXmlAdapter : public IXmlParser
{
public:
    std::string parseXml(const std::string& xmlFile) override
    {
        JacksonXmlParser xmlParser(xmlFile);
        return xmlParser.jacksonParseXml();
    }
};

class OpenXmlParserAdapter : public IXmlParser
{
public:
    std::string parseXml(const std::string& xmlFile) override
    {
        OpenXmlParser xmlParser;
        return xmlParser.openParser(xmlFile);
    }
};


class Renderer
{
public:
    enum class Type
    {
        Jackson,
        OpenXml,
    };

    void renderXmlData(Type type)
    {
        std::shared_ptr<IXmlParser> pXmlparser = nullptr;
        std::string data("Invalid data");
        if (type == Type::Jackson) {
            std::cout << "Debug: Parsing xml using jackson parser ...\n\n"; 
            pXmlparser = std::make_shared<JacksonXmlAdapter>();
        } else {
            std::cout << "Debug: Parsing xml using open xml parser ...\n\n";
            pXmlparser = std::make_shared<OpenXmlParserAdapter>();
        }
        
        if (pXmlparser) {
            data = pXmlparser->parseXml("data.xml");
        }

        std::cout << data << "\n";
    }
};


int main(int argc, char** pArgv)
{
    Renderer::Type renderType = Renderer::Type::OpenXml;
    if (argc == 1) {
        std::cout << "Invalid usage: \n";
        std::cout << "Usage: " << pArgv[0] << " - <type>\nWhere type:\n0 - To parse using Jackson parser \n";
        std::cout << "1 - To parse using open xml parser \n";
        return EXIT_FAILURE;
    }

    const int type = std::atoi(pArgv[1]);
    if (type == 0) {
        renderType = Renderer::Type::Jackson;
    }

    Renderer renderer;
    renderer.renderXmlData(renderType);

    return 0;
}
```
Output:
```bash
bhavith@bhavith:$ ./a.out 
Invalid usage: 
Usage: ./a.out - <type>
Where type:
0 - To parse using Jackson parser 
1 - To parse using open xml parser

bhavith@bhavith:$ ./a.out 0
Debug: Parsing xml using jackson parser ...

<?xml version="1.0" encoding="UTF-8"?>
<Agenda type="gardening">
 <Activity type="Watering">
  <golf-course time="6:00"/>
  <yard time="7:00"/>
 </Activity>
 <Activity type="cooking">
  <lunch time="12:00"/>
 </Activity>
</Agenda>

bhavith@bhavith:$ ./a.out 1
Debug: Parsing xml using open xml parser ...

<?xml version="1.0" encoding="UTF-8"?>
<Agenda type="gardening">
 <Activity type="Watering">
  <golf-course time="6:00"/>
  <yard time="7:00"/>
 </Activity>
 <Activity type="cooking">
  <lunch time="12:00"/>
 </Activity>
</Agenda>

```

Carefully look at the code, How Bhavith's friend refactor the code and make it work for both uses cases, Just we have to pass a command line argument to use which ever the XML parser we want.

This is what adapter design is for.

Here we have two adapter's 
- JacksonXmlAdapter
- OpenXmlParserAdapter

Also we have two adoptee's 
- JacksonXmlParser
- OpenXmlParser

> His friend just create a wrapper, that wrapper we call as `Adapter` and he wrapped some thing, that some thing is called `Adoptee`. 
{: .prompt-tip }

Now on **Bhavith** can use N number of xml parsers and his application scales up drastically. That is the beauty of Adapter design pattern

### When can I choose Adapter design pattern?
> It is very simple, Thumb rule is, When ever there you want to work with 3rd party library in your software, close your eyes and go ahead and implement Adapter design pattern
> 
3rd party always no need to be an external library, it could be a piece of code, where you feel interface may keep change in future
{: .prompt-tip }

If you really like the post please do the following
- You can connect me on LinkedIn [LinkedIn](https://www.linkedin.com/in/bhavithc/)
- Subscribe to my youtube channel [Youtube](https://www.youtube.com/c/BhavithC)


#### Bhavith C
Passionate Programmer and Teacher


