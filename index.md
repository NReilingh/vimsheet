---
layout: default
title: A Reasonably Good Vim Cheat Sheet
---

# A Reasonably Good Vim Cheat Sheet

Note: If you’re decent at Vim and want your mind blown, check out [Advanced Vim](advanced.html).

This cheat sheet aims to cover the essentials accurately and somewhat thoroughly,
and to illustrate patterns in Vim's design that can make commands easier to remember.

Everything here should be compatible with stock Vim, and assumes that the reader
is more interested in maintaining compatibility with any given server they SSH into
than they are in having a super-tricked-out local development environment.

Overhauling this document was used as a way to learn Vim by one of the authors.

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

* `0` - (zero) start of line
* `^` - first non-blank character of line (same as `0w`, but don't forget about `I`!)
* `$` - end of line

#### More Advanced

* `Ctrl+d` - move down half a page
* `Ctrl+u` - move up half a page
* `(` `)` - go backward/forward by sentences
* `{` `}` - go backward/forward by paragraph (blank lines)
* `%` - go to start of innermost, or matching, `()` `[]` `{}`
* `[num]G`, `:[num][enter]` - go to line number `[num]`
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

* Single-character edits
    * `x` - Remove the character under the cursor
    * `X` - Remove the character to the left of the cursor
    * `s` - Remove the character under the cursor and enter insert mode
    * `S` - Remove the entire line at the cursor and enter insert mode (same as `cc`)
    * `r[char]` - replace a single character with the specified `[char]` (does not use Insert mode)
    * `R` - Enter REPLACE mode, which is like INSERT mode but with overtype

* Advanced
    * `J` - Join line below to the current one

### Entering Insert Mode

|        | lowercase | uppercase |
| -------|-----------|-----------|
| insert | `i` - insert in front of cursor | `I` - ... in front of the first non-whitespace in the line
| append | `a` - insert _after_ the cursor | `A` - ... at the _end_ of the line
| open   | `o` - insert to a new line _below_ the cursor | `O` - ... _above_ the cursor

### Multiple-character Edits

<table>
<thead>
<tr>
<th></th>
<th>Yank (y)</th>
<th>Delete (d)</th>
<th>Change (c)</th>
</tr>
</thead>
<tbody>
<tr>
<td>with <code>[noun]</code></td>
<td><code>y[noun]</code> - Yank <code>[noun]</code></td>
<td><code>d[noun]</code> - Delete <code>[noun]</code> </td>
<td><code>c[noun]</code> - Delete <code>[noun]</code> and enter insert mode<br /><ul><li>e.g. <code>ce</code> changes from the cursor to the end of the cursor word</li></ul></td>
</tr>
<tr>
<td>double</td>
<td><code>yy</code> - Yank entire line</td>
<td><code>dd</code> - Delete entire line</td>
<td><code>cc</code> - Delete the entire line and enter insert mode</td>
</tr>
<tr>
<td>uppercase</td>
<td><code>Y</code> - same as <code>yy</code></td>
<td><code>D</code> - Delete from the cursor to the end of the line</td>
<td><code>C</code> - Delete from the cursor to the end of the line and enter insert mode</td>
</tr>
</tbody>
</table>

A noun can be a motion, like `2W` (two whitespace-delimited words) or a text object like `i>` (inner tag).
See also [Text Objects](#objects).

### Undo/Repeat

* `u` - undo
* `Ctrl+r` - redo
* `.` - repeat last change

## Insert Mode

* `Ctrl+[` or `Esc` - exit insert mode
* `Ctrl+r[register]` - Insert at the cursor from the register indicated (no `"` prefix)

## Visual Mode

* `v` - starts visual mode
    * From here you can move around as in normal mode (`h`, `j`, `k`, `l` etc.) and can then do a command (such as `y`, `d`, or `c`)
* `V` - starts linewise visual mode
* `Ctrl+v` - start visual block mode
* `Esc` or `Ctrl+[` - exit visual mode
* Advanced
    * `O` - move to other corner of block
    * `o` - move to other end of marked area

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
* `"2, "3, ... "9` - Numbered history registers; shifted into from `"1` whenever it is written to
* `"a-"z, "A-"Z` - Named registers. Use uppercase variants to append.
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

## Search/Replace

* `/pattern` - search for pattern
* `?pattern` - search backward for pattern
* `n` - repeat search in same direction
* `N` - repeat search in opposite direction
* `:%s/old/new/g` - replace all old with new throughout file ([gn](http://vimcasts.org/episodes/operating-on-search-matches-using-gn/) is better though)
* `:%s/old/new/gc` - replace all old with new throughout file with confirmations

## Working with multiple files - Tabs

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

## Working with multiple files - Buffers

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

* `ma-mz` - Set mark `[a-z]` at cursor position
* A capital mark `[A-Z]` sets a global mark and will work between files
* `'a-'z` - move the cursor to the start of the line where the mark was set
* `''` - go back to the previous jump location

# Making Vim actually useful
Vim is quite unpleasant out of the box. For example, typing `:w` for every file save is awkward and copying and pasting to the system clipboard does not work. However, a few changes will get you much closer to the editor of your dreams.

## .vimrc
* [My .vimrc file](https://github.com/theicfire/dotfiles/blob/master/vim/.vimrc) has some pretty great ideas I haven't seen elsewhere.
* This is a minimal vimrc that focuses on three priorities:
    * adding options that are strictly better (like more information showing in autocomplete)
    * more convenient keystrokes (like  `[space]w` for write, instead of `:w [enter]`)
    * a similar workflow to normal text editors (like enabling the mouse)

### Installation
* Copy this to your home directory and restart Vim. Read through it to see what you can now do (like  `[space]w` to save a file)
    * Mac users - making a hidden normal file is suprisingly tricky. Here’s one way:
        * in the command line, go to the home directory
        * type `nano .vimrc`
        * paste in the contents of the .vimrc file
        * `ctrl+x`, `y`, `[enter]` to save
* You should now be able to press  `[space]w` in normal mode to save a file.
* `[space]p` should paste from the system clipboard (outside of Vim).
    * If you can’t paste, it’s probably because Vim was not built with the system clipboard option. To check, run `vim --version` and see if `+clipboard` exists. If it says `-clipboard`, you will not be able to copy from outside of Vim.
    * For Mac users, homebrew install Vim with the clipboard option. Install homebrew and then run `brew install vim`.
        * then move the old Vim binary: `$ mv /usr/bin/vim /usr/bin/vimold`
        * restart your terminal and you should see `vim --version` now with `+clipboard`

## Plugins
* The easiest way to make Vim more powerful is to use Vintageous in Sublime Text (version 3). This gives you Vim mode inside Sublime. I suggest this (or a similar setup with the Atom editor) if you aren't a Vim master. Check out [Advanced Vim](advanced.html) if you are.
* Vintageous is great, but I suggest you change a few settings to make it better.
    * Clone [this repository](https://github.com/theicfire/Vintageous) to `~/.config/sublime-text-3/Packages/Vintageous`, or similar. Then check out the "custom" branch.
        * Alternatively, you can get a more updated Vintageous version by cloning [the official](https://github.com/guillermooo/Vintageous) repository and then copying over [this patch](https://github.com/theicfire/Vintageous/commit/19ff6311b01e3ae259b7eb8e3944687b42ba06ff).
    * Change the user settings (`User/Preferences.sublime-settings`) to include:
        * `"caret_style": "solid"`
        * This will make the cursor not blink, like in Vim.
        * Sublime Text might freeze when you do this. It’s a bug; just restart Sublime Text after changing the file.
    * `ctrl+r` in Vim means "redo". But there is a handy Ctrl + R shortcut in Sublime Text that gives an "outline" of a file. I remapped it to alt+r by putting this in the User keymap
        * `{ "keys": ["alt+r"], "command": "show_overlay", "args": {"overlay": "goto", "text": "@"} },`
    * [Add the ability to toggle Vintageous on and off](https://github.com/guillermooo/Vintageous/wiki/Toggling-Vintageous)
    * Mac users: you will not have the ability to hold down a navigation key (like holding j to go down). To fix this, run the commands specified here: [https://gist.github.com/kconragan/2510186](https://gist.github.com/kconragan/2510186)

* Now you should be able to restart sublime and have a great Vim environment! Sweet Dude.

## Switch Caps Lock and Escape
* I highly recommend you switch the mapping of your caps lock and escape keys. You'll love it, promise! Switching the two keys is platform dependent; Google should get you the answer.

## Other
I don’t personally use these yet, but I’ve heard other people do!

* `:wqa` - Write and quit all open tabs (thanks Brian Zick)

## Other resources

* Clickable [Vim mode state diagram](https://gist.github.com/darcyparker/1886716/raw/c1ee7657010278a787c6502b796a6766a40d56aa/vimModeStateDiagram.svg)
* [Vim Text Objects: The Definitive Guide](https://blog.carbonfive.com/vim-text-objects-the-definitive-guide/)
* [Vim as a Language](https://benmccormick.org/2014/07/02/learning-vim-in-2014-vim-as-language)
* [Vim Registers](https://www.brianstorti.com/vim-registers/)
* [Mastering the Vim Language](https://www.youtube.com/watch?v=wlR5gYd6um0)
