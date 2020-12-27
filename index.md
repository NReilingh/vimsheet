---
layout: default
title: A Reasonably Good Vim Cheat Sheet
---

# A Reasonably Good Vim Cheat Sheet

This is a remix of <http://vimsheet.com>.

This cheat sheet aims to cover the essentials accurately and somewhat thoroughly,
and to illustrate patterns in Vim's design that can make commands easier to remember.

Everything here should be compatible with stock Vim, and assumes that the reader
is more interested in maintaining compatibility with any given server they SSH into
than they are in having a super-tricked-out local development environment.
In practice, this means that plugins are suggested to be used only when they would
not conflict with the muscle memory one builds up making edits using stock vim.

As is surely the case with many Cheat Sheet projects,
working on this document was used as a way to learn Vim by one of the authors.

### Notation

The notational convention here is to use the same syntax for combination shortcuts
as would be used when writing a key mapping or macro.
That is, instead of `Ctrl+r` or `CTRL-R`, we will write `<C-r>`.
See also `:help key-notation`.

* `<S-...>` - Shift-key
* `<C-...>` - Control-key
* Other modifiers tend to have support issues depending on your platform,
so you probably won't use them, but here's what the notation is:
  * `<M-...>` - Meta-key
  * `<A-...>` - Alt-key
  * `<D-...>` - Command-key (macOS)

## Normal Mode - Motions

### Cursor Movement

Any motion (or command) can be prefixed with a number `[count]` to iterate it that many times.

<img src="images/hjkl.png" alt="The four directions in Vim, keys h, j, k, and l."/>

| Words separated by:        | punctuation | whitespace |
|----------------------------|-------------|------------|
| Start of next word         | `w`         | `W`        |
| End of next word           | `e`         | `E`        |
| Beginning of previous word | `b`         | `B`        |
| End of previous word       | `ge`        | `gE`       |

