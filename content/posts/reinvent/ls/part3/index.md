---
title: "Listing and Sorting Files"
date: 2023-05-07T19:00:00+02:00
description: "Listing and Sorting Files"
menu:
  sidebar:
    name: "Listing and Sorting Files"
    identifier: ls_part3
    parent: ls
    weight: 10
tags: ["C", "Linux", "Shell"]
categories: ["Rewriting Linux `ls` command"]
series: ["LS"]
draft: true
---


> **In this article we will start the with some flags of `ls` command.**
> 
> **If you want to know more about command, please read Part 1.**
> 
> **If you want to know how we configured tests, check Part 2.**
>
> **Note:** Source codes can be found at: https://github.com/Miradils-Blog/linux-ls


## Getting the list of files

First things first, our file structure looks like this (`tree` command was used for output):

![File structure](file_structure.png)

We have separate folder with different types of files for us to cover all cases. For now, we will hard-code the folder where listing will happen, just focusing on functionality. So, how do we get the list of all files and their respective data about ownership, size, links etc.? Lucky for us, C provides function for this (and honestly, for everything else as well):

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <dirent.h>

int main(int argc, char *argv[])
{

    DIR *dir;            // for reading directory
    struct dirent *file; // struct for getting file data

    dir = opendir("folder");

    while ((file = readdir(dir)) != NULL)
    {
        printf("%s\n", file->d_name);
    }

    return 0;
}
```

The output would be:


![Output for code above](output1.png)

Guess what? Our flag `-1` is almost done! We just need to remove hidden files (files starting with character '.' are considered hidden files in Linux) from output. However, we will do that later. Let's analyze some flags and how they can be affecting each other (a.k.a. override).

## Analyzing Flags

First, we will look at flags that affect our printing style:
- **-C**: List entries in tabular format (by columns) (default)
- **-l:** Show enties in long list format
- **-m:** Show list as comma-separated values
- **-x:** List entries by lines
- **-1:** List one file per line

Each of these flags affect the output style. Moreover, they override each other: if our flag is `-lm`, `m` will override `l` and output will be comma-separated. Otherwise, if flag is `-ml`, output will be list, that is, one file per file with extra data. So, how can we handle this easily? Enums, right! We will create a new file `options.h` and store that enum there:

```c
typedef enum
{
    TABULAR_FORMAT,         // -C (default)
    LIST_FORMAT,            // -l, -1
    COMMA_SEPARATED_FORMAT, // -m
    ONE_LINE_FORMAT,        // -x

} print_style_t;
```

Okay, what's next? As we will have a lot of options/styles (show/hide hidden files, sort options, views etc.) depending on flags, let's group them into one object. Thus, we will create a struct to store all options after parsing flags. Right now, we will keep it minimal, and add more options as we process more flags:

```c
typedef struct
{
    print_style_t print_style;
    bool show_hidden_files;
    bool show_curr_prev_dirs;
} options_t;
```

Now, we can write parser for above mentioned flags:

```c
#include <options.h>
#include <stdio.h>

void parse_flags(char *flags[], int count, options_t *options)
{
  for (int i = 0; i < count; ++i)
  {
    if (flags[i][0] == '-') // it is a flag
    {
      while (*(++flags[i]))
      {
        switch (*flags[i])
        {
        case 'C':
          options->print_style = TABULAR_FORMAT;
          break;
        case 'l':
        case '1':
          options->print_style = LIST_FORMAT;
          break;
        case 'm':
          options->print_style = COMMA_SEPARATED_FORMAT;
          break;
        case 'x':
          options->print_style = ONE_LINE_FORMAT;
          break;
        default:
          break;
        }
      }
    }
  }
}
```
