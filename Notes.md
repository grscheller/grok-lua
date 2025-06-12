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

#### Installing a Lua environment (Redone 2025-06-11)

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

#### Blurry future directions

The possible directions I may want to follow are

- Neovim plugin development
- Lua command-line tools
- Luau Roblox game development

##### Neovim plugin development

There is a GitHub group named nvim-neorocks. Their stated aims are
to encourage good software practices in the neovim plugin ecosystem.
The group is working on a plugin manager called `rocks.nvim` which
is cargo-like and leverages LuaRocks. Also, `lazy-nvim` has started
supporting the LuaRocks Rockspec file format, see the lazy.nvim
[What's new][4] page.

##### Lua commandline tools

At this point I wish to keep this ecosystem separate from the one
I use to write Neovim plugins.

##### Roblox

My daughters keep asking me to help them with luau scripting with their
Roadblocks games. This will give me an opportunity to work with another
Lua fork other than LuaJIT.

### 2025-06-11:

#### Observations

Observations of yesterday installation:

- what was installed was luarocks 2.3.0
- latest luarocks is 3.12.0
- luarocks IS NOT COMPATIBLE with luau
- luajit is more or less compatible with lua 5.1
  - there is something called the [luajit Manifest][5]
  - lists all the lua modules compatible with luajit
  - to install one `$ luarocks install --server=https://luarocks.org/m/luajit <name>`
- the luarocks executable is a shebank script
  - it points to the luajit installed with it
  - it looks to be configured for the specific luajit installed with it

This is not what I want. Ripping out what I installed yesterday and
starting over.

#### Get LuaRocks installed

First, I will install the default LuaJIT that comes with PopOS. Which is
`luajit/noble 2.1.0+git20231223.c525bcb+dfsg-1 amd64`.

```
    $ cd ~/devel
    $ rm -r luajit_rocks/
    $ sudo apt install luajit
    $ luajit -v
    LuaJIT 2.1.1703358377 -- Copyright (C) 2005-2023 Mike Pall. https://luajit.org/
    $ nvim --version | grep LuaJIT
    LuaJIT 2.1.1703358377
```

We have a winner! Also, LuaJIT 3.12.0 was just released last week.

Now lets build luarocks. I will follow the installation instructions for
Ubuntu from the [luarocks github repo][6]. The instructions are under
`docs/installation_instructions_for_unix.md`.

Downloaded `luarocks-3.12.0.tar.gz` from the LuaRocks [release page][6].

Now lets build and install it.

```fish
    $ sudo apt install build-essential libreadline-dev unzip
    $ cd ~/build
    $ mkdir luarocks
    $ cd luarocks
    $ mv ~/luarocks-3.12.0.tar.gz .
    $ ax luarocks-3.12.0.tar.gz
    $ cd luarocks-3.12.0
    $ ./configure --prefix=/home/grs/devel/luarocks

    Configuring LuaRocks version 3.12.0...

    Lua version detected: 5.1
    Lua interpreter found: /usr/bin/luajit
    lua.h for Lua 5.1 not found (tried /usr/include/lua/5.1/lua.h /usr/include/lua5.1/lua.h /usr/include/lua-5.1/lua.h /usr/include/lua51/lua.h /usr/include/lua.h /usr/include/luajit-2.1/lua.h)

    If the development files for Lua (headers and libraries)
    are installed in your system, you may need to use the
    --with-lua or --with-lua-include flags to specify their location.

    If those files are not yet installed, you need to install
    them using the appropriate method for your operating system.

    Run ./configure --help for details on flags.

    configure failed.
```

As expected, the first command installed nothing new. I think
configuration failed because it needs a development version?

```
    $ apt list '*' | grep -i luajit

    WARNING: apt does not have a stable CLI interface. U1se with caution in scripts.

    libluajit-5.1-2/noble,now 2.1.0+git20231223.c525bcb+dfsg-1 amd64 [installed,automatic]
    libluajit-5.1-common/noble,noble,now 2.1.0+git20231223.c525bcb+dfsg-1 all [installed,automatic]
    libluajit-5.1-dev/noble 2.1.0+git20231223.c525bcb+dfsg-1 amd64
    libluajit2-5.1-2/noble 2.1-20230410-1build1 amd64
    libluajit2-5.1-common/noble,noble 2.1-20230410-1build1 all
    libluajit2-5.1-dev/noble 2.1-20230410-1build1 amd64
    libtexluajit-dev/noble 2023.20230311.66589-9build3 amd64
    libtexluajit-dev/noble 2023.20230311.66589-9build3 i386
    libtexluajit2/noble 2023.20230311.66589-9build3 amd64
    libtexluajit2/noble 2023.20230311.66589-9build3 i386
    luajit2/noble 2.1-20230410-1build1 amd64
    luajit/noble,now 2.1.0+git20231223.c525bcb+dfsg-1 amd64 [installed]
    uwsgi-plugin-luajit/noble 2.0.22+4+0.0.8build2 amd64

    $ sudo apt install libluajit-5.1-dev
    $ cd ..
    $ rm -r luarocks-3.12.0
    $ ax luarocks-3.12.0.tar.gz
    $ cd luarocks-3.12.0
    $ ./configure --prefix=/home/grs/devel/luarocks --with-lua-include=/usr/include/luajit-2.1

    Configuring LuaRocks version 3.12.0...

    Lua version detected: 5.1
    Lua interpreter found: /usr/bin/luajit
    lua.h found: /usr/include/luajit-2.1/lua.h
    unzip found in PATH: /usr/bin

    Done configuring.

    LuaRocks will be installed at......: /home/grs/devel/luarocks
    LuaRocks will install rocks at.....: /home/grs/devel/luarocks
    LuaRocks configuration directory...: /home/grs/devel/luarocks/etc/luarocks
    Using Lua from.....................: /usr
    Lua include directory..............: /usr/include/luajit-2.1

    * Type make and make install:
      to install to /home/grs/devel/luarocks as usual.
    * Type make bootstrap:
      to install LuaRocks into /home/grs/devel/luarocks as a rock.
    $ make
    $ make install
```

The installed luajit works.

```
    $ cd projects/01-hello-world/
    (grs) [grs@godel2: ~/devel/grok/grok-lua/projects/01-hello-world] (main  =)
    $ luajit hw.lua
    A: 	Hello, World!
    B: 	42
```

Lets try it with Neovim with the luarocks executable on my PATH,

```vim
    : checkhealth lazy
    lazy:                                           require("lazy.health").check()

    lazy.nvim ~
    - {lazy.nvim} version `11.17.1`
    - ✅ OK {git} `version 2.43.0`
    - ✅ OK no existing packages found by other package managers
    - ✅ OK packer_compiled.lua not found

    luarocks ~
    - checking `hererocks` installation
    - ✅ OK no plugins require `luarocks`, so you can ignore any warnings below
    - ✅ OK {python3} `Python 3.13.3`
    - ❌ ERROR {/home/grs/.local/share/nvim/lazy-rocks/hererocks/bin/luarocks} not installed
    - ⚠️ WARNING {/home/grs/.local/share/nvim/lazy-rocks/hererocks/bin/lua} version `5.1` not installed
    - ⚠️ WARNING Lazy won't be able to install plugins that require `luarocks`.
      Here's what you can do:
       - fix your `luarocks` installation
       - disable *hererocks* with `opts.rocks.hererocks = false`
       - disable `luarocks` support completely with `opts.rocks.enabled = false`
```

Looks like it is looking for luarocks in
`~/.local/share/nvim/lazy-rocks/hererocks/bin`. So lets install another
instance there. The name [hererocks][7] is associated with a Python
script that installs multiple versions of Lua, LuaJIT, and LuaRocks.

```fish
    $ ./configure --prefix=/home/grs/.local/share/nvim/lazy-rocks/hererocks --with-lua-include=/usr/include/luajit-2.1

    Configuring LuaRocks version 3.12.0...

    Lua version detected: 5.1
    Lua interpreter found: /usr/bin/luajit
    lua.h found: /usr/include/luajit-2.1/lua.h
    unzip found in PATH: /usr/bin

    Done configuring.

    LuaRocks will be installed at......: /home/grs/.local/share/nvim/lazy-rocks/hererocks
    LuaRocks will install rocks at.....: /home/grs/.local/share/nvim/lazy-rocks/hererocks
    LuaRocks configuration directory...: /home/grs/.local/share/nvim/lazy-rocks/hererocks/etc/luarocks
    Using Lua from.....................: /usr
    Lua include directory..............: /usr/include/luajit-2.1

    * Type make and make install:
      to install to /home/grs/.local/share/nvim/lazy-rocks/hererocks as usual.
    * Type make bootstrap:
      to install LuaRocks into /home/grs/.local/share/nvim/lazy-rocks/hererocks as a rock.

    $ make
    $ make install
```

Let's check to see if nvim is happy.

```vim
    : checkhealth lazy

    lazy:                                           require("lazy.health").check()

    lazy.nvim ~
    - {lazy.nvim} version `11.17.1`
    - ✅ OK {git} `version 2.43.0`
    - ✅ OK no existing packages found by other package managers
    - ✅ OK packer_compiled.lua not found

    luarocks ~
    - checking `hererocks` installation
    - ✅ OK no plugins require `luarocks`, so you can ignore any warnings below
    - ✅ OK {python3} `Python 3.13.3`
    - ✅ OK {/home/grs/.local/share/nvim/lazy-rocks/hererocks/bin/luarocks} `3.12.0`
    - ⚠️ WARNING {/home/grs/.local/share/nvim/lazy-rocks/hererocks/bin/lua} version `5.1` not installed
```

I don't like that last warning.

##### Duck.ai says

LuaRocks can work with the built-in LuaJIT that comes with Neovim, but
it typically requires an external Lua installation (like Lua 5.1) for
managing and installing packages. Here’s a breakdown:

Neovim's LuaJIT: Neovim uses LuaJIT, which is compatible with Lua 5.1.
This means that Lua scripts written for Lua 5.1 should generally work in
Neovim without modification. LuaRocks Installation: When you install
LuaRocks, it usually expects a standalone Lua installation (like Lua5.1)
to function properly. This is because LuaRocks manages packages and
dependencies that are often installed in a global Lua environment. Using
LuaRocks with Neovim: If you want to use LuaRocks with Neovim's LuaJIT,
you can configure LuaRocks to point to the LuaJIT binary. This can be
done by setting the LUA environment variable to the path of Neovim's
LuaJIT when installing LuaRocks or by configuring LuaRocks to use the
LuaJIT paths. Compatibility: While you can use LuaRocks with Neovim's
LuaJIT, some packages may have dependencies that expect a standard Lua
installation. In such cases, you might need to ensure that the packages
are compatible with LuaJIT. In summary, while LuaRocks can be configured
to work with Neovim's built-in LuaJIT, it typically requires an external
Lua installation for managing packages effectively.

### 2025-06-11:

What Duck.ai says above is not quite right. One cannot "easily" use the
builtin LuaJIT in this way. But, it is correct that Lua 5.1 is what
LuaRocks is expecting to be driven with.

Yesterday work done on my godel2 workstation computer. Today working on
my noether2 personal laptop, both PopOS Cosmic Alpha.

#### Install Lua 5.1.5 and LuaRocks

Installed hererocks 0.25.1 from PyPI which is maintained by the luarocks
group, Used it to install Lua 5.1.5 with a patch. When I installed
luarocks with it, it istalled version 3.8.0. According to Duck.ai,
Neovim needs LuaRocks >=3.11.1 but <4.0.0. I could not find a source for
this. So I built it from source leaveraging the Lua 5.1.5 I just
installed with heredocs.

```
    $ hererocks --lua 5.1.5 --patch ~/.local/share/nvim/lazy-rocks/hererocks
    Fetching Lua 5.1.5 (cached)
    Verifying SHA256 checksum
    Building Lua 5.1.5
    Patch for "When loading a file, Lua may call the reader function again after it returned end of input": OK
    Applied 1 patch (1 available for this version)
    Installing Lua 5.1.5
    Done
```

```
    $ sudo apt install build-essential libreadline-dev unzip

    $ apt list '*' | grep -i '^liblua5.1.0-dev'
    liblua5.1-0-dev/noble 5.1.5-9build2 amd64
    liblua5.1-0-dev/noble 5.1.5-9build2 i386

    $ sudo apt install liblua5.1-0-dev
    $ cd ~/build/luarocks-3.12.0/

    $ ./configure --prefix=/home/grs/.local/share/nvim/lazy-rocks/hererocks --with-lua-include=/usr/include/lua5.1

    Configuring LuaRocks version 3.12.0...

    Lua version detected: 5.1
    Lua interpreter found: /home/grs/.local/share/nvim/lazy-rocks/hererocks/bin/lua
    lua.h found: /usr/include/lua5.1/lua.h
    unzip found in PATH: /usr/bin

    Done configuring.

    LuaRocks will be installed at......: /home/grs/.local/share/nvim/lazy-rocks/hererocks
    LuaRocks will install rocks at.....: /home/grs/.local/share/nvim/lazy-rocks/hererocks
    LuaRocks configuration directory...: /home/grs/.local/share/nvim/lazy-rocks/hererocks/etc/luarocks
    Using Lua from.....................: /home/grs/.local/share/nvim/lazy-rocks/hererocks
    Lua include directory..............: /usr/include/lua5.1

    $ make
    $ make install

    $ lua -v
    Lua 5.1.5  Copyright (C) 1994-2012 Lua.org, PUC-Rio

    $ luarocks --version
    /home/grs/.local/share/nvim/lazy-rocks/hererocks/bin/luarocks 3.12.0
    LuaRocks main command-line interface
```

The Lua 5.1.5 install also installed activation scripts similar to
pyenv but for Lua "virtual environments."

```vim
    : checkhealth lazy

    lazy:                                           require("lazy.health").check()

    - {lazy.nvim} version `11.17.1`
    - ✅ OK {git} `version 2.43.0`
    - ✅ OK no existing packages found by other package managers
    - ✅ OK packer_compiled.lua not found

    luarocks ~
    - checking `luarocks` installation
    - ✅ OK no plugins require `luarocks`, so you can ignore any warnings below
    - ✅ OK {luarocks} `/home/grs/.local/share/nvim/lazy-rocks/hererocks/bin/luarocks 3.12.0`
    - ✅ OK {lua} `Lua 5.1.5  Copyright (C) 1994-2012 Lua.org, PUC-Rio`
```

Now lazy.nvim is happy.

---

[1]: https://github.com/grscheller/grok-lua/blob/main/projects/01-hello-world/hw.lua
[2]: https://github.com/torch/luajit-rocks
[3]: https://luarocks.org/
[4]: https://lazy.folke.io/news#11x
[5]: https://luarocks.org/m/luajit
[6]: https://luarocks.github.io/luarocks/releases/
[7]: https://github.com/mpeterv/hererocks
