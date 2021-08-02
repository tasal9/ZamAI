## Standard

For standard configuration (be it in **Vimscript** or **Lua**) see :help config.

You can copy your existing vimrc, or symlink to it. :help nvim-from-vim

## Alternatives

With **Lua** being a first class "citizen", you can pretty much use for configuration (or plugins!) anything that transpiles to it such as [Fennel](https://fennel-lang.org/), [Teal](https://github.com/teal-language/tl), [YueScript](https://github.com/pigpigyyy/Yuescript) or any other.

### Fennel

Thanks to [Hotpot](https://github.com/rktjmp/hotpot.nvim) you are able to run **Fennel** anywhere you would run **Lua** (just replace your `lua/*.lua` files with `fnl/*.fnl`). See the project `README` for details, and [reddit.com/r/neovim/comments/otvt0q/hotpot_seamless_fennel_in_neovim_yafp](https://www.reddit.com/r/neovim/comments/otvt0q/hotpot_seamless_fennel_in_neovim_yafp/) for an excellent introduction to both **Hotpot** and **Fennel** but it all boils down to: 1) add the `hotpot` package to your `init.{vim,lua}` and 2) start writing fennel.

If you already have your config in **Lua** you can convert it to **Fennel** automatically, using [antifennel](https://git.sr.ht/~technomancy/antifennel). And since you most likely want to keep your config tidy (who doesn't?), you would also benefit from using the automatic formatting provided by [fnlfmt](https://git.sr.ht/~technomancy/fnlfmt).

An alternative to using **Hotpot** is [Aniseed](https://github.com/Olical/aniseed).

---

(if you have used any other alternate way to configure Neovim, please consider contributing a section to this document)