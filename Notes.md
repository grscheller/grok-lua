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

#### Installing a Lua environment (Redone 2025-06-12)

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

```fish
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
    :checkhealth lazy
    lazy:                                           require("lazy.health").check()
    ==============================================================================
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
    :checkhealth lazy
    ==============================================================================
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

What Duck.ai says above is not quite right. One cannot "easily" use the
builtin LuaJIT in this way. But, it is correct about Lua 5.1 being what
LuaRocks is expecting to be driven with.

### 2025-06-11:

Yesterday work done on my godel2 workstation computer. Today working on
my noether2 personal laptop, both PopOS Cosmic Alpha.

#### Install Lua 5.1.5 and LuaRocks (on noether2)

`<clip>`

### 2025-06-12:

#### Install Lua 5.1.5 and LuaRocks (repeat on hamilton4)

While repeating noether2 install, I discovered some errors. Morphing
yesterday's write up to what I did today on hamilton4.

Installed hererocks 0.25.1 from PyPI which is maintained by the luarocks
group, Used it to install Lua 5.1.5 with a patch. This version, not the
native LuaJIT version, is needed to install and drive LuaRocks.

```fish
    $ hererocks --lua 5.1.5 --patch ~/.local/share/nvim/lazy-rocks/hererocks
    Fetching Lua 5.1.5 (cached)
    Verifying SHA256 checksum
    Building Lua 5.1.5
    Patch for "When loading a file, Lua may call the reader function again after it returned end of input": OK
    Applied 1 patch (1 available for this version)
    Installing Lua 5.1.5
    Done
```

Unfortunately, hererocks installs version 3.8.0 of LuaRocks. According
to Duck.ai (I could not find a source for this) Neovim needs LuaRocks
`>=3.11.1` but `~<4.0.0`.  So I built it from source obtained from
[luarocks.org][3].

```fish
    $ sudo apt install build-essential libreadline-dev unzip

    $ apt list '*' | grep -i '^liblua5.1.0-dev'
    liblua5.1-0-dev/noble 5.1.5-9build2 amd64
    liblua5.1-0-dev/noble 5.1.5-9build2 i386

    $ sudo apt install liblua5.1-0-dev
    $ cd ~/build/luarocks-3.12.0/

    $ ./configure --prefix=$HOME/.local/share/nvim/lazy-rocks/hererocks --with-lua-include=$HOME/.local/share/nvim/lazy-rocks/hererocks/include

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
    Lua include directory..............: /home/grs/.local/share/nvim/lazy-rocks/hererocks/include

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
    :checkhealth lazy
    ==============================================================================
    lazy:                                           require("lazy.health").check()

    lazy.nvim ~
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

For now, I configured my environment to find this install of Lua. Will
need to revisit this once I start Lua coding outside of my Neovim
configurations.

### 2025-06-12:

#### Fix Lua 5.1.5 and LuaRocks 3.12.0 installation on godel2

Undo the install I did earlier this week.

```fish
$ digpath luajit lua
/usr/bin/luajit

$ apt list '*' | grep -i luajit | grep installed

$ dfInstall --check
nvimInstall: Lua 5.1 is not installed in /home/grs/.local/share/nvim/lazy-rocks/hererocks/bin

$ rm -r ~/.local/share/nvim/lazy-rocks/hererocks/
$ dfInstall --check
nvimInstall: Lua 5.1 is not installed in /home/grs/.local/share/nvim/lazy-rocks/hererocks/bin
nvimInstall: LuaRocks is not installed in /home/grs/.local/share/nvim/lazy-rocks/hererocks/bin

$ ve -c -r
$ hererocks --lua 5.1.5 --patch ~/.local/share/nvim/lazy-rocks/hererocks

$ hererocks --lua 5.1.5 --patch ~/.local/share/nvim/lazy-rocks/hererocks
Fetching Lua 5.1.5 from https://www.lua.org/ftp/lua-5.1.5.tar.gz
Verifying SHA256 checksum
Building Lua 5.1.5
Patch for "When loading a file, Lua may call the reader function again after it returned end of input": OK
Applied 1 patch (1 available for this version)
Installing Lua 5.1.5
Done.

$ $DOTFILES_GIT_REPO/bin/dfInstall --check
nvimInstall: LuaRocks is not installed in /home/grs/.local/share/nvim/lazy-rocks/hererocks/bin

$ cd ~/build/luarocks/
$ ls
luarocks-3.12.0  luarocks-3.12.0.tar.gz

$ cd luarocks-3.12.0/
$ gmake clean

$ cd luarocks-3.12.0/
$ ./configure --prefix=$HOME/.local/share/nvim/lazy-rocks/hererocks --with-lua-include=$HOME/.local/share/nvim/lazy-rocks/hererocks/include

Configuring LuaRocks version 3.12.0...

Lua version detected: 5.1
Lua interpreter found: /usr/bin/luajit
lua.h found: /home/grs/.local/share/nvim/lazy-rocks/hererocks/include/lua.h
unzip found in PATH: /usr/bin

Done configuring
```

