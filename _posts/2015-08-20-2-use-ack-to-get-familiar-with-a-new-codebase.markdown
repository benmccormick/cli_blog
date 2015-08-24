---
layout: post
title: "#2: Use Ack to Get Familiar With a New Codebase"
date: 2015-08-20T18:09:22-04:00
categories: ack
---

[ack](http://beyondgrep.com/) is an incredibly useful tool when approaching a new codebase, especially if you're focused on a specific feature [^1]. It allows you to quickly get a view of the codebase, and then zero in on the functionality you're interested in.  To see how this works, let's use jQuery's [source repo](https://github.com/jquery/jquery) as an example, and see what happens if we want to get a look at how it implements ajax.

```bash get jQuery
❯ git clone git@github.com:jquery/jquery.git
❯ cd jquery
```

We can start by trying to get a general idea of the size of the codebase.  Running `ack -f` to show all files in the directory will immediately overrun our terminal, so we can get a file count using `wc` instead

```bash
❯ ack -f |wc -l
    249
```

Ok thats a good amount of files.  But how many of them are actually source files?  We can filter down to just the javascript files.

```bash
❯ ack -f --js |wc -l
    155
```

But thats probably still a bit high, since not all of the JavaScript files are source files.  A quick `ls` shows that we probably want to filter down to the src directory.  

```bash
❯ ack -f --js src |wc -l
    92
```

Ok so now we're down to 92 source files.  Still a bit much, but its probably worth printing them with `ack -f --js src` at this point to get an idea of the directory structure.  When we do that, we can see there's an ajax folder, which is probably what we're interested in.  To make sure we're not missing related files, we can continue on to searching for files with ajax in the file name directly.

```bash
❯ ack -g --js ajax src
    src/ajax/jsonp.js
    src/ajax/load.js
    src/ajax/parseJSON.js
    src/ajax/parseXML.js
    src/ajax/script.js
    src/ajax/var/location.js
    src/ajax/var/nonce.js
    src/ajax/var/rquery.js
    src/ajax/xhr.js
    src/ajax.js
    src/event/ajax.js
```

That gets us down to 11 files to look at.  But we might be getting a bit greedy.  Not every file related to ajax may have ajax in the file path.  We can use the `-l` option of ack to display the list of files that contain ack somewhere in their text.

```bash
❯ ack --js -l ajax src
    src/ajax/jsonp.js
    src/ajax/load.js
    src/ajax/script.js
    src/ajax/xhr.js
    src/ajax.js
    src/event/ajax.js
    src/jquery.js
    src/manipulation/_evalUrl.js
    src/serialize.js
```

Ok that adds 3 additional files with ajax references.  But its not clear that they're relevant to jQuery's ajax implementation, it may just be a file reference or comment.  Lets use ack to get a quick look at how ajax is referenced in those files.  This part gets a bit more complicated.  We can use ack's `-v` option to invert our matching and find non-matching inputs, then use unix piping to filter our previous command.

```bash
❯ ack --js -l ajax src |ack -v ajax
    src/jquery.js
    src/manipulation/_evalUrl.js
    src/serialize.js
```

Now we can use ack's `-x` argument to search the files piped in through standard input.

```bash
❯ ack --js -l ajax src |ack -v ajax|ack -x ajax

    src/jquery.js
    20:     "./ajax",
    21:     "./ajax/xhr",
    22:     "./ajax/script",
    23:     "./ajax/jsonp",
    24:     "./ajax/load",
    25:     "./event/ajax",

    src/manipulation/_evalUrl.js
    2:      "../ajax"
    6:      return jQuery.ajax({
    9:              // Make this explicit, since user can override this through ajaxSetup (#11264)

    src/serialize.js
    61:             traditional = jQuery.ajaxSettings && jQuery.ajaxSettings.traditional;
```

So we can see pretty quickly that these files are safe to ignore in terms of understanding how ajax works in jQuery.  They're just module loads, an actual call to the ajax method for other purposes, and settings serialization. It is useful to note that `src/ajax` is loaded into the main jQuery file first. Its probably worth looking at it as the starting point for our investigation. We're well on our way to diving into the ajax functionality, able to focus on 11 of the 249 files in the repo immediately, already knowing how those files are loaded into the main library.

This was just a simple example.  Whenever you're spelunking into new code territory, consider using ack to get your bearings.  Things go a lot faster when you know the lay of the land in advance.


[^1]: This post is written referencing ack, but all of the above equally applies to [ag](https://github.com/ggreer/the_silver_searcher)
