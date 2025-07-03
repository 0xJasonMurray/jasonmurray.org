---
title: "Using Hugo as a daily life log"
date: 2020-07-06T12:03:42-05:00
toc: false
images:
categories:
  - tech
tags: 
  - hugo
  - journal
  - log
  - bash
  - script
---

I have been working on automating my daily journal with Hugo, `vi`, and a few `bash` scripts.  The primary goal is to create a friction free system to update a journal entry every time I context switch between tasks or need to log something interesting.

The workflow starts by creating a new journal file every morning with the following `bash` script.   If the journal file does not already exist, a new file is created using the current date as the name, `vi` is started for editing, and a live web server is loaded to view changes.   When `vi` is closed, the file saves and the web server shuts down.   I put this script in the search $PATH:
```
#!/bin/bash

cdate=`date +%Y%m%d`
hugobase="/Users/jemurray/Documents/www-personal/current/journal.jasonmurray.org"
journalfile="$hugobase/content/posts/$cdate.md"


cd $hugobase

if [ ! -f "$journalfile" ]; then
    hugo new posts/$cdate.md
fi

hugo -D server -p 8000 --quiet > /dev/null 2>&1 &

vi $journalfile

kill `ps -ef | grep "[h]ugo -D server -p 8000" | awk '{print $2}'`
```

Create a Hugo template ($HUGOROOT/archetypes/posts.md):
```
---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
toc: false
images:
tags:
  - journal
---

# Daily log:

# Decisions:

# Changes:

# Notes:
```

By using an insert key-remap, typing `idt` anywhere within `vi` will insert a date and time stamp.  Add this to the `~/.vimrc` file:

```
inoremap idt <C-R>=strftime("%c - ")<CR>
```

Start the system:

```
jemurray@mbp-2019:~ $ dailyjournal.sh
---
title: "20200706"
date: 2020-07-06T08:30:57-05:00
toc: false
images:
tags:
  - journal
---

# Daily log:

Mon Jul  6 08:31:04 2020 - review security vulnerability on XXXX and fix put in place to stop it.

Mon Jul  6 08:40:45 2020 - determine XXXXXX charges his time against

Mon Jul  6 08:52:44 2020 - review EnterpriseApplications Confluence pages

Mon Jul  6 08:56:12 2020 - Network Engineering staff meeting

Mon Jul  6 09:11:15 2020 - Zeek tap design

Mon Jul  6 09:28:40 2020 - NE manager meeting

Mon Jul  6 09:58:30 2020 - find IP ranges for med VPN for james 

Mon Jul  6 10:34:04 2020 - snack break

Mon Jul  6 10:44:49 2020 - continue work on zeek taps

Mon Jul  6 11:24:06 2020 - missed monitoring project discussion meeting, was wrapped up in zeek tap

Mon Jul  6 11:24:26 2020 - lunch.

Mon Jul  6 12:01:35 2020 - created blog post about this journal system

# Decisions:
~
~
"~/Documents/www-personal/current/journal.jasonmurray.org/content/posts/20200706.md" 49L, 1132C
```