Oppps... Seems I managed to install lots of Lua stuff. Getting rid of it
is somewhat messy.

Factoid:

```fish
    $ apt-cache rdepends --installed libluajit2-5.1-common
    libluajit2-5.1-common
    Reverse Depends:
      libluajit2-5.1-2

    $ apt-cache rdepends --installed libluajit2-5.1-2
    libluajit2-5.1-2
    Reverse Depends:
      neovim
      neovim
```

Seems Neovim is requiring the "other" maintained library

```fish
$ apt search libluajit-5.1.2
Sorting... Done
Full Text Search... Done
libluajit-5.1-2/noble 2.1.0+git20231223.c525bcb+dfsg-1 amd64
  Just in time compiler for Lua - library version

$ apt search libluajit2-5.1.2
Sorting... Done
Full Text Search... Done
libluajit2-5.1-2/noble,now 2.1-20230410-1build1 amd64 [installed,automatic]
  OpenResty-maintained branch of LuaJIT (shared objects)
```

Oh, one is a static library, the other is a shared library. This has
nothing to do with the Lua 5.1 version needed for LuaRocks. It all about
the internal LuaJIT baked into Neovim.

TODO: Since I have to build LuaRocks anyway, might as well also build
Lua 5.1.5 directly. Maybe automate it?

### 2025-06-11:

#### Install Lua 5.1.5 and LuaRocks without hererocks

Down loaded Lua 5.1.5 from [Lua website][8] to the parent directory
where I previously built luarocks.

```fish
    $ ax lua-5.1.5.tar.gz
    $ cd lua-5.1.5/

    $ make
    Please do
       make PLATFORM
    where PLATFORM is one of these:
       aix ansi bsd freebsd generic linux macosx mingw posix solaris
    See INSTALL for complete instructions.
```
INSTALL file not clear how to set install directory. Reading Makefile.

Editing Makefile, replace `INSTALL_TOP= /usr/local` with
`INSTALL_TOP= /home/grs/.local/share/nvim/lazy-rocks/hererocks`.

```fish
    $ rm -rf ~/.local/share/nvim/lazy-rocks/hererocks/*
    $ make linux
    $ make install
    cd src && mkdir -p /home/grs/.local/share/nvim/lazy-rocks/hererocks/bin /home/grs/.local/share/nvim/lazy-rocks/hererocks/include /home/grs/.local/share/nvim/lazy-rocks/hererocks/lib /home/grs/.local/share/nvim/lazy-rocks/hererocks/man/man1 /home/grs/.local/share/nvim/lazy-rocks/hererocks/share/lua/5.1 /home/grs/.local/share/nvim/lazy-rocks/hererocks/lib/lua/5.1
    cd src && install -p -m 0755 lua luac /home/grs/.local/share/nvim/lazy-rocks/hererocks/bin
    cd src && install -p -m 0644 lua.h luaconf.h lualib.h lauxlib.h ../etc/lua.hpp /home/grs/.local/share/nvim/lazy-rocks/hererocks/include
    cd src && install -p -m 0644 liblua.a /home/grs/.local/share/nvim/lazy-rocks/hererocks/lib
    cd doc && install -p -m 0644 lua.1 luac.1 /home/grs/.local/share/nvim/lazy-rocks/hererocks/man/man1
```

Now check the install of Lua.

```fish
    $ digpath lua
    /home/grs/.local/share/nvim/lazy-rocks/hererocks/bin/lua

    $ lua -v
    Lua 5.1.5  Copyright (C) 1994-2012 Lua.org, PUC-Rio

    $ ldd /home/grs/.local/share/nvim/lazy-rocks/hererocks/bin/lua

            linux-vdso.so.1 (0x00007b4fd47f8000)
            libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007b4fd46c0000)
            libreadline.so.8 => /lib/x86_64-linux-gnu/libreadline.so.8 (0x00007b4fd466b000)
            libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007b4fd4400000)
            /lib64/ld-linux-x86-64.so.2 (0x00007b4fd47fa000)
            libtinfo.so.6 => /lib/x86_64-linux-gnu/libtinfo.so.6 (0x00007b4fd4637000)
```

