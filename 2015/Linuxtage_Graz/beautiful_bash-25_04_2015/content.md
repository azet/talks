# Introduction
## Caveat Emptor

I'm not endorsing Bash for large-scale projects, difficult or
performance critical tasks. If your project needs to talk to a database,
object store, interact with a filesystem or dynamically handle block
devices - you **SHOULD NOT** use Bash in the first place. You can. But
you'll regret it - I speak from years of experience doing completely
insane stuff in Bash for fun (certainly not for profit).

\vfill
Bash is useful for one thing and one thing only: as *glue*!

\vfill

..and it's the glue that holds Linux distributions, Embedded Appliances
and even Commercial networking gear together - so you better use the best glue on
the market, right?

## Do we really need another style guide?
* For starters: It's not only a style guide, but more on that later.
* A lot of the internet actually runs on poorly written Bash.
* Your company probably depends on a lot of Bash-glue.
* Everyone uses it on a daily basis to glue userland utilities together.
* Some scripts unintentionally look like they are submissions for an
  obfuscated code contest.
* There are some style guides (e.g. by Google) and tutorials - but
  nothing definitive.
* Most books on the subject are ancient and often reflect personal
  opinions of authors, outdated Bash versions and userland utilities and
  most haven't been updated in decades.
* I don't know a single good book on Bash. The best resource is still
  \url{http://wiki.bash-hackers.org}.

# Working towards a community style guide
## Working towards a community style guide
* I've started collecting style guides, tutorials, write-ups, tools and
  debugging projects during the last couple of years.    
  ..chose the best ideas and clearest styles and combined them into one
  big community driven effort.
* People started contributing.
* Nothing is written in stone. Come up with a better idea for a certain
  topic and I'll gladly accept it.
* I've also included a lot of mistakes people do or even rely on when
  writing their (often production) scripts.
* I've also collected a lot of tricks and shortcuts I've learned over
  the years specific to bash scripting and the Linux userland.

# Doing it wrong
## Bad Example
Here's a cool and bad example at the same time. `rpm2cpio` reimplemented
in bash.

* As Debian package: `Installed-Size: 1044`
* As Bash script: `4`

## Bad Example (cont.)
\includegraphics[height=200px]{rpm2cpio}

\tiny
\url{https://trac.macports.org/attachment/ticket/33444/rpm2cpio}

## Common bad style practices

* overusing `grep` for tasks that Bash can do by itself.
* using bourne-shell backticks instead of `$()` for subshell calls.    
  .. ever tried to nest backtick subshells? yea. you'll have to escape
  them. instead of e.g.:     
  `$(util1 $(util2 ${some_variable_as_argument}))`.
* manual argument parsing instead of using the `getopts` builtin.
* using `awk` for arithmetic operations bash can do very well.    
  .. same goes for `expr(1)`. please stop using it in bash scripts.    
  .. same goes for `bc(1)`. please stop using it in bash scripts.

## Common bad style practices (cont.)

* using the `echo` builtin where `printf` can (and probably should) be
  used.
* using `seq 1 15` for range expressions instead of `{1..15}`
* many `coreutils` you do not need \& you save on subshell calls.    
  .. a lot is set as a variable in your environment already    
  (protip: see what `env` gives you to work with in the first place)
* worst of all:  endless and unreadable pipe glue............

## Common bad style practices (cont.)
So what is more readable to you and probably the angry sysadmin that
might take over your codebase at some point in time?
\vspace{20px}


`ls ${long_list_of_parameters} | grep ${foo} | grep -v grep | pgrep | wc -l | sort | uniq`
\newline
\newline
or


```
ls ${long_list_of_parameters}   \
    | grep ${foo}               \
    | grep -v grep              \
    | pgrep                     \
    | wc -l                     \
    | sort                      \
    | uniq
```

## `awk(1)` for everything
But why?

```
$ du -sh Downloads | awk '{ print $1 }'
366G
```

```
$ folder_size=($(du -sh Downloads))
$ echo ${folder_size[1]}
Downloads
$ echo ${folder_size[0]}
366G
```

##

## Debugging is a mess

One of the reasons nobody should aim for big projects in Bash is that it
is terrible to debug, most of you will know this already.
\vfill

This project aims to make it easier for you to debug your scripts. By
writing beautiful, solid and testable code.

# Modern Bash scripting (Welcome to 2014!)
## Modern Bash scripting

Most people don't know that there are a lot of useful paradigms and
tools that are used for software engineering in serious languages
available also to Bash.

\vfill
Let's not kid ourselves: some Bash scripts will run in production, even
for years. They'd better work. And not take your business offline.


## Conventions
I've come up with a few conventions:

* use `#!/usr/bin/env bash`
* do **not** use TABs for (consistently use 2, 3 or 4 spaces)
* but conditional and loop clauses on the same line:     
  `if ..; then` instead of    
```
    if ...
    then
      ...
    fi
```
* there're no private functions in Bash, RedHat has a convention for
  that, prepend with two underscores `function __my_private_function()`
* as in Ruby, Python; don't use indents in switch (case) blocks
* always "escape" varabiles. Bad: `$MyVar`, Good: `${MyVar}`.

## DocOpt
DocOpt is a Command-line interface description language with support for
all popular programming languages.

* \url{http://docopt.org/}
* \url{https://github.com/docopt}

\vfill
..also for Bash

* \url{https://github.com/docopt/docopts}

## Test Driven Development and Unit tests with Bash
```
#!/usr/bin/env bats

@test "addition using bc" {
  result="$(echo 2+2 | bc)"
  [ "$result" -eq 4 ]
}

@test "addition using dc" {
  result="$(echo 2 2+p | dc)"
  [ "$result" -eq 4 ]
}
```
...

## Test Driven Development and Unit tests with Bash (cont.)

1. Sam Stephenson (of `rbenv` fame) wrote an automated testing
  system for Bash scripts called 'bats' using TAP (Test Anything Protocol): 
  \url{https://github.com/sstephenson/bats}
2. `Sharness`: another TAP library.  there's even a Chef cookbook for it: \url{https://github.com/mlafeldt/sharness}
3. `Cram`: a functional testing framework based on
   Marcurial's unified test format - \url{https://bitheap.org/cram/}
4. `rnt`: Automated testing of commandline interfaces - \url{https://github.com/roman-neuhauser/rnt}
5. `shUnit2`: is a xUnit framework (similar to PyUnit, JUnit et cetera) - \url{https://code.google.com/p/shunit2/}
6. `shpec`: Tests/Specs - \url{https://github.com/rylnd/shpec}

..there are more, but these I've found to be most useful.


## Linting
* A online Bash style linter: \url{https://github.com/koalaman/shellcheck}
* Ubuntu ships with a tool called `checkbashisms` based on Debians
  `lintian` (portability).
* `shlint` tests for portability between zsh, ksh, bash, dash and bourne
  shell (if need be): \url{https://github.com/duggan/shlint}
* For Node fans: Grunt task that checks if a Bash script is valid (not anything else, btw): \url{https://www.npmjs.com/package/grunt-lint-bash}

## Inter-shell portability
### Personal opinion:
Inter-shell portability doesn't matter. I've spent years writing OS agnostic
bourne-shell scripts. Today every modern OS ships with a reasonably
recent version of Bash. These days Solaris (and FOSS forks like SmartOS)
ship even with a GNU userland. Use Bash.

I love `zsh` and it can do a lot more. I still use Bash for (semi-)
production scripts. They run basically everywhere when done right.

## Defensive Bash programming
* As you would in every other language, write helper functions, test
  these functions.
* Set constants `readonly`.
* Write concise, well defined and tested functions for every action.
* Use the `local` keyword for function-local variables.
* Prepend every function with the `function` keyword.
* Return proper error codes and check for them.
* Write unit tests.
* Some people write a `function main()` as people would with Python.
  So one can import and test ones `main` call as well.

## Defensive Bash programming (cont.)
```
function fail() {
  local msg=${@}

  # handle failure appropriately
  cleanup && logger "my message to syslog"

  echo "ERROR: ${msg}"
  exit 1
}
```

et cetera

## Defensive Bash programming (cont.)
```
function linux_distro() {
  local releasefile=$(cat /etc/*release* 2> /dev/null)
  case ${releasefile} in
  *Debian*)             printf "debian\n" ;;
  *Suse*)               printf "sles\n"   ;;
  *CentOS* | *RedHat*)  printf "el\n"     ;;
  *)                    return 1          ;;
  esac
}

...
[[ $(linux_distro) ]] || fail "Unkown distribution!"
readonly linux_distro=$(linux_distro)
...
```

## Defensive Bash programming (cont.)
```
function debian_version() {
  # convert debian version to single unsigned integer
  local dv=$(printf "%.f" $(</etc/debian_version))
  printf "%u" ${dv}
}
```

## Defensive Bash programming (cont.)
```
function is_empty() {
  local var=${1}
  [[ -z ${var} ]]
}

function is_file() {
  local file=${1}
  [[ -f ${file} ]]
}

function is_dir() {
  local dir=${1}
  [[ -d ${dir} ]]
}
```


## Signal Handling

* Bash supports signal handling with the builtin `trap`:

```
# call the fail() function if one
# of these signals is caught by trap:
trap 'fail "caught signal!"' HUP KILL QUIT
```

## Anonymous Functions (Lambdas)

You'll probably never ever need this in Bash, but it's possible:

```
function lambda() {
  _f=${1} ; shift
  function _l {
    eval ${_f};
  }
  _l ${*} ; unset _l
}
```

## Bash Profiling
* Sam Stephenson also wrote a profiler for Bash scripts:
  \url{https://github.com/sstephenson/bashprof}


## Bash Debugging

Hopefully you'll write code that you do not have to debug often, but
eventually you'll have to. There's only one real way to debug a Bash
script unfortunately:

* `bash -evx script.sh`
* or setting `set -evx` in your script directly
* that being said, someone wrote a Bash debugger with `gdb` command
  syntax: \url{http://bashdb.sourceforge.net/}

# Conclusion
## Conclusion
* There's a lot more to tell (just ask me afterwards) - but this was
  supposed to be a lightning talk.
* All this, a lot of references and other projects are mentioned in my
  ``Community Bash Style Guide`` which is on GitHub.
* Please contribute in any way you can if you come up with useful
  Bashisms, tricks or find any cool projects.
* Any input is very much appreciated!

### Fork and open Pull Requests, Issues or Complaints! 
\url{https://github.com/azet/community_bash_style_guide}

## Trivia: **Do not try this at home**
\vfill
OOP in Bash: 

* \url{https://github.com/tomas/skull}
* \url{https://github.com/kristopolous/TickTick}
* \url{https://code.google.com/p/object-oriented-bash/}
* \url{https://github.com/patrickd-/ooengine}
* \url{http://hipersayanx.blogspot.co.at/2012/12/object-oriented-programming-in-bash.html}

\vfill
LISP Dialect implemented in Bash:

* \url{https://github.com/alandipert/gherkin}

\vfill

The original Macros used in the source of Bourne Shell (To make it look
like ALGOL68 - the author was a big fan):

* \url{http://research.swtch.com/shmacro}


