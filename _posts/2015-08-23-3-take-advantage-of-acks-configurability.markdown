---
layout: post
title: "#3: Take Advantage of Acks Configurability"
date: 2015-08-23T22:01:41-04:00
categories: ack
---

*This is the 3rd post in a row on ack.  If you haven't yet, checkout tips [#1](http://benmccormick.github.io/cli_blog/ack/2015/08/20/1-prefer-ack-or-ag-to-grep.html) and [#2](http://benmccormick.github.io/cli_blog/ack/2015/08/20/2-use-ack-to-get-familiar-with-a-new-codebase.html) for more background on ack.*

One of the nice things about [ack](http://beyondgrep.com/) is the fact that it takes an `.ackrc` configuration file [^1]. These files can be scoped at a directory level, a user level or at a root level, and mirror the options available on the executable itself.  This allows you to extend and customize acks behavior in 3 primary ways.

#### 1. Add new filetypes, and bundle existing ones

One of ack's most useful features is the ability to add a `--<filetype>` flag to your searches to restrict your searches to a specific filetype.   ack comes with 75 builtin types including almost all common languages.  But those aren't always good enough.  To handle obscure languages, and not so obscure syntaxes like handlebars templates, ack lets you define new filetypes.  You're also able to modify existing filetypes to add additional file extensions to include under that filetype.  So for instance my ackrc includes these lines:

```
--type-add
hbs=hbs
--type-add
js=js,es6
```

The first 2 lines allow me to specifically search handlebars files, which are where all of the html content for my work projects is stored.  The next 2 lines include es6 files in my JavaScript searches, useful since I append JavaScript files that I intend to process with [Babel](https://babeljs.io/) with the `.es6` extenstion.  Customizing the js extension allows me to search all of my JavaScript together.  You can also define richer types in ackrc [based on file names using regular expressions or direct name matching](http://beyondgrep.com/documentation/ack-2.14-man.html#defining_your_own_types).

#### 2. Set default options

Any option that the ack executable takes can have a default set or changed in an ackrc file.  This is mainly useful for the display option.  By default ack groups matches from different files and gives the file information above each group of matches.  It also shows all output in color.  Any of those things can be disabled or changed in your ackrc file. 

#### 3. Exclude unwanted directories or files

Oftentimes there are a predictable group of files that you may want to ignore in searches.  Perhaps you have a build directory that mirrors your source code, or documentation files mingled with your source.  You can put `--ignore-directory` or `--ignore-file`.  Ignore directory takes a directory name, while ignore-file takes matches in the same format as the custom filetypes mentioned above.

If you're starting to use ack, don't miss the chance to make your searches even more efficient by removing any rough edges with an `.ackrc`.

[^1]: This is also one of the main traits that separates ack from [ag](https://github.com/ggreer/the_silver_searcher).
