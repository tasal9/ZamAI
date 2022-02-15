## Pull requests

"Cleanup" PRs should include some unit tests that cover the function being changed, so that we can feel more confident about regressions, future changes, and Vim-patch merges.

For example if the [coverage report](https://codecov.io/gh/neovim/neovim/src/master/src/nvim/buffer.c) shows that `build_stl_str_hl` is not covered by our tests, any "cleanup" PR for `build_stl_str_hl` should include unit-test coverage.

## Frozen legacy modules

Refactoring Vim structurally and aesthetically is an important goal of Neovim. But there are some modules that should not be changed significantly, because they are maintained only by Bram, at present. Until someone takes "ownership" of these modules, the cost of any significant changes (including style or structural changes that re-arrange the code) to these modules outweighs the benefit. The modules are:

- `regexp.c`
- `regexp_bt.c` 
- `regexp_nfa.c`
- `indent_c.c`