Now lets build LuaRocks.

```fish
    $ cd ..
    $ ax luarocks-3.12.0.tar.gz
    $ cd luarocks-3.12.0/

    $ ./configure --prefix=$HOME/.local/share/nvim/lazy-rocks/hererocks --with-lua-include=$HOME/.local/share/nvim/lazy-rocks/hererocks/include

    Configuring LuaRocks version 3.12.0...

    Lua version detected: 5.1
    Lua interpreter found: /home/grs/.local/share/nvim/lazy-rocks/hererocks/bin/lua
    lua.h found: /home/grs/.local/share/nvim/lazy-rocks/hererocks/include/lua.h
    unzip found in PATH: /usr/bin

    Done configuring.

    LuaRocks will be installed at......: /home/grs/.local/share/nvim/lazy-rocks/hererocks
    LuaRocks will install rocks at.....: /home/grs/.local/share/nvim/lazy-rocks/hererocks
    LuaRocks configuration directory...: /home/grs/.local/share/nvim/lazy-rocks/hererocks/etc/luarocks
    Using Lua from.....................: /home/grs/.local/share/nvim/lazy-rocks/hererocks
    Lua include directory..............: /home/grs/.local/share/nvim/lazy-rocks/hererocks/include

    $ make
    $make install
```

Now check the install of LuaRocks.

```fish
    $ digpath luarocks
    /home/grs/.local/share/nvim/lazy-rocks/hererocks/bin/luarocks

    $ luarocks --version
    /home/grs/.local/share/nvim/lazy-rocks/hererocks/bin/luarocks 3.12.0
    LuaRocks main command-line interface
```

Eventually I want to use luarocks outside of nvim and want to keep them
separate. Editing
`~/.local/share/nvim/lazy-rocks/hererocks/etc/luarocks/config-5.1.lua`.

```lua
    -- LuaRocks configuration

    rocks_trees = {
       { name = "user", root = "/home/grs/.local/share/grs/nvim/.luarocks" };
       { name = "system", root = "/home/grs/.local/share/nvim/lazy-rocks/hererocks" };
    }
    variables = {
       LUA_DIR = "/home/grs/.local/share/nvim/lazy-rocks/hererocks";
       LUA_INCDIR = "/home/grs/.local/share/nvim/lazy-rocks/hererocks/include";
       LUA_BINDIR = "/home/grs/.local/share/nvim/lazy-rocks/hererocks/bin";
       LUA_VERSION = "5.1";
       LUA = "/home/grs/.local/share/nvim/lazy-rocks/hererocks/bin/lua";
    }
```

Now let's see if lazy.nvim is happy with the install.

```vim
    :checkhealth lazy
    ==============================================================================
    lazy:                                           require("lazy.health").check()

    lazy.nvim ~
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

Success!

So, what is `luarocks-admin` used for?

It is used to administer a LuaRocks repository, not to be confused with
a rock-tree.

A repository is the source of the rocks, while the rock-tree is the
destination where they are installed. LuaRocks supports both local and
remote repositories. The most commonly used one is the public LuaRocks
repository on luarocks.org.

When LuaRocks is used to install a rock, it downloads it from
a specified repository (or repositories) and places it in a chosen
rock-tree. The rock-tree then allows Lua's loader to find and load
installed modules when needed. In summary, a LuaRocks repository is
where you get rocks from, and a LuaRocks rock-tree is where you put
rocks when you install them.

### 2025-06-15:

#### Decided to try and install luarocks again with luarocks-nvim

I removed my lua and luarocks installs. factuallylly removed the entire
`~/.local/share/nvim/lazy-rocks subdirectory.

```vim
    :checkhealth luarocks-nvim
    ==============================================================================
    luarocks-nvim:                         require("luarocks-nvim.health").check()

    luarocks.nvim ~
    - luarocks system is not prepared
    - ✅ OK git is installed
    - ❌ ERROR lua is not installed! If you're on unix, please install it using your package manager. For windows use https://github.com/rjpcomputing/luaforwindows.
    - ✅ OK make is installed
```

