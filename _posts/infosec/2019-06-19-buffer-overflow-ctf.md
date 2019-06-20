---
layout: post
title:  "Buffer Overflow CTF writeups"
date:   2019-06-19 21:35:15 +0800
categories: infosec
---
> Buffer Overflow Writeups

*Due to the on-going nature of the CTF, I will be avoiding keywords related to the problem or hint the answer out of respect for the competition.*

## Buffer Overflow Rundown

> Terminologies: Buffer, Stack, Memory Addresses

### How memory works in a program

In a program, memory is divided as such:
1. Heap (Dynamic memory - for pointers)
2. Stack (Function calls, Local Vars)
3. Static/Global (Global Vars)
4. Code

- When a program is compiled and executed, each instruction has a hexadecimal byte memory address assigned to it.

- The buffer overflow exploit lies in the stack.

### Buffer + Overflow

*Example Code:*
```cpp
#include <iostream>
int main() {
  char name[20]; //1
  cout << "Enter your name" << endl; //2
  cin >> name; //3
  cout << "Your name is " << name << endl; //4
  return 0; //5
}
```
Given the above sample program, the *name* we enter will be stored in a buffer - ``` char name[20] ``` (line 3) and retrieved from the buffer (line 4). 

Specifically, this buffer stores 20 chars. Once the length of our input exceeds 20 chars, the allocated buffer area is *overflowed*. This usually results in a segfault...

## How is buffer overflow dangerous?

At first sight, making the program crash does not seem particularly useful. However, note that the program will also write data outside of the allocated buffer area.

A buffer overflow is particularly dangerous when the program is a *setuid* binary. Basically, the program allows the user to run with the capabilities of *root*. Think of it like *sudo*. By passing a buffer, we can overwrite the program with a payload that executes a system call and spawns a root shell.