---
layout: post
title: "#1: Prefer ack or ag to grep"
date: 2015-08-20T00:30:00-04:00
---

[grep](http://linux.die.net/man/1/grep) is one of the most well known unix utilties.  If you've had any exposure to the command line at all, you've probably searched for something using grep.  grep's standalone use is searching for content inside of files, but with Unix's piping philosophy it is useful as a general search and filtering tool.  Originally created for Version 4 Unix 42 years ago, it has been a staple of *Nix operating systems ever since.   If you've been using it for even half that time, I'm sure a blog post isn't enough to convince you to stop.  But for the rest of us, there's an opportunity to do better.

[ack](http://beyondgrep.com/) and [ag](https://github.com/ggreer/the_silver_searcher) [^1] are similar projects with the same goal: providing a better experience than grep when searching directories of files, particularly for programmers.  The key selling points:

1. Improved speed when searching through directories
2. Ignores version control and binary files by default when searching
3. Easy filtering by file-type
4. A cleaner API for searching directories, without adding complexity to the single file case

This is easiest to see by comparing commands.

```bash grep command for searching for 'javascript' in a file
grep javascript foo.txt
```

```bash ack command for searching for 'javascript' in a file
ack javascript foo.txt
```

```bash grep command for searching for 'javascript' in any file in the current directory
grep -r javascript .
```

```bash ack command for searching for 'javascript' in any file in the current directory
ack javascript
```

```bash grep command for searching for 'javascript' in any file in the current directory, ignoring anything in ./.git
grep -r javascript . $(find . -type f | grep -v '\.git')
```

```bash ack command for searching for 'javascript' in any file in the current directory, ignoring anything in ./.git
ack javascript
```

```bash grep command for searching for 'javascript' in any html file in the current directory, ignoring anything in ./.git
grep -r javascript . $(find . -type f | grep -v '\.git')
```

```bash ack command for searching for 'javascript' in any html file in the current directory, ignoring anything in ./.git
ack --html javascript
```

Basically, as the complexity of what you're trying to do increases in terms of multiple files, the overhead of using grep increases proportionately.  Because ack and ag are optimized for common use cases, they're able to scale much better.

### Installation Instructions

- [ack](http://beyondgrep.com/install/)
- [ag](https://github.com/ggreer/the_silver_searcher#installing)


[^1]: ack came first; ag was created as a faster version of ack.  ack is written in perl, making it more portable, and has a few more configuration options.  ag is written in C and is generally faster.  Your choice may vary depending on your use case.
