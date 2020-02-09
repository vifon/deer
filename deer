# -*- mode: shell-script -*-
# vim: set ft=zsh :
#########################################################################
# Copyright (C) 2014-2015  Wojciech Siewierski                          #
#                                                                       #
# This program is free software: you can redistribute it and/or modify  #
# it under the terms of the GNU General Public License as published by  #
# the Free Software Foundation, either version 3 of the License, or     #
# (at your option) any later version.                                   #
#                                                                       #
# This program is distributed in the hope that it will be useful,       #
# but WITHOUT ANY WARRANTY; without even the implied warranty of        #
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the         #
# GNU General Public License for more details.                          #
#                                                                       #
# You should have received a copy of the GNU General Public License     #
# along with this program.  If not, see <http://www.gnu.org/licenses/>. #
#########################################################################

zstyle -s ":deer:" height DEER_HEIGHT || DEER_HEIGHT=22


typeset -Ag DEER_KEYS
function ()
{
    while [ -n "$2" ]; do
        DEER_KEYS[$1]=${DEER_KEYS[$1]:-$2}
        shift 2
    done
} down                j    \
  page_down           J    \
  up                  k    \
  page_up             K    \
  enter               l    \
  leave               h    \
  next_parent         ']'  \
  prev_parent         '['  \
  search              /    \
  filter              f    \
  toggle_hidden       H    \
  quit                q    \
  append_path         a    \
  append_abs_path     A    \
  insert_path         i    \
  insert_abs_path     I    \
  multi_insert_dwim   s    \
  multi_insert_abs    S    \
  chdir               c    \
  chdir_selected      C    \
  rifle               r    \
  edit                e    \


