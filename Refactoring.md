## Frozen legacy modules

Refactoring Vim structurally and aesthetically is an important goal of Neovim.
But there are some modules that should not be changed significantly, because
they are maintained Vim, at present. Until someone takes "ownership" of these
modules, the cost of any significant changes (including style or structural
changes that re-arrange the code) to these modules outweighs the benefit. The
modules are:

- `regexp.c`
- `indent_c.c`

