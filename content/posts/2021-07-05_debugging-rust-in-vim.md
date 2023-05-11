+++
title = "Debugging Rust with vim and neovim (in Linux)"
date = 2021-07-05
+++

## Step 1

Run `:packadd termdebug` inside vim/nvim.
This is not some third party package manager, `packadd` is built into vim.

This has to be done for every instance, so if you want this to always be enabled
then add this to your vimrc.

Also to use `rust-gdb` rather than standard `gdb` set `let
g:termdebugger="rust-gdb"`.

## Step 2

Build your project (so you have something to debug in `./target/debug/<your
project>`)

## Step 3

From within vim/nvim run `:Termdebug target/debug/<your project>`.
This will start gdb.

Now you can set a breakpoint with `:Break` and finally run the code with `:Run`.

## Commands

* `:Break` sets a breakpoint where the cursor is
* `:Run` starts the debugger
* `:Clear` removes the breakpoint under the cursor
* `:Over` steps over the current line
* `:Step` steps into the current line
* `:Continue` to continue execution
* `:Evaluate` to execute selected code or code under cursor

For more information see `:help Termdebug` inside vim/nvim.

**Note** To see the value of the variable under the cursor: `K` (shift + k) by
default.

---------

## GDB dashboard

If you want to use gdb dashboard (this is useful as it lets you see the locals
all the time) you can get this from [https://github.com/cyrus-and/gdb-dashboard/](https://github.com/cyrus-and/gdb-dashboard/)
(check your package manager if you are on Linux as it might exist there as
well).


Create `$XDG_CONFIG/gdb-dashboard/init` and add the follow lines: 
 
```
dashboard -layout variables
dashboard variables -style compact False
```

{% note() %}
A hack: 
Somewhere around line 527 (at the time of writing) there is an if-statement that checks if you are
running inside `gdb` or printing to a file.

```python
if fs is not gdb or clear_screen:
    buf += Dashboard.clear_screen()
```

By removing the `if` and changing this to

```python
buf += Dashboard.clear_screen()
```
the locals will stay on top in the NeoVim terminal
(otherwise they will keep scrolling the window).

{% end %}

