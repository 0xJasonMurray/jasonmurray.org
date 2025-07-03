---
title: "Octal file permissions in Linux"
date: 2021-01-14T15:31:21-06:00
toc: false
images:
categories:
  - tech
tags: 
  - chmod
  - file
  - permissions
  - linux
  - filesystem
---

# GNU/Linux basic file permission overview

After creating a basic text file within a the GNU/Linux operating system:

```
jemurray@shell:~$ touch test
```

You may see a set of permission that look like this:

```
jemurray@shell:~$ ls -al test
-rw-r--r-- 1 jemurray jemurray 40 Jan 14 21:32 test
```

This section of the output above describes the type of file and the permissions associated with it:

```
-rw-r--r--
```

Broken out into individual components, each section has a specific meaning:

```
Type    User       Group       Others
-       rw-        r--         r--
```

- Type:
  - There are many different file types (complete  list is here `info ls -n "What information is listed"`).  The most common are:
  - `-` - Regular file
  - `d` - Directory
  - `l` - Link
- User:
  - Permissions for the owner of the file
- Group:
  - Permissions for users within the same `/etc/group` 
- Others:
  - Permissions for everyone else

Each one of the `-` in the User, Group, or Others permission set specifies the `none`, `read`, `write`, or `execute` permission set:

```
Read      Write       Execute
-         -           -
r         w           x
```

If the file type is a regular file indicated by a `-` in the first field:

- `r` = Read the file
- `w` = Write to the file
- `x` = Execute the file if it is a program or script

If the file type is a directory indicated by a `d` in the first field:

- `r` = Files can be read within the directory
- `w` = Files can be written or modified in the directory
- `x` = Files can be accessed if the name is know, but not viewed


# Changing permissions

The following command will change the `User`, `Group`, and `Other` permissions.  But what is the black magic behind the numbers?

```
chmod 664 test
```

Each number above adjusts the `User`, `Group`, and `Other` section.  The first `6` is for the `User`, the second `6` is for the `Group`, and the final `4` is for `Other`.  This is known as the octal notation permission format.


Each `-` from left to right is assigned a number:


```
User          Group           Other
4   2   1     4   2   1       4    2    1
-   -   -     -   -   -       -    -    -
r   w   x     r   w   x       r    w    x
```

By adding the number of each section together, individual permissions are set.  For example, to enable `read` and `write` for user and remove all other permissions add the numbers in the `user` column `4 + 2 = 6` and execute:

```
chmod 600 test
```

To set `read` and `write` for `user`, `group`, and `other` add each columns numbers `4 + 2 = 6` `4 + 2 = 6` `4 + 2 = 6`:

```
chmod 666 test
```

To set permission that look like this `rw-rw-r--` add the numbers `4 + 2 = 6` `4 + 2 = 6` `4 + 0 = 4`:

```
chmod 664 test
```

