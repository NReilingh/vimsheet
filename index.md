---
layout: default
title: A Reasonably Good Vim Cheat Sheet
---

# A Reasonably Good Vim Cheat Sheet

This is a remix of <http://vimsheet.com>.

This cheat sheet assumes that you already know how Vim works, conceptually speaking,
but want a quick reference for the default key mappings
and a way to remind yourself of some of the more clever things
that Vim is capable of.

In light of that, this sheet aims to be fairly thorough in its coverage,
but also to illustrate patterns in Vim's design that can make commands easier to remember.

Everything here should be compatible with stock Vim, and assumes that the reader
is more interested in maintaining compatibility with any given server they SSH into
than they are in having a super-tricked-out local development environment.
In practice, this means that plugins and `.vimrc` hacks are suggested to be used
only when they would not conflict with the muscle memory one builds up
making edits using stock vim.

From the author: I add content and revise sections of the original cheat sheet
as I delve into each feature and absorb those parts of the manual.
As you might expect, I am making this to be a useful reference for myself first and foremost.

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

* `H`, `M`, `L` - Screen-relative line navigation: Home (top), Middle, Last (bottom)
* Window Movement:
    * `zt`, `zz`, `zb` - Scroll cursor location to top, middle, bottom

#### Left-right Shortcuts

* `0` (zero) - start of line
* `^` - first non-blank character of line (similar to `0w` and `_`, but don't forget about `I`!)
* `$` - end of line
* `g_` - last non-blank character of line

#### Seeking Within a Line

* `f[char]` - Seek forwards to the next instance of `[char]`
* `F[char]` - Seek backwards to the next instance of `[char]`
* `t[char]` - Seek forwards until adjacent to the next instance of `[char]`
* `T[char]` - Seek backwards until adjacent to the next instance of `[char]`
* All these commands can be followed by `;` to advance the seek to the next instance in the "forwards" (lowercase) direction,
or `,` to advance the seek to the next instance in the "backwards" (uppercase) direction.
(A `[count]` prefix will not affect `;` or `,`.)

#### Larger Movements

* `(` `)` - go backward/forward by sentences
* `{` `}` - go backward/forward by paragraph (blank lines)
* `%` - go to next (or matching) `()`, `[]`, `{}`
* `-` - Like `k`, but on the first non-blank char of the line
* `+` - Like `j`, but on the first non-blank char of the line
* `_` - Like `+`, but with -1 to `[count]`

#### More Advanced

* `<C-d>` - move down half a page
* `<C-u>` - move up half a page
* `[num]G`, `:[num]<Enter>` - go to line number `[num]`
* `G` - go the bottom of the page
* `gg` - go to the top of the page
* Jumplist feature (NOT motion commands!)
    * `<C-o>` - Jump to previous (older) cursor position
    * `<C-i>` or `<Tab>` - Jump to newer cursor position
    * `:ju` - Show jumplist

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

### Single-character edits

* `x` - Remove the character under the cursor
* `X` - Remove the character to the left of the cursor
* `r[char]` - replace a single character with the specified `[char]` (does not use Insert mode)
* `R` - Enter REPLACE mode, which is like INSERT mode but with overtype
* Advanced
    * `J` - Join current line to the line below
    * `r<CR>` - "Split line" at cursor, assuming space selected
    * `s<CR>` - As above, but remain in insert mode

### Multiple-character Edits

|                                    | Yank (y)    | Delete (d) | Change (c) - Delete and enter insert mode |
|------------------------------------|-------------|------------|-------------------------------------------|
| `[noun]`                           | `y[noun]`   | `d[noun]`  | `c[noun]`                                 |
| entire line                        | `yy` or `Y` | `dd`       | `cc`                                      |
| from cursor to the end of the line | `y$`        | `D`        | `C`                                       |

A noun can be a motion, like `2W` (two whitespace-delimited words) or a text object like `it` (inner tag block).
See also [Text Objects](#objects).

* `>[noun]`, `<[noun]` - Shift `[noun]` forward/backward by one `'shiftwidth'`.
* `>>`, `<<` - Shift line forward/backward by one `'shiftwidth'`.

### Undo/Repeat

* `u` - undo
* `U` - Undo entire line
* `<C-r>` - redo
* `.` - repeat last change

## Insert Mode [incomplete]

* `<C-[>` or `<Esc>` - exit insert mode
* `<C-r>[register]` - Insert at the cursor from the register indicated (no `"` prefix)

## Visual Mode

* `v` - starts visual mode
    * From here you can move around as in normal mode (`h`, `j`, `k`, `l` etc.) and can then do a command (such as `y`, `d`, or `c`)
* `V` - starts linewise visual mode
* `<C-v>` - start visual block mode
* `gv` - After doing a command that leaves you in normal mode, return to the same visual mode with the same selection
* `<C-[>` or `<Esc>` - exit visual mode
* Motions in visual mode
    * Use characterwise and linewise motions listed above to select, starting from initial position
    * `o` - Move cursor to opposite corner of the visual selection
    * `O` - In VISUAL BLOCK mode, move cursor to opposing corner _within the same line_
    * Also use [text objects](#objects) to expand selection

## Visual commands

Type any of these while some text is selected to apply the action

* `v`, `V`, `<C-v>` - Switch to a different visual mode, as above. Match current mode to return to normal mode.
* `y` - yank (copy) selected text
* `d` - delete selected text
* `c` - delete the selected text and go into insert mode (like c does above)
* `p` - Replace with register
* `r[char]` - Replace selection with `[char]`
* `I` - Block insert
* `A` - Block append
* `U`, `u` - Uppercase/lowercase
* `J` - Join lines
* `>`, `<` - Shift right/left by one `'shiftwidth'` (don't forget about `[count]` prefix)
* Advanced
    * `:` - Enter command mode with `'<,'>` already prepended,
    which is a range of automatic marks representing the current selection.
    * `:norm [commands]` - Execute a normal mode command sequence on the visual selection.
    This can include inserts!
    * `<C-v>[keystroke]` - While in command mode (or insert mode),
    prefix a control character that you want inserted literally.
    * Example: you have a visual linewise selection and you want to quote each line:
        * Type `:norm I"<C-v><Esc>A"`
        * The command line will display: `:'<,'>norm I"^[A"`.
        The `^[` sequence will appear in a different color if your terminal supports it.
        * This translates to: execute command mode (`:`) on the range defined by the start and end
        of visual selection marks (`'<,'>`), run a normal mode command on each line (`norm`);
        that command says: enter insert mode at the start of the line (`I`), insert a quote character (`"`),
        enter the escape control character (`^[`) to leave insert mode
        (remember `<Esc>` and `<C-[>` are synonymous, and `^` is a shorthand for Control-key),
        append to line (`A`), and insert a quote character (`"`).
        * `:norm` technically adds the final `<Esc>` at the end of the insert automatically,
        so after running the command, you will not remain in insert mode.


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
* `"`, `'`, `` ` `` - Quoted strings (only works within one line)
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

* `*` - Search forward for the word nearest to the cursor
* `#` - Search backward for the word nearest to the cursor
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
* `<C-t>` - Jump back (uses `tag-stack`)
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
when remoting into other machines.
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
* [How to Do 90% of What Plugins Do (With Just Vim)](https://www.youtube.com/watch?v=XA2WjJbmmoM)