# Select the Nth next file. Pass a negative argument for the previous file.
deer-move()
{
    local FILES MOVEMENT INDEX
    MOVEMENT=$1

    FILES=($DEER_DIRNAME/${~DEER_FILTER[$DEER_DIRNAME]:-'*'}(N$DEER_GLOBFLAGS-/:t)
           $DEER_DIRNAME/${~DEER_FILTER[$DEER_DIRNAME]:-'*'}(N$DEER_GLOBFLAGS-^/:t))

    INDEX=${(k)FILES[(re)$DEER_BASENAME[$DEER_DIRNAME]]}

    if (( INDEX+MOVEMENT <= 0 )); then
        DEER_BASENAME[$DEER_DIRNAME]=$FILES[1]
    elif (( INDEX+MOVEMENT > $#FILES )); then
        DEER_BASENAME[$DEER_DIRNAME]=$FILES[$#FILES]
    else
        DEER_BASENAME[$DEER_DIRNAME]=$FILES[$INDEX+$MOVEMENT]
    fi
}

# Select the first visible directory (or file if there are no
# directories) in the current directory. Useful when changing the file
# filter.
deer-refocus()
{
    local TMP
    TMP=($DEER_DIRNAME/${~DEER_FILTER[$DEER_DIRNAME]:-'*'}(N$DEER_GLOBFLAGS-/:t)
         $DEER_DIRNAME/${~DEER_FILTER[$DEER_DIRNAME]:-'*'}(N$DEER_GLOBFLAGS-^/:t))
    DEER_BASENAME[$DEER_DIRNAME]=$TMP[1]

    [ -n "$DEER_BASENAME[$DEER_DIRNAME]" ] # Return if there were any files at all.
}

# Enter the selected directory
deer-enter()
{
    # Abort if there is no file focused at all or if it is not a
    # directory.
    [ -n "$DEER_BASENAME[$DEER_DIRNAME]" -a \
      -d "$DEER_DIRNAME/$DEER_BASENAME[$DEER_DIRNAME]" ] || return

    DEER_DIRNAME=${DEER_DIRNAME%/}/$DEER_BASENAME[$DEER_DIRNAME]

    if [ -z $DEER_BASENAME[$DEER_DIRNAME] ]; then
        deer-refocus
    fi
}

# Move to the parent directory
deer-leave()
{
    [ $DEER_DIRNAME = / ] && return
    DEER_BASENAME[$DEER_DIRNAME:h]=$DEER_DIRNAME:t
    DEER_DIRNAME=$DEER_DIRNAME:h
}

# Display a given prompt, read a string and save it into $BUFFER.
deer-prompt()
{
    BUFFER=""
    PREDISPLAY="$1/ "
    POSTDISPLAY=""

    local region_highlight
    region_highlight=("P0 $#1 fg=green")
    zle recursive-edit
}

# Read a pattern and select the first matching file.
deer-search()
{
    deer-prompt "search"

    local TMP
    TMP=($DEER_DIRNAME/${~BUFFER}${~DEER_FILTER[$DEER_DIRNAME]:-'*'}(N$DEER_GLOBFLAGS-:t))
    [ -n "$TMP[1]" ] && DEER_BASENAME[$DEER_DIRNAME]=$TMP[1]
}

# Read a pattern and use it as a new filter.
deer-filter()
{
    deer-prompt "filter"

    if [ -n "$BUFFER" ] && [[ ! $BUFFER == *\** ]]; then
        BUFFER=*$BUFFER*
    fi

    deer-apply-filter $BUFFER || deer-apply-filter
}

deer-apply-filter()
{
    DEER_FILTER[$DEER_DIRNAME]=$1
    deer-refocus
}

# Draw an arrow pointing to the selected file.
deer-mark-file-list()
{
    local MARKED=$1
    shift

    print -l -- "$@" \
        | grep -Fx -B5 -A$DEER_HEIGHT -- "$MARKED" \
        | perl -pe 'BEGIN{$name = shift}
                    if ($name."\n" eq $_) {
                        $_="-> $_"
                    } else {
                        $_="   $_"
                    }' -- "$MARKED"
}

# Draw the file lists in the form of Miller columns.
deer-refresh()
{
    local FILES PREVIEW PARENTFILES OUTPUT REL_DIRNAME
    local SEPARATOR="------"

    PREDISPLAY=$OLD_LBUFFER
    REL_DIRNAME=${${DEER_DIRNAME%/}#$DEER_STARTDIR}/
    [ -n "$DEER_STARTDIR" ] && REL_DIRNAME=${REL_DIRNAME#/}
    LBUFFER=$REL_DIRNAME$DEER_BASENAME[$DEER_DIRNAME]
    RBUFFER=""
    local TMP_FILTER
    TMP_FILTER=${DEER_FILTER[$DEER_DIRNAME]}
    POSTDISPLAY=${TMP_FILTER:+  filt:$TMP_FILTER}
    region_highlight=("P0       $#PREDISPLAY              fg=black,bold"
                      "0        $#REL_DIRNAME             fg=blue,bold"
                      "$#BUFFER $[$#BUFFER+$#POSTDISPLAY] fg=yellow,bold")


    FILES=($DEER_DIRNAME/${~DEER_FILTER[$DEER_DIRNAME]:-'*'}(N$DEER_GLOBFLAGS-/:t)
           $SEPARATOR
           $DEER_DIRNAME/${~DEER_FILTER[$DEER_DIRNAME]:-'*'}(N$DEER_GLOBFLAGS-^/:t))
    PARENTFILES=($DEER_DIRNAME:h/${~DEER_FILTER[$DEER_DIRNAME:h]:-'*'}(N$DEER_GLOBFLAGS-/:t))

    local IFS=$'\n'
    FILES=($(deer-mark-file-list "$DEER_BASENAME[$DEER_DIRNAME]" $FILES))
    PARENTFILES=($(deer-mark-file-list "$DEER_DIRNAME:t" $PARENTFILES))
    unset IFS

    FILES=(${(F)FILES[1,$DEER_HEIGHT]})
    PARENTFILES=(${(F)PARENTFILES[1,$DEER_HEIGHT]})


    if [ -f $DEER_DIRNAME/$DEER_BASENAME[$DEER_DIRNAME] ]; then
        if file $DEER_DIRNAME/$DEER_BASENAME[$DEER_DIRNAME] | grep -Fq text; then
            PREVIEW="--- Preview: ---"$'\n'$(head -n$DEER_HEIGHT $DEER_DIRNAME/$DEER_BASENAME[$DEER_DIRNAME])

            # Replace '/' with '∕' (division slash, U+2215) to allow using it as a
            # paste(1)/column(1) separator.
            PREVIEW=${PREVIEW//\//∕}
        else
            PREVIEW="--- Binary file, preview unavailable ---"
        fi
    else
        # I'm really sorry about what you see below.
        # It basically means: PREVIEW=(directories separator files)
        PREVIEW=($DEER_DIRNAME/$DEER_BASENAME[$DEER_DIRNAME]/${~DEER_FILTER[$DEER_DIRNAME/$DEER_BASENAME[$DEER_DIRNAME]]:-'*'}(N$DEER_GLOBFLAGS-/:t)
                 $SEPARATOR
                 $DEER_DIRNAME/$DEER_BASENAME[$DEER_DIRNAME]/${~DEER_FILTER[$DEER_DIRNAME/$DEER_BASENAME[$DEER_DIRNAME]]:-'*'}(N$DEER_GLOBFLAGS-^/:t))
        PREVIEW=${(F)PREVIEW[1,$DEER_HEIGHT]}
    fi

    OUTPUT="$(paste -d/ <(<<< $PARENTFILES                   \
                            | awk '{print substr($0,1,16)}') \
                        <(<<< $FILES)                        \
                        <(<<< $PREVIEW)                      \
                | sed 's,/, / ,g'                            \
                | column -t -s/ 2> /dev/null                 \
                | awk -v width=$COLUMNS '{print substr($0,1,width-1)}')"
    zle -M -- $OUTPUT
    zle -R
}

# Run `deer-add' with the same arguments, restore the shell state and
# then exit.
deer-restore()
{
    deer-add "$@"
    PREDISPLAY=""
    POSTDISPLAY=""
    region_highlight=()
    LBUFFER=$OLD_LBUFFER
    RBUFFER=$OLD_RBUFFER
    zle reset-prompt
    zle -M ""
}

# Add the given string before or after the cursor.
deer-add()
{
    case $1 in
        --append)
            OLD_LBUFFER+=$2
            shift 2
            ;;
        --insert)
            OLD_RBUFFER=$2$OLD_RBUFFER
            shift 2
            ;;
    esac
}

# Get the quoted relative path from the absolute unquoted path.
deer-get-relative()
{
    local TMP
    TMP=${1:-${DEER_DIRNAME%/}/$DEER_BASENAME[$DEER_DIRNAME]}
    TMP="`python -c '
import sys, os
print(os.path.relpath(sys.argv[1], sys.argv[2]))
' $TMP ${DEER_STARTDIR:-$PWD}`"
    print -R $TMP:q
}

# Tries to guess a directory to start in from the current argument.
deer-set-initial-directory()
{
    autoload -U split-shell-arguments modify-current-argument
    local REPLY REPLY2 reply
    local DIRECTORY

    ((--CURSOR))
    split-shell-arguments
    ((++CURSOR))

    # Find the longest existing directory path in the current argument.
    DEER_STARTDIR=${(Q)${${reply[$REPLY]%%[[:space:]]#}:a}%/}
    while [ -n "$DEER_STARTDIR" -a \
            ! -d "$DEER_STARTDIR" ]; do
        DEER_STARTDIR=${DEER_STARTDIR%/*}
    done

    DEER_DIRNAME=${DEER_STARTDIR:-$PWD}
}

# The main entry function.
deer-launch()
{
    emulate -L zsh
    setopt extended_glob
    local DEER_DIRNAME DEER_STARTDIR DEER_GLOBFLAGS
    local -A DEER_FILTER DEER_BASENAME
    local REPLY OLD_LBUFFER OLD_RBUFFER

    local GREP_OPTIONS
    GREP_OPTIONS=""

    OLD_LBUFFER=$LBUFFER
    OLD_RBUFFER=$RBUFFER

    deer-set-initial-directory

    DEER_GLOBFLAGS=""

    if [ -n "$NUMERIC" ]; then
        for i in {1..$NUMERIC}; do
            deer-leave
        done
    else
        # Don't change cwd but initialize the variables.
        deer-leave
        deer-enter
    fi

    deer-refresh
    while read -k; do
        case $REPLY in
            # Movement
            $DEER_KEYS[up])
                deer-move -1
                deer-refresh
                ;;
            $DEER_KEYS[page_up])
                deer-move -5
                deer-refresh
                ;;
            $DEER_KEYS[down])
                deer-move 1
                deer-refresh
                ;;
            $DEER_KEYS[page_down])
                deer-move 5
                deer-refresh
                ;;
            $DEER_KEYS[enter])
                deer-enter
                deer-refresh
                ;;
            $DEER_KEYS[leave])
                deer-leave
                deer-refresh
                ;;
            $DEER_KEYS[next_parent])
                deer-leave
                deer-move 1
                deer-enter
                deer-refresh
                ;;
            $DEER_KEYS[prev_parent])
                deer-leave
                deer-move -1
                deer-enter
                deer-refresh
                ;;
            # Search
            $DEER_KEYS[search])
                deer-search
                deer-refresh
                ;;
            # Filter
            $DEER_KEYS[filter])
                deer-filter
                deer-refresh
                ;;
            $DEER_KEYS[toggle_hidden])
                if [ -z $DEER_GLOBFLAGS ]; then
                    DEER_GLOBFLAGS="D" # show hidden files
                else
                    DEER_GLOBFLAGS=""
                fi
                # make sure the focus is on a visible file
                DEER_BASENAME[$DEER_DIRNAME]=
                deer-leave
                deer-enter
                deer-refresh
                ;;
            # Quit
            $DEER_KEYS[quit])
                deer-restore
                break
                ;;
            # Insert the path and quit.
            $DEER_KEYS[append_path])
                deer-restore --append "`deer-get-relative` "
                break
                ;;
            $DEER_KEYS[append_abs_path])
                deer-restore --append "${${DEER_DIRNAME%/}:q}/${DEER_BASENAME[$DEER_DIRNAME]:q} "
                break
                ;;
            $DEER_KEYS[insert_path])
                deer-restore --insert " `deer-get-relative`"
                break
                ;;
            $DEER_KEYS[insert_abs_path])
                deer-restore --insert " ${${DEER_DIRNAME%/}:q}/${DEER_BASENAME[$DEER_DIRNAME]:q}"
                break
                ;;
            # Insert the path and don't quit yet.
            $DEER_KEYS[multi_insert_dwim])
                if [ "$OLD_LBUFFER[-1]" = "/" ]; then
                    OLD_LBUFFER+="{"
                fi
                # replacement used to insert ',' instead of '{' as a separator in {foo,bar,...} lists
                deer-add --append "`deer-get-relative`"${${OLD_LBUFFER[-1]/\{/,}:- }
                deer-move 1
                deer-refresh
                ;;
            # Insert the absolute path and don't quit yet.
            $DEER_KEYS[multi_insert_abs])
                deer-add --append " ${${DEER_DIRNAME%/}:q}/${DEER_BASENAME[$DEER_DIRNAME]:q}"
                deer-move 1
                deer-refresh
                ;;
            # Quit and change the shell's current directory to the selected one.
            $DEER_KEYS[chdir])
                deer-leave
                ;&
            $DEER_KEYS[chdir_selected])
                if [[ -d $DEER_DIRNAME/$DEER_BASENAME[$DEER_DIRNAME] && \
                      -x $DEER_DIRNAME/$DEER_BASENAME[$DEER_DIRNAME] ]]; then
                    cd -- $DEER_DIRNAME/$DEER_BASENAME[$DEER_DIRNAME]
                    deer-restore
                    break
                fi
                ;;
            $DEER_KEYS[edit])
                if [[ -f $DEER_DIRNAME/$DEER_BASENAME[$DEER_DIRNAME] ]]; then
                    "${EDITOR:-vim}" $DEER_DIRNAME/$DEER_BASENAME[$DEER_DIRNAME]
                fi
                ;;
            # See rifle(1) manpage (included with ranger(1)).
            $DEER_KEYS[rifle])
                if [[ -f $DEER_DIRNAME/$DEER_BASENAME[$DEER_DIRNAME] ]]; then
                    rifle $DEER_DIRNAME/$DEER_BASENAME[$DEER_DIRNAME]
                fi
                ;;
            # Arrow keys
            $'\e')
                read -k
                case $REPLY in
                    '[')
                        read -k
                        case $REPLY in
                            'A')
                                deer-move -1
                                deer-refresh
                                ;;
                            'B')
                                deer-move 1
                                deer-refresh
                                ;;
                            'C')
                                deer-enter
                                deer-refresh
                                ;;
                            'D')
                                deer-leave
                                deer-refresh
                                ;;
                        esac
                        ;;
                esac
                ;;
        esac
    done
}

if zle; then
    deer-launch
else
    deer()
    {
        deer-launch "$@"
    }
fi