* `0` (zero) - start of line
* `^` - first non-blank character of line (similar to `0w`, but don't forget about `I`!)
* `$` - end of line

#### More Advanced

* `<C-d>` - move down half a page
* `<C-u>` - move up half a page
* `(` `)` - go backward/forward by sentences
* `{` `}` - go backward/forward by paragraph (blank lines)
* `%` - go to start of innermost, or matching, `()` `[]` `{}`
* `[num]G`, `:[num]<Enter>` - go to line number `[num]`
* `G` - go the bottom of the page
* `gg` - go to the top of the page
* `-` - Like `k`, but on the first non-blank char of the line
* `+` - Like `j`, but on the first non-blank char of the line
* `_` - Like `+`, but with -1 to `[count]`

#### Seeking within a line

* `f[char]` - Seek forwards to the next instance of `[char]`
* `F[char]` - Seek backwards to the next instance of `[char]`
* `t[char]` - Seek forwards until adjacent to the next instance of `[char]`
* `T[char]` - Seek backwards until adjacent to the next instance of `[char]`
* All these commands can be followed by `;` to advance the seek to the next instance in the "forwards" (lowercase) direction,
or `,` to advance the seek to the next instance in the "backwards" (uppercase) direction.
(The _n_ prefix will not affect `;` or `,`.)

## Normal Mode - Editing Text

Be sure to see [Registers](#registers) below for how they work with yanked and deleted text.

* `p` - Put (paste) the unnamed register _after_ the cursor (or current line)
* `P` - Put (paste) the unnamed register _before_ the cursor (or current line)

Put cursor/line behavior depends on whether the the register contains characterwise or linewise text.

### Entering Insert Mode

|                | lowercase | uppercase |
| ---------------|-----------|-----------|
| Insert (i)     | `i` - insert in front of cursor | `I` - ... in front of the first non-whitespace in the line
| Append (a)     | `a` - insert _after_ the cursor | `A` - ... at the _end_ of the line
| Open (o)       | `o` - insert to a new line _below_ the cursor | `O` - ... _above_ the cursor
| Substitute (s) | `s` - remove char at the cursor first | `S` - Clear entire line first (same as `cc`)

`I` and `A` are also used in VISUAL BLOCK mode to insert before or after the block selection.

### Single-character edits

* `x` - Remove the character under the cursor
* `X` - Remove the character to the left of the cursor
* `r[char]` - replace a single character with the specified `[char]` (does not use Insert mode)
* `R` - Enter REPLACE mode, which is like INSERT mode but with overtype

* Advanced
    * `J` - Join line below to the current one

### Multiple-character Edits

|                                    | Yank (y)    | Delete (d) | Change (c) - Delete and enter insert mode |
|------------------------------------|-------------|------------|-------------------------------------------|
| `[noun]`                           | `y[noun]`   | `d[noun]`  | `c[noun]`                                 |
| entire line                        | `yy` or `Y` | `dd`       | `cc`                                      |
| from cursor to the end of the line | `y$`        | `D`        | `C`                                       |

A noun can be a motion, like `2W` (two whitespace-delimited words) or a text object like `it` (inner tag block).
See also [Text Objects](#objects).

### Undo/Repeat

* `u` - undo
* `<C-r>` - redo
* `.` - repeat last change

## Insert Mode

* `<C-[>` or `<Esc>` - exit insert mode
* `<C-r>[register]` - Insert at the cursor from the register indicated (no `"` prefix)

## Visual Mode

* `v` - starts visual mode
    * From here you can move around as in normal mode (`h`, `j`, `k`, `l` etc.) and can then do a command (such as `y`, `d`, or `c`)
* `V` - starts linewise visual mode
* `<C-v>` - start visual block mode
* `<C-[>` or `<Esc>` - exit visual mode
* Advanced
    * `o` - Move cursor to opposite corner of the visual selection
    * `O` - In VISUAL BLOCK mode, move cursor to opposing corner _within the same line_

## Visual commands

Type any of these while some text is selected to apply the action

* `y` - yank (copy) marked text
* `d` - delete marked text
* `c` - delete the marked text and go into insert mode (like c does above)

## Registers<a name="registers"></a>

Prefix most yank/delete/change commands with `"[reg]` where `[reg]` is a specific register to copy to or from.

* `:reg` - View the current registers
* `""` - The unnamed register: default source of puts (p, P), acts like a pointer to the most recently written register
* `"-` - Default register for deletes less than a full line
* `"0` - Default destination of yanks
* `"1` - Default destination of deletes of at least a full line; also some specific motion nouns
* `"[2-9]` - Numbered history registers; shifted into from `"1` whenever it is written to
* `"[a-z]`, `"[A-Z]` - Named registers. Use uppercase variants to append.
* `"+` - System pasteboard; access to copy/paste from the system.

## Text Objects<a name="objects"></a>

Objects must be prefixed with `a` or `i`, for "a" ("an") object or "inner" object.

* `w`, `W` - word or Word (as above)
* `s` - Sentence
* `p` - Paragraph
* `"` - Double-quoted string
* `'` - Single-quoted string
* `` ` `` - Backquoted string
* `(`, `)`, or `b` - Parenthesized block
* `[` or `]` - Bracketed block
* `{` or `}` - Braced block
* `t` - XML-style tag block
* `<` or `>` - XML-style tag itself

In practice, use these objects in context of another command, with the a/i prefix, like `cib`.

## Exiting

* `:w` - write (save) the file, but don't exit
* `:wq` - write (save) and quit
* `:q` - quit (fails if anything has changed)
* `:q!` - quit and throw away changes
* `:wqa` - Write and quit all open tabs (thanks Brian Zick)
* `:x` - Write and exit, but only write if there are changes

## Search/Replace [unrevised]

* `/pattern` - search for pattern
* `?pattern` - search backward for pattern
* `n` - repeat search in same direction
* `N` - repeat search in opposite direction
* `:%s/old/new/g` - replace all old with new throughout file ([gn](http://vimcasts.org/episodes/operating-on-search-matches-using-gn/) is better though)
* `:%s/old/new/gc` - replace all old with new throughout file with confirmations

## Working with multiple files - Tabs [unrevised]

* `:e filename` - Edit a file
* `:tabe` - Make a new tab
* `gt` - Go to the next tab
* `gT` - Go to the previous tab
* Advanced
    * `:vsp` - vertically split windows
    * `ctrl+ws` - Split windows horizontally
    * `ctrl+wv` - Split windows vertically
    * `ctrl+ww` - switch between windows
    * `ctrl+wq` - Quit a window

## Working with multiple files - Buffers [unrevised]

* `:enew` - edit a new, unnamed buffer
* `:ls` - list all open buffers
* `:bn` - go to the next buffer
* `:bp` - go to the previous buffer
* `:b[N]` - goto buffer numbered N
    * ex. `:b2` will bring buffer numbered 2 to your tab
* `:b filename-prefix` - goto buffer with the corresponding filename-prefix
* `Ctrl+^` - Toggle between current and last buffer
* `Ctrl+o` - Go to the previous buffer you were in
* `Ctrl+i` - Reverse the effect of `Ctrl+o`

## Marks

Marks allow you to jump to designated points in your code.

* `m[a-z]` - Set mark `[a-z]` at cursor position
* A capital mark `[A-Z]` sets a global mark and will work between files
    * Hint: use a global mark like `'V` as a shortcut to your `.vimrc` file.
* `'[a-z]` - move the cursor to the start of the line where the mark was set
* `''` - go back to the previous jump location

## Vim Help

* `:help [topic]` - Search help pages for topic
    * `[topic]` can be a keystroke like `CTRL-R` (shorthand like `^r` is also accepted)
* Keystrokes can be prefixed to specify specific modes other than normal:
    * `v_` - Visual mode prefix
    * `i_` - Insert mode prefix
    * `c_` - Command mode prefix
* `<C-]>` - Jump to keyword
* Try `:help help-summary` for more tips

# Other Tips

## .vimrc

On macOS, Vim's included `.vimrc` file turns on a lot of convenient features like mouse support.
You might want to make some of the following tweaks.
Remember that the `<Leader>` key, by default, is `\`.

### Reload `.vimrc` on Save

    if has("autocmd")
        autocmd BufWritePost .vimrc source $MYVIMRC
    endif

### Line Numbering

    set number
    set relativenumber

### Visible Whitespace

    set listchars=tab:\|\ ,space:·
    set list
    " Set a shortcut to toggle visible whitespace on and off.
    nmap <Leader>l :set list!<CR>

### Stop Highlighting After Search

    " Must be double-escape to avoid conflicting with mouse scroll
    nmap <Esc><Esc>:noh<CR><Esc>

## Plugins [incomplete]

[vim-plug](https://github.com/junegunn/vim-plug)

## Map Caps Lock to Control

No, not Escape. You don't actually need to use escape if you use `<C-[>` instead.
On [the machine](https://en.wikipedia.org/wiki/ADM-3A) that Bill Joy originally used to write `vi`,
the control key was placed where caps lock is on most keyboards today.
Escape was placed where the tab key is today, so if you want to be totally based,
you could try swapping those.

Some people also like mapping `jk` to exit insert mode.
The problem with this is that you would have to accomplish it in your `.vimrc`,
and this would violate our stated intention of not messing with muscle memory
when SSHing into servers.
The Caps Lock/Tab remaps above can be accomplished on the operating system level,
or better yet on the keyboard hardware level, so they don't depend on the particular
`.vimrc` file in the environment you are interacting with.

## See Also

* Clickable [Vim mode state diagram](https://gist.github.com/darcyparker/1886716/raw/c1ee7657010278a787c6502b796a6766a40d56aa/vimModeStateDiagram.svg)

## References

* [Vim Text Objects: The Definitive Guide](https://blog.carbonfive.com/vim-text-objects-the-definitive-guide/)
* [Vim as a Language](https://benmccormick.org/2014/07/02/learning-vim-in-2014-vim-as-language)
* [Vim Registers](https://www.brianstorti.com/vim-registers/)
* [Mastering the Vim Language](https://www.youtube.com/watch?v=wlR5gYd6um0)
