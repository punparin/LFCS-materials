# Essential Commands


## man
> Show manual
```
$ man man

...
The table below shows the section numbers of the manual followed by the types of pages they contain.

1   Executable programs or shell commands
2   System calls (functions provided by the kernel)
3   Library calls (functions within program libraries)
4   Special files (usually found in /dev)
5   File formats and conventions, e.g. /etc/passwd
6   Games
7   Miscellaneous (including macro packages and conventions), e.g. man(7), groff(7)
8   System administration commands (usually only for root)
9   Kernel routines [Non standard]
...

$ man 3 printf
...
PRINTF(3)                                                 Linux Programmer's Manual                                                 PRINTF(3)

NAME
       printf, fprintf, dprintf, sprintf, snprintf, vprintf, vfprintf, vdprintf, vsprintf, vsnprintf - formatted output conversion
...
```

## apropos
> Search keyword in manual

```
$ apropos directory

alphasort (3)        - scan a directory for matching entries
basename (1)         - strip directory and suffix from filenames
bindtextdomain (3)   - set directory containing message catalogs$
...

# Find based on section
$ apropos -s 1 directory
basename (1)         - strip directory and suffix from filenames
dbus-cleanup-sockets (1) - clean up leftover sockets in a directory
...

# To create db manually in case of `nothing appropriate` warning
$ sudo mandb
```

## ls
> list directory content

```
# all
$ ls -a
./  ../  .bash_history  .bashrc

# long-listing format
$ ls -l

total 4
drwxr-xr-x 2 root root 4096 Apr 28 10:06 scripts/

# human readable format
$ ls -lah
total 48K
drwx------  6 root root 4.0K Apr 28 10:10 ./
```

## ln
> make links between files

```
# hard link
$ ln file1 file2

# soft link
$ ln -s file1 file2

# check links
$ ls -l
total 4
lrwxrwxrwx 1 punpa punpa    5 Aug 10 01:40 file2 -> file1
```

# File permissions

> `-rwxrw-rwx`

> meaning `-[rwx][rw-][rwx]`

- [1] = user (u)
- [2] = group (g)
- [3] = others (o)

```
# Change group
$ ls -l
-rw-r--r-- 1 root root 0 Aug 10 20:51 test

$ chgrp group1 test

$ ls -l
-rw-r--r-- 1 root group1 0 Aug 10 20:51 test
```

```
# Change owner

$ ls -l
-rw-r--r-- 1 root root 0 Aug 10 20:51 test

$ sudo chown john test

$ ls -l
-rw-r--r-- 1 john root 0 Aug 10 20:51 test
```

```
# Add permissions

$ ls -l
-rw-r--r-- 1 root root 0 Aug 10 20:51 test

$ chmod u+x test

$ ls -l
-rwxr--r-- 1 root root 0 Aug 10 20:51 test
```

```
# Remove permissions

$ ls -l
-rwxr--r-- 1 root root 0 Aug 10 20:51 test

$ chmod g-r test

$ ls -l
-rwx---r-- 1 rootroot root 0 Aug 10 20:51 test
```

```
# Set exact permissions

$ ls -l
-rwxr--r-- 1 root root 0 Aug 10 20:51 test

$ chmod g=rw test

$ ls -l
-rwxrw-r-- 1 root root 0 Aug 10 20:51 test
```

```
# Chaining permissions

$ ls -l
-rwxr--r-- 1 root root 0 Aug 10 20:51 test

$ chmod u-rw,g=w,o= test

$ ls -l
---x-w---- 1 root root 0 Aug 10 20:51 test
```

```
# Octal permissions

$ ls -l
-rwxr--r-- 1 root root 0 Aug 10 20:51 test

$ chmod 777 test

$ ls -l
-rwxrwxrwx 1 root root 0 Aug 10 20:51 test
```

## SUID

> Set user ID bit to execute file using the owner permission

```
$ ls -l
-rw-r--r-- 1 root root 0 Aug 10 20:51 test

$ chmod 4777 test

# lower s if owner has execute premission
$ ls -l
-rwsrwxrwx 1 root root 0 Aug 10 20:51 test*

$ chmod 4666 test

# Capital S if owner doesn't have execute premission
$ ls -l
-r-Sr--r-- 1 root root 0 Aug 10 20:51 test
```

## SGID

> Set group ID bit to execute file using the group permission

```
$ ls -l
-rw-r--r-- 1 root root 0 Aug 10 20:51 test

$ chmod 2777 test

# lower s if group has execute premission
$ ls -l
-rwxrwsrwx 1 root root 0 Aug 10 20:51 test*

$ chmod 2666 test

# Capital S if group doesn't have execute premission
$ ls -l
-rw-rwSrw- 1 root root 0 Aug 10 20:51 test
```

## Sticky bit
> To only enable owner to remove file

```
$ ls -l
-rw-r--r-- 1 root root 0 Aug 10 20:51 test

$ chmod +t test

# capital T if owner doesn't have execute premission
$ ls -l
-rw-r--r-T 1 root root 0 Aug 10 20:51 test

$ chmod 1777 test

# lower t if owner has execute premission
$ ls -l
-rwxrwxrwt 1 root root 0 Aug 10 20:51 test*
```

## find
> search for files

```
# ls -l
-rwxr-xr-x 1 root root 7 Aug 10 22:55 file1.txt*
-rw-r--rw- 1 root root 0 Aug 10 22:55 file2.txt

$ find -name file1.txt
./file1.txt

$ find temp/ -name file1.txt
./file1.txt

$ find temp/ -name "file*"
temp/file1.txt
temp/file2.txt

# find file modified 5 mins ago
$ find temp/ -mmin -5
temp/file1.txt

# find file modified in the last 5 min
$ find temp/ -mmin -5
temp/
temp/file1.txt
temp/file2.txt

# find file modified before 5 min ago
$ find temp/ -mmin +5

# not operator
$ find temp/ -not -name file1.txt
temp/
temp/file2.txt

# find only file type
$ find temp/ -type f
temp/file1.txt
temp/file2.txt

# find perm with exact match
$ find temp/ -type f -perm 646
temp/file2.txt

# find perm with at least perm
$ find temp/ -type f -perm -u=rw,g=r
temp/file1.txt
temp/file2.txt

# find perm with any of the perms
$ find temp/ -type f -perm /o=x
temp/file1.txt

# find file with size 7 bytes
$ find temp/ -type f -size 7c
temp/file1.txt

# find file with size 5-10 bytes
$ find temp/ -type f -size +5c -size -10c
temp/file1.txt

# find and execute command
# '{}' gets substitute with find result
# starts with -exec, end with \;
$ find temp/ -type f -name "file*" -exec ls -l '{}' \;
-rwxr-xr-x 1 root root 7 Aug 10 23:10 temp/file1.txt
-rw-r--rw- 1 root root 0 Aug 10 22:55 temp/file2.txt
```

