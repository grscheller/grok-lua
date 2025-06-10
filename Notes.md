# Grokking Lua Notes

Chronological notes on my journey to master Lua.

## Notes

I don't intend to retroactively edit these notes useless they are either
misleadingly confusing or factually inaccurate. Linked notes are subject
to change at any time.

### Pinned links

Pinned information will go here.

### 2024-03-18:

#### Getting a hello world program working

I intend to start by getting a variant of the first example program
from `:help lua-intro` to work.

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

See [hw.lua][1] from my first Lua project for the source code.

### 2025-06-10:

#### Installing a Lua environment

Moved from Arch Linux to Pop!_OS 24.04 LTS x86_64. I am using a late
COSMIC desktop Alpha release. I don't trust what the underlying Ubuntu
reposes will give me, so I am using [Torch luajit-rocks][2] repo on
GitHub to build both LuaJIT 2.1 and [LuaRocks][3] for me. Luarocks is
a Lua Package manager for Lua modules.

```fish
    $ sudo apt install cmake
    $ cd ~/build
    $ git clone git@github.com:torch/luajit-rocks
    $ cd luajit-rock
    $ mkdir build
    $ cmake .. -DCMAKE_INSTALL_PREFIX=$HOME/devel/luajit_rocks -DWITH_LUAJIT21=ON
    $ make install
```

Lets see what got installed.

```fish
    $ cd ~/devel/luajit_rocks/

    $ ls
    bin  etc  include  lib  share

    $ ls -l bin/
    total 804
    -rwxr-xr-x 1 grs grs 813672 Jun 10 14:41 luajit
    -rwxr-xr-x 1 grs grs   1046 Jun 10 14:38 luarocks
    -rwxr-xr-x 1 grs grs    642 Jun 10 14:38 luarocks-admin
    $ ./bin/luajit -v
    LuaJIT 2.1.0-beta2 -- Copyright (C) 2005-2017 Mike Pall. http://luajit.org/
    
     _____              _
    |_   _|            | |
      | | ___  _ __ ___| |__
      | |/ _ \| '__/ __| '_ \
      | | (_) | | | (__| | | |
      \_/\___/|_|  \___|_| |_|

    $ ./bin/luarocks --version
    ./bin/luarocks 2.3.0
    LuaRocks main command-line interface
```

Running just `./bin/luarocks` gives a fairly complete man page.

Lets see what Neovim is currently using,

```fish
    $ nvim --version
    NVIM v0.12.0-dev
    Build type: RelWithDebInfo
    LuaJIT 2.1.1703358377
    Run "nvim -V1 -v" for more info
```

Still TODO:

- configure nvim and fish for this installation location
- test what was done before back in 2024

---

[1]: https://github.com/grscheller/grok-lua/blob/main/projects/01-hello-world/hw.lua
[2]: https://github.com/torch/luajit-rocks
[3]: https://luarocks.org/
