---
title: PicoCTF - PIE TIME
published: 2025-08-07
description: PicoCTF Writeup for PIE TIME Challenge
tags: [binary exploitation, easy, picoCTF 2025]
category: PicoCTF Writeup
draft: false
lang: en
---

# PicoCTF Writeup - PIE TIME

PicoCTF Challenge: 
[https://play.picoctf.org/practice/challenge/490?category=6&page=1](https://play.picoctf.org/practice/challenge/490?category=6&page=1)

> ## PIE TIME
> Author: Darkraicg492
> 
> ### Description
> Can you try to get the flag? Beware we have PIE!<br>
> Additional details will be available after launching your challenge instance.<br>
> ### Hints
> 1st - Can you figure out what changed between the address you found locally and in the server output?<br>
> ### Resources:
> Source Code: [https://challenge-files.picoctf.net/c_rescued_float/1d01af98df77f5ba0339c7e7ba2031e95c3bcce1397dc3b60617dfcfe2e4c7be/vuln.c](https://challenge-files.picoctf.net/c_rescued_float/1d01af98df77f5ba0339c7e7ba2031e95c3bcce1397dc3b60617dfcfe2e4c7be/vuln.c)<br>
> Binary: [https://challenge-files.picoctf.net/c_rescued_float/1d01af98df77f5ba0339c7e7ba2031e95c3bcce1397dc3b60617dfcfe2e4c7be/vuln](https://challenge-files.picoctf.net/c_rescued_float/1d01af98df77f5ba0339c7e7ba2031e95c3bcce1397dc3b60617dfcfe2e4c7be/vuln)<br>

```bash
<!-- Server Information -->
$ nc rescued-float.picoctf.net 61719
```

## Analysis


```c
#include <stdio.h>
#include <stdlib.h>
#include <signal.h>
#include <unistd.h>

void segfault_handler() {
    printf("Segfault Occurred, incorrect address.\n");
    exit(0);
}

int win() {
    FILE *fptr;
    char c;

    printf("You won!\n");
    // Open file
    fptr = fopen("flag.txt", "r");
    if (fptr == NULL)
    {
        printf("Cannot open file.\n");
        exit(0);
    }

    // Read contents from file
    c = fgetc(fptr);
    while (c != EOF)
    {
        printf ("%c", c);
        c = fgetc(fptr);
    }

    printf("\n");
    fclose(fptr);
    }

    int main() {
    signal(SIGSEGV, segfault_handler);
    setvbuf(stdout, NULL, _IONBF, 0); // _IONBF = Unbuffered

    printf("Address of main: %p\n", &main);

    unsigned long val;
    printf("Enter the address to jump to, ex => 0x12345: ");
    scanf("%lx", &val);
    printf("Your input: %lx\n", val);

    void (*foo)(void) = (void (*)())val;
    foo();
}
```

