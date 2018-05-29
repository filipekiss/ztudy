# Ztudy

## Zsh Parameter Expansion

[Official Docs]

---

#### Some parameter expansion black magic

I've changed my ZSH setup early this year to reduce the amount of symbolic links
I did from my [dotfiles] repository to their correct place.

ZSH has a configuration variable called `ZDOTDIR`, which basically tells where
`zsh` will look for configuration files (such as `.zshrc`).

If you want to use a folder that's not you `$HOME` folder, you just need to have
a `.zshenv` file in your home folder and set `ZDOTDIR` to point to where your
`.zshrc` and other files are currently stored.

What I did was to symlink my [`.zshenv`] to my home folder. In my `.zshenv` I
have this piece of code:

```zsh
export ZDOTDIR="${${(%):-%N}:A:h}"
```

Them, I _symlink_ `.zshenv` to `$HOME/.zshenv`. When `zsh` runs, it will
correctly interpret the `ZDOTDIR` value as being the folder where `.zshenv` is
located, resolving symbolic links and all! But how does that work. Here's a
graph explaining it (read from the bottom):

```text
${${(%):-%N}:A:h}
     │   │   │ │
     │   │   │ └── :h - Remove the trailing part from path, so /path/to/file becomes /path/to
     │   │   └──── :A - Resolves symlink to absolute path, so /some/symlink/to/file becomes /path/to/file
     │   └──────── %N - This is a prompt expansion: This will show the last invoked or sourced file or function. Almost like $0 in bash.
     └──────────── (%) - This is a parameter expansion flag. It allows us to expand the %N in the next step
```

So that `ZDOTDIR` assignment is nothing more that telling `zsh`: \_"Hey, find
out where this file is, resolve the symlink, remove the file name and you'll
have the path to the folder I want"

[official docs]: http://zsh.sourceforge.net/Doc/Release/Expansion.html#Parameter-Expansion
