---
layout: post
title: Never forget to delete an array of object using delete[]
date: 2022-05-21 11:24 +0530
tags: [c++, cpp, pointers]
categories: [c++ or cpp]
---

Never forget to delete an array of object created in heap using `delete[]`.

If you forget to do so its really hard to debug especially if you are doing this one in some function some where is your source code and execution hits this delete on some particular condition

As usual let's begin with example
```cpp
class Test
{
public:
    Test()
    {
        std::cout << "Ctor called\n";
    }

    ~Test()
    {
        std::cout << "Dtor called\n";
    }
};

int main()
{
    Test* p = new Test[2]; //(1)

    delete p; //(2)

    return 0;
}
```

Here in this above example, `Test* p` is a pointer pointing to an array of Test objects. In next line we can see `delete p` is used. When we try to compile this source, compiler does not give an error rather it thrown some warnings. If we ignore this warning then we have a pay it for later.

Let's compile it
```bash
bhavith@bhavith:~$ g++ main.cpp 
main.cpp: In function ‘int main()’:
main.cpp:21:12: warning: ‘void operator delete(void*, std::size_t)’ called on pointer ‘<unknown>’ with nonzero offset 8 [-Wfree-nonheap-object]
   21 |     delete p;
      |            ^
main.cpp:19:25: note: returned from ‘void* operator new [](std::size_t)’
   19 |     Test* p = new Test[2];

```

From the above compiler message we can observer that there is some function like `void* operator new[](std::size_t)` which we are calling on `(1) ` i.e. `new Test[2]` that means we are calling an operator `[]` with size. Then this function returning an pointer i.e. `void*` and we saving to variable `p` 

The second warning compiler is complaining is, there is a function `void operator delete(void*, std::size_t)` and we are calling it which we are not suppose to call.

In simple words call, `operator` function associate with `delete` operator not direct `delete`. 

Let's see what happen when we execute the code
```bash
bhavith@bhavith:~$ ./a.out 
Ctor called
Ctor called
Dtor called
munmap_chunk(): invalid pointer
Aborted (core dumped)
```
Did you see that? the application crashed and this is undefined behvaiour many of times 

Let's use `gdb` to debug it

```bash
bhavith@bhavith:$ gdb a.out 
GNU gdb (Ubuntu 12.0.90-0ubuntu1) 12.0.90
Copyright (C) 2022 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<https://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from a.out...
(No debugging symbols found in a.out)
(gdb) r
Starting program: /home/bhavith/Bhavith/Development/blogs/a.out 
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
Ctor called
Ctor called
Dtor called
munmap_chunk(): invalid pointer

Program received signal SIGABRT, Aborted.
__pthread_kill_implementation (no_tid=0, signo=6, threadid=140737348162496) at ./nptl/pthread_kill.c:44
44	./nptl/pthread_kill.c: No such file or directory.
(gdb) bt
#0  __pthread_kill_implementation (no_tid=0, signo=6, threadid=140737348162496) at ./nptl/pthread_kill.c:44
#1  __pthread_kill_internal (signo=6, threadid=140737348162496) at ./nptl/pthread_kill.c:78
#2  __GI___pthread_kill (threadid=140737348162496, signo=signo@entry=6) at ./nptl/pthread_kill.c:89
#3  0x00007ffff7b77476 in __GI_raise (sig=sig@entry=6) at ../sysdeps/posix/raise.c:26
#4  0x00007ffff7b5d7f3 in __GI_abort () at ./stdlib/abort.c:79
#5  0x00007ffff7bbe6f6 in __libc_message (action=action@entry=do_abort, fmt=fmt@entry=0x7ffff7d10b8c "%s\n") at ../sysdeps/posix/libc_fatal.c:155
#6  0x00007ffff7bd5d7c in malloc_printerr (str=str@entry=0x7ffff7d13230 "munmap_chunk(): invalid pointer") at ./malloc/malloc.c:5664
#7  0x00007ffff7bd605c in munmap_chunk (p=<optimized out>) at ./malloc/malloc.c:3060
#8  0x00007ffff7bda51a in __GI___libc_free (mem=<optimized out>) at ./malloc/malloc.c:3381
#9  0x000055555555527a in main ()
(gdb)
```

From the above debug message we can see program received `SIGABRT` i.e. abort signal from the kernel and if you back trace it, you can see that call trace is pointing from function `main`, and abort aroses when control reaches `__GI___libc_free` function.

**Moral of the story:**

Never delete the pointer pointing to any array of object like a regular pointer rather use `delete[] p`

Working source 
```cpp
int main()
{
    Test* p = new Test[2];

    delete[] p; //Use this rather delete p

    return 0;
}
```
Compile and rum
```bash
bhavith@bhavith:~$ g++ main.cpp
bhavith@bhavith:~$ ./a.out 
Ctor called
Ctor called
Dtor called
Dtor called
```
