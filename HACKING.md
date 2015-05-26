# WARNING: MAY BE OUTDATED, SORRY #

In this file I'll try to explain some design decisions and how `deer` works.

VARIABLES
=========

PATHS
-----

**DEER_DIRNAME**

The full path to the current directory, without the trailing slash.

**DEER_BASENAME**

An associative array mapping the dirnames to the files or directories
focuesed there.

**DEER_BASENAME[$DEER_DIRNAME]**

The name of the currently selected file or directory.

Due to the special case of the root directory (`/`), simply using
`$DEER_DIRNAME/$DEER_BASENAME[$DEER_DIRNAME]` to get the full path to the selected
directory is not sufficient as it would result in a double slash.
Currently I use `${DEER_DIRNAME%/}/$DEER_BASENAME[$DEER_DIRNAME]` to eliminate one
slash in that corner case. It complicates the code in a few places but
it's more maintainable than keeping the slash in `DEER_BASENAME` and
concatenating dirname with basename without the additional slash in
between (it was the case up until recently).

OTHERS
------

**DEER_FILTER**

An associative array mapping the dirnames to the filter to be applied
there. It's modified in the function `deer-enter`. If the directory
does not exist in that map, it's value should be assumed to be `*`.
The prefered notation is quite ugly but it works:
`${DEER_FILTER[$DEER_DIRNAME]:-'*'}`

**OLD_LBUFFER and OLD_RBUFFER**

The line buffer stored to restore the `LBUFFER` and `RBUFFER` contents
later (in the `deer-restore` function) after adding the selected files
to them.

**PREDISPLAY**

The built-in variable used to display the preview of the left side of
the buffer (`OLD_LBUFFER`).

**POSTDISPLAY**

Used to display the status information. Currently only displays the
applied filter if any.
