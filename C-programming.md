# C type refactoring
## Int types
- `int` with size semantics:
    * signedness conversion easy --> `size_t` <br/>
       (check for signedness conversion usual problems).
    * signedness conversion difficult --> `ssize` <br/>
       (for example, complicated code involving subtractions) 
- `int` without size semantics --> `int`

## Special cases
In spite of the general advice above, there are some cases where we prefer more tight typing.

### Struct fields
We should try to keep structs small, if possible. To that end:
- Use fixed width types (`int32_t`, `uint32_t`, etc.), if possible. This is:
- Do that only if you can be sure that the specified width will always be enough. So:
- Please don't impose arbitrary/unneeded limits on fields. 
- For example: If a field has clear size semantics and there's no particular reason to impose a restriction on it, use `size_t`/`ssize`.

### External interfaces
- For functions interfacing other processes over a transport/serialization mechanism, fixed-width types are preferred. For example, in `msgpack_rpc.h`:

```c
bool msgpack_rpc_integer_result(uint32_t result,
                                msgpack_object *req,
                                msgpack_packer *res);
```

- For functions part of a public API, native types are preferred. For example, in a hypothetical `libneovim.h`:

```c
int neovim_get_current_buffer(void);
```

## Type cascading
Once you have some input variable you have to deal with (be it a struct field, a function parameter, or even a global), the issue arises whether to cascade that type in code dealing with it, or using a wider type if considered better for some reason. Typical example is, once you have a fixed-width struct field, code dealing with it (function variables/parameters) should also use fixed-width types, or could types we widened to some other enclosing type? In principle, we say:
- If access to input variable is read-only, then it's safe to use wider types if some other reason makes them preferable, as you will be only upcasting the value.
- If access to input variable can be read/write, then intermediate variables/params should try to maintain input variable's type as much as possible. If not, you will end up with a downcast somewhere that will require an assert/some other kind of guard/error handling.
