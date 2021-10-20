# How to update Neovim's `:help` documentation

## Lua docs

### Regenerating

Use the `./scripts/gen_vimdoc.py` script for that.
You can filter the regeneration based on the target (api, lua, or lsp), or the file you changed, that need a doc refresh.

### Doc comments

A doc comment in Lua should roughly follow this template:

```lua
--- {Brief}
---
--- {Long explanation}
---
---@param arg1 {description}
---@param arg2 {description}
{...}
---
---@return {description}
```

If a function in your Lua module should not be documented (e.g. internal function or local function), you should set the doc comment to:

```
---@private
```
