## Refactoring Vim into a library

Here's an idea of how Vim reads input and transitions between modes. The actual code is much more complex but follows this basic flow:

```c
while (1) {
  normal_mode_process_byte(getc());
}

void normal_mode_process_byte(char c) {
  if (c == 'i') // enter insert mode
    while (1) {
	  insert_mode_process_byte(getc());
	}
}
```

What needs to be done is remove all those read loops and replace by a function (e.g., `process_byte(char c)`) that will buffer input, delegate to mode-specific input-handling functions or transition editor state when necessary. Something like this:

```c
void process_byte(char c) {
  if (mode == NORMAL)
    normal_mode_process_byte(c);
  else if (mode == INSERT)
    insert_mode_process_byte(c);
  ...
}

void normal_mode_process_byte(char c) {
  if (c == 'i') {
    mode = INSERT;
    return;
  }
  ...
}
```

Think of the editor as a state machine: when input is read and fed into the machine, it may change the active buffer, transition mode, etc. The point is: the machine shouldn't call IO functions directly


## Text/buffer representation

Arguably, wrangling and displaying text are the most important things a text editor does. Vim represents text as a sequence of lines (see `mem{line,file}.{c,h}`, needs extra verification).

Operations on lines are thus easy and efficient. It seems to be a good fit for the many operations Vim supports (undo/redo, text operators, ...). Even operations that are not necessarily line-based, like block mode operations, are easy to implement over a line-based API.

Yet it is always instructive to learn about how other text editors tackle the issue.

One of the disadvantages of the line-based approach seems to be that Vim has problems efficiently handling files that have no/little newlines. Think of large XML files that curiously have no newlines, it is often slow. Syntax highlighting is likely to blame in this case. More research is necessary to discover if this is can be remedied within the line-based structure, or if a different approach is necessary.

Some alternative approaches:

### Piece Buffer

As described in "Data Structures for Text Sequences" [comments](https://news.ycombinator.com/item?id=8827887), [pdf](https://www.cs.unm.edu/~crowley/papers/sds.pdf). Notably implemented in the [vis](https://github.com/martanne/vis) text editor, which supports many Vim-like operators.

Advantages:
- Can `mmap()` the [file into memory](http://lists.suckless.org/dev/1409/23497.html). This is the best way to seamlessly support editing huge files, especially on OS'es that do demand paging (all Neovim supported platforms do that).

Disadvantages:
- As noted by the **vis** author, text encoding conversion undoes the `mmap()` advantage mentioned above. A workaround might be possible, but it would really complicate the implementation. One idea for making it work is to make an overlay, see [neovim/#1767](https://github.com/neovim/neovim/issues/1767).

### Rope

See [wikipedia](http://en.wikipedia.org/wiki/Rope_%28data_structure%29). TODO: more discussion on how it could be applied to Neovim.

### Gap Buffer

See [wikipedia](http://en.wikipedia.org/wiki/Gap_buffer). TODO: more discussion on how it could be applied to Neovim.
