---
published: true
layout: post
title: Immutable
---

I was thinking about the fact that when I code I sometimes cut corners to save
time, but those compromises end up biting me back later on. Problems like bad
variable names and poor code structure become harder to fix as a project gets
larger. I thought that this problem could be from coding too fast. I wanted to
try an experiment to slow down my coding, and force me to think about what I'm
writing. So, I thought it'd be fun to try to write with an immutable editor.

I wrote a plugin for the [Atom](https://atom.io) editor that disables the backspace and delete keys.
This forces me to think about what I'm writing, since things can't be changed.
Admittedly, it's a stupid idea. A really stupid idea.
 It's amazing how much I use backspace, and how much I
miss it when it's gone. But, hey, it's just an experiment. Actually, the experiment
was such a failure that I had to make a keybinding to toggle back on mutability.
It's just too hard to type without making mistakes. But, I think it's a fun idea,
in a kind of masochistic way.

If you want to try it too, you can download the plugin for Atom, it's just called
[Immutable](https://atom.io/packages/immutable). You use Ctrl-Backspace to toggle
mutability.
