deer
====

DESCRIPTION
-----------

`deer` is a file manager for [zsh](http://zsh.sourceforge.net/) heavily inspired
by [ranger](http://ranger.nongnu.org/).

USAGE
-----

To launch `deer`, press `alt+k`. Now we have the following keys available:

* `hjkl` --  vi-like movement
* `J`,`K` --  move by five files
* `/` -- search
* `q` -- quit and do nothing
* `i` -- quit and paste the selected file's path
* `c` -- quit and cd into the selected directory

INSTALLATION
------------

Just source the `deer` main file from your `zshrc`:

    source /path/to/deer

KNOWN ISSUES
------------

**Slashes are replaced with backslashes in file previews**

This is an ugly workaround the issues with the way the output is
formatted (slash is used as a separator for `paste(1)` and
`column(1)` as it cannot appear in the filename).

**Garbled subdirectory listing**

Before `util-linux 2.23` there was a bug in `column(1)` that is the
cause of it. I cannot do much. If I recall correctly, Ubuntu and
Debian might still be affected.

You can download from
[HERE](http://ftp.kernel.org/pub/linux/utils/util-linux/) and compile
the newer verion and take from it the working `column(1)`.

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
