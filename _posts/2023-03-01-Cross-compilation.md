---
title: "Cross-compilation"
date: 2023-03-01T18:32:30-00:00
categories:
  - blog
tags:
  - programming
  - tools
  - NanoPi
  - RaspberryPi
  - weather
  - IoT
  - ssh
  - IP
---

In this post I'm going to show you, how to run a program on NanoPi. The thing is, its processor has worse parameters than the one on my PC. This is why I need to apply cross compilation. It means I will install on my PC compiler that is compatible with NanoPi's processor. I am then going to write simple program in C that prints "Hello world"in the command line. Compilation will then produce executable file that will not work on my computer. In return, it should fly on NanoPi.

First things first.

We need to install compiler that works with board's processor - Cortex A53. It is sufficient to install compiler for ARM64 architecture - that should do. In order to save you some time here's the command you need. Of course you need to run this command with root priviliges.

```bash
sudo apt install gcc-aarch64-linux-gnu
```

Now I'm going to shine. I am going to write C program that prints Hello world :)

```c
#include<stdio.h>

int main () {

  printf("Hello world (\",)\n";

  return 0;

}
```
What I do next is I use my new ARM64 compiler to produce executable file for NanoPi:

```bash 
aarch64-linux-gnu-gcc -mcpu=cortex-a53 crosscompilation.c -o hello_arm
```
Now I have program `hello_arm` that should work on NanoPi. All that's left is copying it to the board. In order to this I am using SSH.

I am using command `scp` what stands for copy via SSH

```bash
scp hello_arm pi@192.168.1.3:
```
I receive  information that `hello_arm` has been copied to NanoPi. Semicolon at the end of the above command opens `home` section of the board.

Let's check how NanoPi performs.

I'm connecting with the board:

```bash
ssh pi@192.168.1.3
```

After typing password I can explore the contents of NanoPi.

```bash
pi@FriendlyElec: ls
hello_arm
./hello_arm

Hello world! (",) 
```

Yes, I couldn't resist to put this emoji at the end :)


