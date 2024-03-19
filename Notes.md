# Grokking Lua Notes

Chronological notes on my journey to master Lua.

## Notes

I don't intend to retroactively edit these notes useless they are
either misleadingly confusing, factually inaccurate, or to edit out
typos.

### 2024-03-18: Get a hello world program working

Here are my available tools I have so far on Arch Linux.

```fish
    $ lua -v
    Lua 5.4.6  Copyright (C) 1994-2023 Lua.org, PUC-Rio

    $ nvim --version
    NVIM v0.9.5
    Build type: Release
    LuaJIT 2.1.1702233742

      system vimrc file: "$VIM/sysinit.vim"
      fall-back for $VIM: "/usr/share/nvim"

    Run :checkhealth for more info
```

Both can act as Lua interpreters, here is how to execute a Lua script
from the commandline.

```fish
    $ lua hw.lua
    A: 	Hello, World!
    B: 	42

    $ nvim -l hw.lua
    A:  Hello, World!
    B:  42 
```

See [hw.lua][1] from my firsty Lua project for the source code.

---

[1]: https://github.com/grscheller/grok-lua/blob/main/projects/01-hello-world/hw.lua
