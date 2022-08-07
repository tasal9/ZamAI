Here are a few hints for finding your way around the source code.  This
doesn't make it less complex than it is, but it gets you started.

## Jumping around

Use `ctags -R` to generate a tags file for the `:tag` command. (We recommend [universal-ctags](https://github.com/universal-ctags/ctags) instead of the default `ctags` provided by most distros; see also [CONTRIBUTING.md](https://github.com/neovim/neovim/blob/master/CONTRIBUTING.md#navigate).)

To jump to a function or variable definition, move the cursor on the name and
use the `CTRL-]` command.  Use `CTRL-T` or `CTRL-O` to jump back.

To jump to a file, move the cursor on its name and use the `gf` command.

Most code can be found in a file with an obvious name (incomplete list):
*   [buffer.c](../blob/master/src/nvim/buffer.c)	   manipulating buffers (loaded files)
*   [diff.c](../blob/master/src/nvim/diff.c)	   diff mode (vimdiff)
*   [eval.c](../blob/master/src/nvim/eval.c)	   expression evaluation
*   [fileio.c](../blob/master/src/nvim/fileio.c)	   reading and writing files
*   [fold.c](../blob/master/src/nvim/fold.c)	   folding
*   [getchar.c](../blob/master/src/nvim/getchar.c)  character input
*   [mark.c](../blob/master/src/nvim/mark.c)	   marks
*   [mbyte.c](../blob/master/src/nvim/mbyte.c)	   multi-byte character handling
*   [memfile.c](../blob/master/src/nvim/memfile.c)  storing lines for buffers in a swapfile
*   [memline.c](../blob/master/src/nvim/memline.c)  storing lines for buffers in memory
*   [menu.c](../blob/master/src/nvim/menu.c)	   menus
*   [message.c](../blob/master/src/nvim/message.c)  (error) messages
*   [ops.c](../blob/master/src/nvim/ops.c)          handling operators (`d`, `y`, `p`)
*   [option.c](../blob/master/src/nvim/option.c)	   options
*   [quickfix.c](../blob/master/src/nvim/quickfix.c) quickfix commands (`:make`, `:cn`)
*   [regexp.c](../blob/master/src/nvim/regexp.c)	   pattern matching
*   [screen.c](../blob/master/src/nvim/screen.c)	   updating the windows
*   [search.c](../blob/master/src/nvim/search.c)	   pattern searching
*   [spell.c](../blob/master/src/nvim/spell.c)	   spell checking
*   [syntax.c](../blob/master/src/nvim/syntax.c)	   syntax
*   [tag.c](../blob/master/src/nvim/tag.c)	   tags
*   [terminal.c](../blob/master/src/nvim/terminal.c)	   integrated terminal emulator
*   [undo.c](../blob/master/src/nvim/undo.c)	   undo and redo
*   [window.c](../blob/master/src/nvim/window.c)	   handling split windows
	

## Important variables

The current mode is stored in `State`.  The values it can have are `MODE_NORMAL`,
`MODE_INSERT`, `MODE_CMDLINE`, and a few others.

The current window is `curwin`.  The current buffer is `curbuf`.  These point
to structures with the cursor position in the window, option values, the file
name, etc.

All the global variables are declared in [globals.h](../blob/master/src/nvim/globals.h).


## The main loop

The main loop is implemented in state_enter. The basic idea is that Vim waits
for the user to type a character and processes it until another character is
needed.  Thus there are several places where Vim waits for a character to be
typed.  The `vgetc()` function is used for this.  It also handles mapping.

Updating the screen is mostly postponed until a command or a sequence of
commands has finished.  The work is done by `update_screen()`, which calls
`win_update()` for every window, which calls `win_line()` for every line.
See the start of [screen.c](../blob/master/src/nvim/screen.c) for more explanations.


## Command-line mode

When typing a `:`, `normal_cmd()` will call `getcmdline()` to obtain a line with
an Ex command.  `getcmdline()` calls a loop that will handle each typed
character.  It returns when hitting `<CR>` or `<Esc>` or some other character that
ends the command line mode.


## Ex commands

Ex commands are handled by the function `do_cmdline()`.  It does the generic
parsing of the `:` command line and calls `do_one_cmd()` for each separate
command.  It also takes care of while loops.

`do_one_cmd()` parses the range and generic arguments and puts them in the
exarg_t and passes it to the function that handles the command.

The `:` commands are listed in [ex_cmds.lua](../blob/master/src/nvim/ex_cmds.lua). 


## Normal mode commands

The Normal mode commands are handled by the `normal_cmd()` function.  It also
handles the optional count and an extra character for some commands.  These
are passed in a `cmdarg_T` to the function that handles the command.

There is a table `nv_cmds` in [normal.c](../blob/master/src/nvim/normal.c) which 
lists the first character of every
command.  The second entry of each item is the name of the function that
handles the command.


## Insert mode commands

When doing an `i` or `a` command, `normal_cmd()` will call the `edit()` function.
It contains a loop that waits for the next character and handles it.  It
returns when leaving Insert mode.


## Options

There is a list with all option names in [options.lua](../blob/master/src/nvim/options.lua).

