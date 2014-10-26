deer
====

DESCRIPTION
-----------

`deer` is a file navigator for [zsh](http://zsh.sourceforge.net/)
heavily inspired by [ranger](http://ranger.nongnu.org/).

**WHY**

I've created `deer` because I really like to use `ranger` as an
extension of my shell to navigate the directories but its startup time
(even though still short) is sometimes too cumbersome. `deer`
implements the very basic ranger-like file navigation + some basic
operations on the commandline, like inserting the selected path (in
various ways). Not using the whole terminal can be viewed as an
another feature.

Pros:  
* launches much faster  
* better shell integration  
* retains the terminal contents and only uses a small part of the terminal  

Cons:  
* offers only a small subset of `ranger's` features  
* needs `zsh`  

USAGE
-----

To launch `deer`, press `alt+k`.

You can supply the numeric argument (`alt-number`) to go up the
appropriate number of directory levels on start.

If you activate `deer` with the cursor on a path, it will start in there.

**KEYS AND FUNCTIONS**

These functions can be bound to some custom key (the default is in the
parentheses):

* `down` (j) -- one item down
* `page_down` (J) -- five items down
* `up` (k) -- one item up
* `page_up` (K) -- five items up
* `enter` (l) -- enter into the selected directory
* `leave` (h) -- leave the current directory (one directory up)
* `search` (/) -- select the first file matching the given pattern
* `filter` (f) -- shows only files matching the given pattern
* `toggle_hidden` (H) -- show/hide the hidden files
* `quit` (q) -- exit `deer`
* `append_path` (a) -- insert the current path and leave the cursor on
  its right
* `append_abs_path` (A) -- absolute path version
* `insert_path` (i) -- insert the current path and leave the cursor on
  its left
* `insert_abs_path` (i) -- absolute path version
* `multi_insert` (s) -- insert the current path, add a smart separator
  (using the last character before the cursor, unless it's an opening
  brace) and don't quit yet
* `multi_insert_basename` (S) -- as above but insert only the basename
* `chdir` (c) -- cd into the current directory and quit
* `chdir_selected` (C) -- cd into the selected directory and quit
* `rifle` (r) -- run `rifle(1)` on the selected file

To bind the function to a different key, add something like this to
your `zshrc`:

    typeset -Ag DEER_KEYS
    DEER_KEYS[function]=key

The `DEER_KEYS` variable is an associative array holding the keys
associated with functions. One function may only be bound to one key
(the previous one is overwritten).

INSTALLATION
------------

Drop the `deer` main file in a directory from `$fpath[@]`, and make sure it gets
autoloaded in your `zshrc`:

    autoload -U deer

Adding these lines will make the script available to the line editor, and bind
it to a `ALT+k` respectively:

    zle -N deer
    bindkey '\ek' deer

Alternatively, you can source the file `deer`, and bind the initialization
function to a `ALT+k` as follows:

    source /path/to/deer
    zle -N deer-launch
    bindkey '\ek' deer-launch

By default, `deer` will use 22 lines of your terminal. This is configurable
with the `zstyle` mechanism. Drop a line like this in `zshrc` to adjust this
setting:

    zstyle ':deer:' height 35


KNOWN ISSUES
------------

**Slashes are replaced with backslashes in file previews**

This is an ugly workaround the issues with the way the output is
formatted (slash is used as a separator for `paste(1)` and
`column(1)` as it cannot appear in the filename).

**The ../ directory is not correctly shown when completing the
  previously typed path**

The shown path is created by deleting the preexisting prefix from the
absolute path. It greatly simplifies the code and makes it easier to
maintain (it's already quite messy in some places).

FAQ
---

**Can you add colors?**

Unfortunately, no. It's a limitation of the underlying `zle` (zsh
line editor) and I cannot do much about it.

**Why are there so many strange features and their variations?**

At first I add the new features taylored for myself. Later I plan to
review these features and clean them up. If you need something else,
please leave a feature request or add it yourself if you know how.

**The key binding system is ugly**

Yes. It is. I plan to replace it with a proper keymap but for now it
should suffice.

RELATED PROJECTS
----------------

[lscd](https://github.com/hut/lscd) from the author of the original
ranger is a minimal file browser written in a POSIX shell (with only a
few necessary bashisms).

[blscd](https://github.com/D630/blscd) is a Bash fork of `lscd` which
is very similar to ranger.

SEE ALSO
--------

ranger(1), zsh(1)

AUTHOR
------

Wojciech 'vifon' Siewierski < wojciech dot siewierski at gmail dot com >

COPYRIGHT
---------

Copyright (C) 2014  Wojciech Siewierski

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.
