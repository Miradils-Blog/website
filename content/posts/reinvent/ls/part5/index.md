---
title: "Formatting the output"
date: 2023-09-01T10:00:00+02:00
description: "Formatting the output"
menu:
  sidebar:
    name: "Formatting the output"
    identifier: ls_part5
    parent: ls
    weight: 50
tags: ["C", "Linux", "Shell", "File System"]
categories: ["C", "Linux"]
series:
  - rewrite_ls
draft: true
---

> **In this article we will apply flags from previous post and format output `ls` command.**
>
> **If you want to know more about command, please read Part 1.**
>
> **If you want to know how we configured tests, check Part 2.**
>
> **If you want to know how we collected info about files, check Part 3.**
> 
> **If you want to know how we processed the flags, check Part 4**
>
> **Note:** Source codes can be found at: <https://github.com/Miradils-Blog/linux-ls>

## What is done and what is left

In [previous part](../part4/) we parsed all flags and stored them. Now, it is time to apply those flags, and get nice output, as expected! However, we still need to consider some details:

1.  To find the shell width, so, for tabular output, we calculate how many columns we have
2.  To get max length of each column, so, column size is dynamic to longest row

### 1. Get the shell width

Lucky for us, C already provides function for us to get shell width: `ioctl` with `TIOCGWINSZ`. Let's get this value, and store it in struct:

```
typedef struct
{
    int win_size;
    
} widths_t;


/////// IN DIFFERENT FILE ///////

#include <sys/ioctl.h>

int get_window_size()
{
    struct winsize w;
    ioctl(0, TIOCGWINSZ, &w);
    return w.ws_col;
}

widths_t widths;
widths.win_size = get_window_size();

```

### 2. Store max width of each column
You remember our main loop? It looks like this:

```
while ((file = readdir(dir)) != NULL)
{
    lstat(file->d_name, (struct stat *)&files[i]);

    strncpy(files[i].name, file->d_name, 256);
    get_username_from_uid(files[i].st_uid, files[i].owner_name);
    get_groupname_from_gid(files[i].st_gid, files[i].group_name);
    construct_permission_str(files[i].st_mode, files[i].permission);
    get_file_extension(files[i].name, files[i].extension);
    get_color(files[i].permission, files[i].extension, files[i].color);
    
    files[i].indicator = get_indicator(files[i].permission);
    ++i;
}
```

For each file, we can get length of each attribute, and of course, store it in our above defined strcut. So, our struct and §loop will grow bigger:

```
while ((file = readdir(dir)) != NULL)
{
    lstat(file->d_name, (struct stat *)&files[i]);

    strncpy(files[i].name, file->d_name, 256);
    get_username_from_uid(files[i].st_uid, files[i].owner_name);
    get_groupname_from_gid(files[i].st_gid, files[i].group_name);
    construct_permission_str(files[i].st_mode, files[i].permission);
    get_file_extension(files[i].name, files[i].extension);
    get_color(files[i].permission, files[i].extension, files[i].color);

    files[i].indicator = get_indicator(files[i].permission);
    
    widths.inode_width = max(widths.inode_width, NUM_LEN(files[i].d_ino));
    widths.nlink_width = max(widths.inode_width, NUM_LEN(files[i].st_nlink));
    widths.ownerid_width = max(widths.ownerid_width, NUM_LEN(files[i].st_uid));
    widths.ownername_width = max(widths.ownername_width, strlen(files[i].owner_name));
    widths.groupid_width = max(widths.groupid_width, NUM_LEN(files[i].st_gid));
    widths.groupname_width = max(widths.groupname_width, strlen(files[i].group_name));
    widths.block_width = max(widths.block_width, NUM_LEN(files[i].st_size));
    
    ++i;
}
```
