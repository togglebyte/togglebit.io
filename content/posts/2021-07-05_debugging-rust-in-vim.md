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
