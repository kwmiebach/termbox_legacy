
# Termbox Legacy

While this fork has been renamed as Termbox Legacy, it is important to note that the internal naming conventions within the codebase will be retained for the sake of compatibility with existing projects.

For a seamless transition, and to maintain proximity to the original library, clone this repository using the following command:
`git clone https://github.com/kwmiebach/termbox_legacy termbox`

This ensures that even the project directory mirrors that of the original.

**From the README of the last known commit of the original https://github.com/nfs/termbox project:**

IMPORTANT

This library is no longer maintained. It's pretty small if you have a big
project that relies on it, just maintain it yourself. Or look for forks. Or look
for alternatives. Or better - avoid using terminals for UI. Never the less, thanks
to all who ever contributed.

## Termbox

Termbox is a library that provides minimalistic API which allows the
programmer to write text-based user interfaces.

It is based on a very simple abstraction. The main idea is viewing terminals as
a table of fixed-size cells and input being a stream of structured
messages. Would be fair to say that the model is inspired by windows console
API. The abstraction itself is not perfect and it may create problems in certain
areas. The most sensitive ones are copy & pasting and wide characters (mostly
Chinese, Japanese, Korean (CJK) characters). When it comes to copy & pasting,
the notion of cells is not really compatible with the idea of text. And CJK
runes often require more than one cell to display them nicely. Despite the
mentioned flaws, using such a simple model brings benefits in a form of
simplicity. And KISS principle is important.

At this point one should realize, that CLI (command-line interfaces) aren't
really a thing termbox is aimed at. But rather pseudo-graphical user interfaces.

### Installation

Termbox comes with a waf-based build scripts.

You need to install python3 and sudo first.

If a `python` symlink (without 3 at the end) is not present on
the system, you need to create one:

`sudo ln -s /usr/bin/python3 /usr/bin/python`

This is needed in the build process.


In order to configure, build and
install, do the following::

```
./waf configure --prefix=/usr                                (configure)
./waf                                                        (build)
./waf install --destdir=DESTDIR                              (install)
```

By default termbox will install the header file and both shared and static
libraries. If you want to install a shared library or static library alone, use
the following as an install command::

```
./waf install --targets=termbox_shared --destdir=PREFIX      (shared library)
```

or::

```
./waf install --targets=termbox_static --destdir=PREFIX      (static library)
```

##### Python

In order to install the python module, use the following command (as root or
via sudo)::

```
python setup.py install
```

for Python 3::

```
python3 setup.py install
```

### Getting started

Termbox's interface only consists of 12 functions::

```
tb_init() // initialization
tb_shutdown() // shutdown

tb_width() // width of the terminal screen
tb_height() // height of the terminal screen

tb_clear() // clear buffer
tb_present() // sync internal buffer with terminal

tb_put_cell()
tb_change_cell()
tb_blit() // drawing functions

tb_select_input_mode() // change input mode
tb_peek_event() // peek a keyboard event
tb_poll_event() // wait for a keyboard event
```

See src/termbox.h header file for full detail.

### Docker

Include something like this in your Dockerfile. The example
is taken from a Dockerfile based on `elixir:1.14.1`:

```
USER root
WORKDIR /opt
# waf wants python, python3 should work.
# it also uses sudo, so install it too.
RUN apt-get update && apt-get install -y python3 sudo \
    && ln -s /usr/bin/python3 /usr/bin/python \
    && git clone https://github.com/nsf/termbox.git \
    && cd termbox \
    && ./waf configure \
    && ./waf \
    && sudo ./waf install
# To use termbox_legacy replace the git clone command above with:
#   && git clone https://github.com/kwmiebach/termbox_legacy termbox \

# The sudo ./waf install command is supposed to
# install the header files to the appropriate system directory
# (typically /usr/local/include), but to make sure they
# are correctly installed, you can explicitly copy them:
# RUN cp src/termbox.h /usr/local/include/
RUN chmod 644 /usr/local/include/termbox.h
```

### Links

If you want me to add your Termbox project here, send me a pull request or drop
a note via email, you can find my email below.

##### Language bindings

- https://github.com/nsf/termbox - Python
- https://github.com/adsr/termbox-php - PHP
- https://github.com/gchp/rustbox - Rust
- https://github.com/fouric/cl-termbox - Common Lisp
- https://github.com/zyedidia/termbox-d - D
- https://github.com/dduan/Termbox - Swift
- https://github.com/andrewsuzuki/termbox-crystal - Crystal
- https://github.com/jgoldfar/Termbox.jl - Julia
- https://github.com/mitchellwrosen/termbox - Haskell
- https://github.com/dom96/nimbox - Nim
- https://github.com/ndreynolds/ex_termbox - Elixir
- https://github.com/bmsauer/termbox-ada - Ada
- https://github.com/luxint/termbox - newLISP

##### Other implementations

- https://github.com/nsf/termbox-go - Go pure Termbox implementation

##### Applications

- https://github.com/adsr/mle - a small, flexible terminal-based text editor
- https://github.com/colinta/Ashen - framework for building terminal applications based on the Elm architecture
- https://github.com/afify/sfm - simple file manager for unix-like systems

### Bugs & questions

Report bugs to the https://github.com/nsf/termbox issue tracker. Send rants
and questions to me: no.smile.face@gmail.com.

### Changes

v1.1.2:

- Properly include changelog into the tagged version commit. I.e. I messed up
  by tagging v1.1.1 and describing it in changelog after tagged commit. This
  commit marked as v1.1.2 includes changelog for both v1.1.1 and v1.1.2. There
  are no code changes in this minor release.

v1.1.1:

- Ncurses 6.1 compatibility fix. See https://github.com/nsf/termbox-go/issues/185.

v1.1.0:

- API: tb_width() and tb_height() are guaranteed to be negative if the termbox
  wasn't initialized.
- API: Output mode switching is now possible, adds 256-color and grayscale color
  modes.
- API: Better tb_blit() function. Thanks, Gunnar Zötl <gz@tset.de>.
- API: New tb_cell_buffer() function for direct back buffer access.
- API: Add new init function variants which allow using arbitrary file
  descriptor as a terminal.
- Improvements in input handling code.
- Calling tb_shutdown() twice is detected and results in abort() to discourage
  doing so.
- Mouse event handling is ported from termbox-go.
- Paint demo port from termbox-go to demonstrate mouse handling capabilities.
- Bug fixes in code and documentation.

v1.0.0:

- Remove the Go directory. People generally know about termbox-go and where
  to look for it.
- Remove old terminfo-related python scripts and backport the new one from
  termbox-go.
- Remove cmake/make-based build scripts, use waf.
- Add a simple terminfo database parser. Now termbox prefers using the
  terminfo database if it can be found. Otherwise it still has a fallback
  built-in database for most popular terminals.
- Some internal code cleanups and refactorings. The most important change is
  that termbox doesn't leak meaningless exported symbols like 'keys' and
  'funcs' now. Only the ones that have 'tb_' as a prefix are being exported.
- API: Remove unsigned ints, use plain ints instead.
- API: Rename UTF-8 functions 'utf8_*' -> 'tb_utf8_*'.
- API: TB_DEFAULT equals 0 now, it means you can use attributes alones
  assuming the default color.
- API: Add TB_REVERSE.
- API: Add TB_INPUT_CURRENT.
- Move python module to its own directory and update it due to changes in the
  termbox library.
