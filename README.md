deer
====

DESCRIPTION
-----------

`deer` is a file navigator for [zsh](http://zsh.sourceforge.net/)
heavily inspired by [ranger](http://ranger.nongnu.org/).

**WHY**

`deer` isn't in any way better than `ranger`. If you seek features,
you're better off using the original.

I've created `deer` because I really like to use `ranger` as an
extension of my shell to navigate the directories but its startup time
(even though still short) is sometimes too cumbersome. `deer`
implements the very basic ranger-like file navigation + some basic
operations on the commandline, like inserting the selected path (in
various ways). Not using the whole terminal can be viewed as an
another feature.

Pros:  
* launches much faster  
* retains the terminal contents and only uses a small part of the terminal  

Cons:  
* offers only a small subset of `ranger's` features  
* needs `zsh`  

USAGE
-----

To launch `deer`, press `alt+k`. Now we have the following keys available:

* `hjkl` --  vi-like movement
* `J`,`K` --  move by five files
* `f`,`/` -- search
* `q` -- quit and do nothing
* `a` (`A`) -- quit and paste before the cursor the selected file's absolute (or relative) path
* `i` (`I`) -- quit and paste after the cursor the selected file's absolute (or relative) path
* `c` -- quit and cd into the current directory
* `C` -- quit and cd into the selected directory
* `r` -- open the file with `ranger's` `rifle`

You can supply the numeric argument (`alt-number`) to go up the
appropriate number of directory levels on start.

INSTALLATION
------------

Drop the `deer` main file in a directory from `$fpath[@]`, and make sure it gets
autoloaded in your `zshrc`:

    autoload -U deer

Adding these lines will make the script available to the line editor, and bind
it to a `ALT+k` respectively:

    zle -N deer
    bindkey '\ek' deer

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

FAQ
---

**Can you add colors?**

Unfortunately, no. It's a limitation of the underlying `zle` (zsh
line editor) and I cannot do much about it.

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