From the README.md file on `vhyrro/luarocks.nvim`, the 5.1 or JIT
versions of Lua need to be installed. Lets go with LuaJIT.

```fish
    $ apt search '^luajit'
    Sorting... Done
    Full Text Search... Done
    luajit/noble 2.1.0+git20231223.c525bcb+dfsg-1 amd64
      Just in time compiler for Lua programming language version 5.1

    luajit2/noble 2.1-20230410-1build1 amd64
      OpenResty-maintained branch of LuaJIT (interpreter)
```

Seems to be two flavors. What the heck is OpenRusty?
OpenResty® is a dynamic web platform based on NGINX and LuaJIT. It has
a 2-clause BSD license. For now, lets stick with vanilla luajit.

```fish
    $ sudo apt install luajit
    $ digpath luajit
    /usr/bin/luajit
```

Didn't work. from the help docs,

```vim
    :help luajit.nvim:

    **On unix systems**, this is as simple as using your system package manager (`brew`, `pacman`, `apt` etc.).
    Just make sure that you're installing the 5.1 or JIT version of lua!
    Here's a list of known package names for various package managers (choose the one your distro uses):
    - brew (MacOS): `brew install luajit`
    - apt: `sudo apt install liblua5.1-0-dev`
    - dnf: `sudo dnf install compat-lua-devel-5.1.5`
    - pacman: `sudo pacman -Syu lua51` or `sudo pacman -Syu luajit`

```

Installing `liblua5.1-0-dev` did not work. When I uninstalled it and
installed `apt install lua5.1` and `lua5.1-doc` I get

```vim
    :checkhealth luarocks-nvim
    ==============================================================================
    luarocks-nvim:                         require("luarocks-nvim.health").check()

    luarocks.nvim ~
    - luarocks system is not prepared
    - ✅ OK git is installed
    - ✅ OK lua is installed
    - ✅ OK make is installed
```

I reinstalled `liblua5.1-dev` but it did not help.

So, how do I "prepare" it?

```vim
    :Lazy build luarocks.nvim
    :checkhealth luarocks-nvim
    ==============================================================================
    luarocks-nvim:                         require("luarocks-nvim.health").check()$ fd 'luarocks\.lua'
share/nvim/lazy/mason.nvim/lua/mason-core/installer/compiler/compilers/luarocks.lua
share/nvim/lazy/mason.nvim/lua/mason-core/installer/managers/luarocks.lua

    luarocks.nvim ~
    - ✅ OK luarocks is installed, system is prepared to install rocks!
```

When I tried to down load 3 of my plugins from LuaRocks, it failed.
Seems that lazy.nvim tried downloading from
`https://nvim-neorocks.github.io/rocks-binaries/` instead of the
LuaRocks repo. That is the repository used by the nvim-neorocks/lux
package manager.

- luarocks
  - The original Lua package manager
- rocks.nvim
  - A Neovim plugin manager that uses luarocks under the hood,
    - will be rewritten soon to use Lux instead.

Seems that lazy.nvim is gearing up to use Lux?

The rocks.nvim-neorocks repo has mostly treesitter related stuff. It
did have a rock for fzy, so I configured `vhyrro/luarocks.nvim` to
install the rock for fzy. It worked, but I use a rust version of this
fuzzy finder.

### 2025-06-16:

#### Caught a mistake

I think I missed a step while repeating what I did on Noether2 on
Hamilton4 involving the build process. After removing my manual install,

