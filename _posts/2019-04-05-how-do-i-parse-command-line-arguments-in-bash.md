---
layout: post
title: "How do I parse command line arguments in Bash?"
date: 2019-04-05 15:29:06 +0700
tags: [bash, bash script, command line]
---

### Method #1: Using bash without getopt[s]

Two common ways to pass key-value-pair arguments are:

### Bash Space-Separated (e.g., --option argument) (without getopt[s])

Usage `./myscript.sh -e conf -s /etc -l /usr/lib /etc/hosts`

```
#!/bin/bash

POSITIONAL=()
while [[ $# -gt 0 ]]
do
key="$1"

case $key in
    -e|--extension)
    EXTENSION="$2"
    shift # past argument
    shift # past value
    ;;
    -s|--searchpath)
    SEARCHPATH="$2"
    shift # past argument
    shift # past value
    ;;
    -l|--lib)
    LIBPATH="$2"
    shift # past argument
    shift # past value
    ;;
    --default)
    DEFAULT=YES
    shift # past argument
    ;;
    *)    # unknown option
    POSITIONAL+=("$1") # save it in an array for later
    shift # past argument
    ;;
esac
done
set -- "${POSITIONAL[@]}" # restore positional parameters

echo FILE EXTENSION  = "${EXTENSION}"
echo SEARCH PATH     = "${SEARCHPATH}"
echo LIBRARY PATH    = "${LIBPATH}"
echo DEFAULT         = "${DEFAULT}"
echo "Number files in SEARCH PATH with EXTENSION:" $(ls -1 "${SEARCHPATH}"/*."${EXTENSION}" | wc -l)
if [[ -n $1 ]]; then
    echo "Last line of file specified as non-opt/last argument:"
    tail -1 "$1"
fi
```

### Bash Equals-Separated (e.g., --option=argument) (without getopt[s])

Usage `./myscript.sh -e=conf -s=/etc -l=/usr/lib /etc/hosts`

```
#!/bin/bash

for i in "$@"
do
case $i in
    -e=*|--extension=*)
    EXTENSION="${i#*=}"
    shift # past argument=value
    ;;
    -s=*|--searchpath=*)
    SEARCHPATH="${i#*=}"
    shift # past argument=value
    ;;
    -l=*|--lib=*)
    LIBPATH="${i#*=}"
    shift # past argument=value
    ;;
    --default)
    DEFAULT=YES
    shift # past argument with no value
    ;;
    *)
          # unknown option
    ;;
esac
done
echo "FILE EXTENSION  = ${EXTENSION}"
echo "SEARCH PATH     = ${SEARCHPATH}"
echo "LIBRARY PATH    = ${LIBPATH}"
echo "Number files in SEARCH PATH with EXTENSION:" $(ls -1 "${SEARCHPATH}"/*."${EXTENSION}" | wc -l)
if [[ -n $1 ]]; then
    echo "Last line of file specified as non-opt/last argument:"
    tail -1 $1
fi
```
To better understand `${i#*=}` search for "Substring Removal" in [this guide](http://tldp.org/LDP/abs/html/string-manipulation.html). It is functionally equivalent to `sed 's/[^=]*=//' <<< "$i"` which calls a needless subprocess or `echo "$i" | sed 's/[^=]*=//'` which calls two needless subprocesses.

### Method #2: Using bash with getopt[s]

from: [http://mywiki.wooledge.org/BashFAQ/035#getopts](http://mywiki.wooledge.org/BashFAQ/035#getopts)

**getopt(1) limitations** (older, relatively-recent `getopt` versions):

+ can't handle arguments that are empty strings
+ can't handle arguments with embedded whitespace

More recent `getopt` versions don't have these limitations.

Additionally, the POSIX shell (and others) offer `getopts` which doesn't have these limitations. Here is a simplistic `getopts` example:

```
#!/bin/sh

# A POSIX variable
OPTIND=1         # Reset in case getopts has been used previously in the shell.

# Initialize our own variables:
output_file=""
verbose=0

while getopts "h?vf:" opt; do
    case "$opt" in
    h|\?)
        show_help
        exit 0
        ;;
    v)  verbose=1
        ;;
    f)  output_file=$OPTARG
        ;;
    esac
done

shift $((OPTIND-1))

[ "${1:-}" = "--" ] && shift

echo "verbose=$verbose, output_file='$output_file', Leftovers: $@"

# End of file
```
The advantages of `getopts` are:

1. It's more portable, and will work in other shells like `dash`.
2. It can handle multiple single options like `-vf filename` in the typical Unix way, automatically.

The disadvantage of `getopts` is that it can only handle short options (`-h`, not `--help`) without additional code.

There is a [getopts tutorial](http://wiki.bash-hackers.org/howto/getopts_tutorial) which explains what all of the syntax and variables mean. In bash, there is also `help getopts`, which might be informative.

Link follow: [Stackoverflow.com](https://stackoverflow.com/a/14203146)
