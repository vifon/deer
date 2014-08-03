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

**Garbled file previews**

The previews of files containing slashes are broken due to the way the
output is formatted into columns. I plan to fix this once everything
else will work as intended.

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