```fish
    $ dfInstall  # triggered lazy.nvim activity with spell file update
    $ nvim       # manual Lazy update and cleanup via :Lazy
    $ sudo apt install liblua5.1-0-dev lua5.1 lua5.1-doc
    $ cd ~/.local/share/nvim/lazy/luarocks.nvim
    $ nvim -l build.lua  # step I missed
    $ nvim
```
 Then while in that directory (I don't know if that matters or not),

```vim
    :Lazy build luarocks.nvim$ fd 'luarocks\.lua'
share/nvim/lazy/mason.nvim/lua/mason-core/installer/compiler/compilers/luarocks.lua
share/nvim/lazy/mason.nvim/lua/mason-core/installer/managers/luarocks.lua
    :checkhealth lazy
      "snip"
    - ❌ ERROR {/home/grs/.local/share/nvim/lazy-rocks/hererocks/bin/luarocks} not installed
    - ⚠️ WARNING {/home/grs/.local/share/nvim/lazy-rocks/hererocks/bin/lua} version `5.1` not installed
    - ⚠️ WARNING Lazy won't be able to install plugins that require `luarocks`.
      "snip"
    :Lazy build lazy
    :checkhealth
        "same result as before"
```

Just as I thought I was seeing the light at the end of the tunnel...

Why is it now looking for a hererocks installation when hererocks is out
of date?

Time to use the nuclear option. Removed `~/.local/share/nvim/lazy` and
`~/.local/share/nvim/lazy`. Now repeat what was done on noether2. After
restarting nvim 2 times, without doing anything else.

```vim
    :checkhealth luarocks-nvim
    ==============================================================================
    luarocks-nvim:                         require("luarocks-nvim.health").check()

    luarocks.nvim ~
    - ✅ OK luarocks is installed, system is prepared to install rocks!

    :checkhealth lazy
    ==============================================================================
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

    ==============================================================================
    telescope._extensions.lazy: require("telescope._extensions.lazy.health").check()

    telescope-lazy.nvim ~

    Plugin installation status ~
    - ✅ OK telescope installed
    - ✅ OK lazy installed
    - ⚠️ WARNING telescope-egrepify not installed (optional)
    - ✅ OK telescope-file-browser installed (optional)
```

Seems to be a house divided?
$ fd 'luarocks\.lua'
share/nvim/lazy/mason.nvim/lua/mason-core/installer/compiler/compilers/luarocks.lua
share/nvim/lazy/mason.nvim/lua/mason-core/installer/managers/luarocks.lua
From `:help luarocks-nvim` with markdown heading reduced,

##### Lazy.nvim Integration

For users employing the Lazy.nvim plugin manager, `luarocks.nvim` can be added to your configuration with the following code:

```lua
{
  "vhyrro/luarocks.nvim",
  priority = 1000, -- Very high priority is required, luarocks.nvim should run as the first plugin in your config.
  config = true,
}
```

Upon installing, an automatic build step will be invoked by `lazy.nvim` in an attempt to compile a local luarocks installation on your machine.
If you're having issues with this, be sure to manually run `:Lazy build luarocks.nvim`!

Generally, other plugins which rely on `luarocks.nvim` as their dependency manager perform automatic
dependency installation in their `build.lua`s, so you don't even have to touch any options yourself!
Just set up this plugin and the rest should be automatic.

##### Installing a Rock List

To install a set of rocks (with the ability to add version constraints) use the following configuration instead:

```lua
{
  "vhyrro/luarocks.nvim",
  priority = 1000, -- Very high priority is required, luarocks.nvim should run as the first plugin in your config.
  opts = {
    rocks = { "fzy", "pathlib.nvim ~> 1.0" }, -- specifies a list of rocks to install
    -- luarocks_build_args = { "--with-lua=/my/path" }, -- extra options to pass to luarocks's configuration script
  },
}
```

Maybe I am over thinking this?

Any case, while looking for luarocks.lua, I found

```fish
    $ cd .local/
    $ fd 'luarocks\.lua'
    share/nvim/lazy/mason.nvim/lua/mason-core/installer/compiler/compilers/luarocks.lua
    share/nvim/lazy/mason.nvim/lua/mason-core/installer/managers/luarocks.lua
```$ fd 'luarocks\.lua'
share/nvim/lazy/mason.nvim/lua/mason-core/installer/compiler/compilers/luarocks.lua
share/nvim/lazy/mason.nvim/lua/mason-core/installer/managers/luarocks.lua

But,

```vim
: checkhealth mason
      "snip"
    mason.nvim [Languages] ~
    - ⚠️ WARNING luarocks: not available
      - ADVICE:
        - spawn: luarocks failed with exit code - and signal -. Could not find executable "luarocks" in PATH.
      "snip"
```

#### All right back to pure Lua

This side journey working getting LuaRocks, or whatever it is morphing
to, to work taught me much about Lua implementations. Time to get back
to standalone Lua development.

---

[1]: https://github.com/grscheller/grok-lua/blob/main/projects/01-hello-world/hw.lua
[2]: https://github.com/torch/luajit-rocks
[3]: https://luarocks.org/
[4]: https://lazy.folke.io/news#11x
[5]: https://luarocks.org/m/luajit
[6]: https://luarocks.github.io/luarocks/releases/
[7]: https://github.com/luarocks/hererocks
[8]:: https://www.lua.org/
