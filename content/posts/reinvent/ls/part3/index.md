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
> **Note:** Source codes can be found at: <https://github.com/Miradils-Blog/linux-ls>

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
- **-g:** Same as `-l`, but do not show owner.
- **-l:** Show enties in long list format
- **-m:** Show list as comma-separated values
- **-n:** Same as `-l`, but show owner and group ID
- **-x:** List entries by lines
- **-1:** List one file per line

Each of these flags affect the output style. Moreover, they override each other: if our flag is `-lm`, `m` will override `l` and output will be comma-separated. Otherwise, if flag is `-ml`, output will be list, that is, one file per file with extra data. So, how can we handle this easily? Enums, right! We will create a new file `options.h` and store that enum there:

```c
typedef enum
{
    TABULAR_FORMAT,         // -C (default)
    LIST_FORMAT,            // -g, -l, -n, -1
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
      while (*(++flags[i])) // iterate through characters/flags
      {
        switch (*flags[i])
        {
        case 'C':
          options->print_style = TABULAR_FORMAT;
          break;
        case 'g':
        case 'l':
        case 'n':
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

We can see that, `print_style` according to the "latest" flag. Moreover, `1` and `l` has the same format, but latter shows extra info about all files. We will add more options later as we progress. We also do not need to analyze print function, as it is just handling if statements. You can check code HEREEEEEEEEE

## Sort flags

Similar to printing style, there are multiple flags which define sorting, and they too, override each other:

- **-c:** Sort entries by their "last modification time"
- **-f:** List all files in their order in directory, without any colors
- **-S:** Sort file by size, largest first
- **-t:** Sort by time, newest first
- **-u:** Sort and show access time. Newest first.
- **-U:** Do not sort, list entries in directory order

Thus, we need another enum to store our sorting option:

```c
typedef enum
{
    NO_SORT,      // -f, -U   
    BY_MODIFICATION_TIME,  // -c          
    BY_SIZE, // -S
    BY_CREATION_TIME, // -t
    BY_ACCESS_TIME // -u
} sort_option_t;
```

By default, every sorting is in descending order, unless `-r` flag is set. We will just have boolean for storing the order of sort. If we add this enum to our `options` struct, and change `parse_flags` function:

```c
#include <options.h>
#include <stdio.h>

void parse_flags(char *flags[], int count, options_t *options)
{
  for (int i = 0; i < count; ++i)
  {
    if (flags[i][0] == '-') // it is a flag
    {
      while (*(++flags[i])) // iterate through characters/flags
      {
        switch (*flags[i])
        {
        case 'C':
          options->print_style = TABULAR_FORMAT;
          break;
        case 'g':
        case 'l':
        case 'n':
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
