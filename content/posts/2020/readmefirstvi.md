---
title: "vi commands every engineer should know"
date: 2020-05-20T11:53:15-05:00
toc: false
images:
categories:
  - tech
tags: 
  - vi
---

# Summary

- The basics:
  - Start: `vi`
  - Insert: `i`
  - Exit insert mode (to use movement keys): `<esc>`
  - Left: `h`
  - Up: `j`
  - Down: `k`
  - Right: `l`
  - Save: `<esc>:wq!`


# Details

Many heated debates have been had over editors, vi vs emacs, graphical vs. console, etc.

Any network or unix engineer worth their weight in salt should know `vi`.  Why you ask? Unix-based systems with a CLI will have `vi`. It is simple, lightweight, works over a terminal or console session, and does not require any special dependencies.

While `vi` may be intimidating at first, the basics are easy. For those people starting out in `vi` these few commands will accomplish 80% of your needs:

## Operating modes

The first thing to know about `vi` is there are 2 operating modes:

- **Command mode**: When `vi` starts, this is the default operating mode.  Everything except inserting text will be done here. Pressing `<esc>` at any time will put the editor into command mode.
- **Insert mode**: Start insert mode by pressing `i` - This is how to enter text. Note: while the arrow keys work in insert mode, get used to pressing `<esc>` and using the `h` (left), `j` (down), `k` (up), and `l` (right) keys for navigation, you will thank me later.


## Starting

To start `vi` run the command:

```
jemurray@mbp-2019:~/www $ vi content/posts/readmefirstvi.md
```

The screen will look like this:

```
---
title: "vi basics every engineer should know"
date: 2020-05-19T17:53:15-05:00
draft: true
toc: false
images:
tags: 
  - vi
---

# Summary


- The basics:
  - Start: `vi`
  - Insert: `i`
  - Exit insert mode (to use movement keys): `<esc>`
  - Left: `h`
  - Up: `j`
  - Down: `k`
  - Right: `l`
  - Save: `<esc>:wq!`


# Details
~
~
~
~
~
“content/posts/readmefirstvi.md” 26L, 842C
```

## Movement

Moving the cursor can be done in one of two ways:

- The arrow keys will move up, down, left, and right, don’t do it. Learn to exit insert mode and use the home row keys.
- The better way is, `h` (left), `j` (down), `k` (up), and `l` (right)


## Editing / Inserting

Press `i` to insert text.

Press `<esc>` when you are finished adding text. Be liberal with the `<esc>` key, it won’t hurt anything. 25 years of muscle memory has me pressing `<esc>` a lot, even when I am not editing text with `vi`.


## Quit and Save

Press: `<esc>:wq!`

- `<esc>` exit insert mode
- `:` enter command mode
- `w` write changes
- `q` quit
- `!` force


## Next...

Google is your friend, `vi` can do a lot. Start with these basics, then branch out. Becoming a master with `vi` will improve your text editing workflows by orders of magnitude. You will be glad you learned it. 25 years later, it is still my primary text editor.



