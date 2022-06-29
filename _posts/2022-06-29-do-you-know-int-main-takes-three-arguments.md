---
layout: post
title: Do you know int main takes three arguments
date: 2022-06-29 22:00 +0530
tags: [c++, c, linux]
categories: [c]
---

Many of us know how to read command line arguments from the program in C/C++ 

```cpp
#include <iostream>

int main(int argc, char** pArgv)
{
    for (int i = 0; i < argc; i++) {
        std::cout << i << " - " << pArgv[i] << "\n";
    }

    return 0;
}
```
Output:

```bash
bhavith@bhavith:$ ./a.out first second 
0 - ./a.out
1 - first
2 - second
```

But do you know int main takes one more argument, i.e. pointer to an list of environment variable

```cpp
#include <iostream>

int main(int argc, char** pArgv, char** pEnv) {
    
    char** pTmp = pEnv;

    while (*pTmp != nullptr) {
        std::cout << *pTmp << "\n";
        pTmp++;
    }
    
    std::cout << "End of the list \n";
    return 0;
}
```

I ran the program in [https://www.programiz.com/cpp-programming/online-compiler/](https://www.programiz.com/cpp-programming/online-compiler/) and this is the output :) 

Output:

```bash
KUBERNETES_PORT=tcp://10.0.0.1:443
KUBERNETES_SERVICE_PORT=443
PROGRAMIZ_COMPILER_WEB_UI_SEVICE_PORT_80_TCP_ADDR=10.0.14.233
HOSTNAME=programiz-compiler-deployment-755649f487-8zgzd
PROGRAMIZ_COMPILER_WEB_UI_SEVICE_PORT_80_TCP_PORT=80
HOME=/home/compiler
PROGRAMIZ_COMPILER_WEB_UI_SEVICE_PORT_80_TCP_PROTO=tcp
PROGRAMIZ_COMPILER_SERVICE_HOST=10.0.10.151
PS1=
PROGRAMIZ_COMPILER_WEB_UI_SEVICE_PORT_80_TCP=tcp://10.0.14.233:80
PROGRAMIZ_COMPILER_SERVICE_PORT=80
PROGRAMIZ_COMPILER_PORT=tcp://10.0.10.151:80
TERM=xterm
KUBERNETES_PORT_443_TCP_ADDR=10.0.0.1
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
PROGRAMIZ_COMPILER_PORT_80_TCP_ADDR=10.0.10.151
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_PROTO=tcp
PROGRAMIZ_COMPILER_PORT_80_TCP_PORT=80
PROGRAMIZ_COMPILER_PORT_80_TCP_PROTO=tcp
PROGRAMIZ_COMPILER_WEB_UI_SEVICE_SERVICE_HOST=10.0.14.233
GCC_COLORS=
KUBERNETES_PORT_443_TCP=tcp://10.0.0.1:443
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_SERVICE_HOST=10.0.0.1
PWD=/app
PROGRAMIZ_COMPILER_PORT_80_TCP=tcp://10.0.10.151:80
PROGRAMIZ_COMPILER_WEB_UI_SEVICE_SERVICE_PORT=80
PROGRAMIZ_COMPILER_WEB_UI_SEVICE_PORT=tcp://10.0.14.233:80
End of the list 
```