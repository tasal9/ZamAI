# Guidelines for removing char_u

* Most `char_u*` storage can be cast to `char*`, and vice versa.
    * But `char**` cannot be cast to `char_u**`, and vice versa. Under C "strict aliasing" rules, accessing a `char_u*` via pointer to `char*`, is undefined behavior. 
        * https://github.com/neovim/neovim/issues/19125
        * http://port70.net/~nsz/c/c99/n1256.html#6.5p7