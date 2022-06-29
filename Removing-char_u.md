Guidelines for removing char_u.

* Most `char_u*` storage can be cast to `char*`, and vice versa.
    * But `char**` cannot be cast to `char_u**`, and vice versa. Under C "strict aliasing" rules, accessing a `char_u*` via pointer to `char*`, is undefined behavior. 
        * https://github.com/neovim/neovim/issues/19125
        * http://port70.net/~nsz/c/c99/n1256.html#6.5p7
* `char_u` <=> `char` is allowed, because `unsigned char` (`char_u`) can alias anything. The signed type corresponding to `unsigned char` isn't `char`, it's `signed char`.
* C quirk: `char`, `signed char`, and `unsigned char` are three separate types. But `int` and `signed int` are the same type!