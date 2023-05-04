---
title: "Starting with C"
date: 2023-04-28T19:00:00+02:00
description: "Starting with C"
menu:
  sidebar:
    name: "Starting with C"
    identifier: ls_part2
    parent: ls
    weight: 10
tags: ["C", "Linux", "Shell"]
categories: ["Rewriting Linux `ls` command"]
series: ["LS"]
draft: true
---

In this article we will start the first steps to rewrite `ls` command. If you want to know more about command, please read Part 1.

## The easy way
Actually, C provides a function to run any OS command, and print its output: [system](https://www.tutorialspoint.com/c_standard_library/c_function_system.htm). From the example in the reference, we see that, we can just take arguments from shell and pass it to that function, and it is done. 

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main(int argc, char *argv[])
{
    char command[50] = "ls ";

    for (int i = 1; i < argc; ++i)
    {
        strcat(command, argv[i]);
        strcat(command, " ");
    }

    system(command);
    return 0;
}

```

The first element of `argv` is always the name of executable, which we do not need, as we replace it with original `ls`. The rest of the arguments are copied to `command` variable, separated by spaces, and run. We get the following output:

![Output of snippet](system_output.png)


That's it, tutorial is done.

#### Just Kidding!
Of course, I am kidding! We will still write `ls` from scratch. However, this is the perfect way to test our code! If we compare the output of our command with this `system` function, we can be sure that we are on a right track.

## Writing unit tests
We will use [Unity Test Framework](http://www.throwtheswitch.org/unity) to do unit testing. It is one of widely used testing frameworks alongside with [Check](https://libcheck.github.io/check/), [Google Test](https://google.github.io/googletest/) etc. 

