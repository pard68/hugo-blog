+++
title = "Literate Dotfiles"
author = ["Ian S. Pringle"]
draft = false
+++

These are my _literate_ dotfiles. To compile and deploy these files you need to
run `(org-bable-tangle)`. There are no additional settings that are needed to
tangle this. Running the above command will create all the needed directories
and files, and compile the configs specified here and deploy them to the right
locations. To update a config file, make the edits and then run the same
`(org-bable-tangle)` function to update. Because of the `:cache yes` directive,
Org-Babel will only change files if there are changes.


## Log {#log}


### <span class="timestamp-wrapper"><span class="timestamp">&lt;2022-08-25 Thu&gt;</span></span> {#1bd8cd}

I've "disabled" yabai, spacebar, and those related things. I'll probably
eventually delete them from this doc or something like that. Yabai is not
playing nicely and I'm looking for an alternative solution, likely something
with just Hammerspoon alone.


### <span class="timestamp-wrapper"><span class="timestamp">&lt;2022-08-18 Thu&gt;</span></span> {#56d0d5}

I'm trying to decide how to incorporate my doom config into this. I _really_
want to be _orgish_ and use one file for all my configurations. I suspect that
might not eventually and ultimately be what I do though. Nonetheless, I can try!

I'm also not sure if I _want_ to use the `README.org` for my config, but it does
have the advantage of being displayed automatically by nearly all VCS GUIs in
the world.


## Configurations {#configurations}


### Boilerplate {#boilerplate}

This is a brief header explaining that these files are generated with org-babel
and should not be directly edited. This block doesn't actually do anything by
itself, but it can be included in other files by including it in other blocks
with `<<boilerplate-file-header>>`.

<a id="code-snippet--boilerplate-file-header"></a>
```sh
# This file was generated from a literate dotfiles config.
# This file should not be directly edited. Instead edit
# the literate-dotfiles repo and then tangle the file.
# The repo can be found at:
# https://github.com/pard68/literate-dotfiles
```


### Shell {#shell}

First let's setup the various ZSH files that the shell expects. We like A S T H
E T I C home directories, so we're going to hide our ZSH config files in the
`\~/.config/` dir like the good Lord intended.


#### Environment Variables {#environment-variables}

Sadly, ZSH sorta sucks and the `.zshenv` has to live in the home directory, so
we're going to set that up and tell it to look in `.config/zsh/` for all the
rest of the zsh config files we might use. So we'll setup the zshenv file to
have all the right XDG settings, plus point all the various other things that
need to be told to use .config to do so.

First let's add the boilderplate header to this file:

```sh
<<boilerplate-file-header>>
```

<!--list-separator-->

-  XDG and Zsh paths

    ```sh
    export XDG_CONFIG_HOME="$HOME/.config"
    export XDG_DATA_HOME="$HOME/.local/share"
    export XDG_DATA_HOME="$HOME/.cache/"
    export ZDOTDIR="$XDG_CONFIG_HOME/zsh"
    export HISTFILE="$ZDOTDIR/zhistory"

    export DOOM_EMACS=$XDG_CONFIG_HOME/emacs
    export DOOMDIR=$XDG_CONFIG_HOME/doom
    ```

<!--list-separator-->

-  HIST

    We want to make sure our history is nice and long. It's 2022, so we shouldn't
    worry too much about disk space or RAM... I hope

    ```sh
    export HISTSIZE=10000
    export SAVEHIST=10000
    ```

<!--list-separator-->

-  EDITOR

    Let's set our editor quickly so we can make sure we're never far away from
    emacs.

    ```sh
    export EDITOR="/usr/local/bin/emacsclient"
    export VISUAL="/usr/local/bin/emacsclient"
    export ALTERNATE_EDITOR="/usr/local/bin/nvim"
    ```

<!--list-separator-->

-  $PATH

    We'll setup some `$PATH` stuff now too. I really hate editing one-liner `$PATH`
    exports, so we'll just do one per line, why not? Maybe there is a cool way to
    use org and iterate over a list of path values and concatenate them together,
    but IDK how to do that right now...

    ```sh
    export PATH="$HOME/.cargo/bin:$PATH"
    export PATH="$HOME/go/bin:$PATH"
    export PATH="$HOME/.local/bin:$PATH"
    export PATH="/usr/local/bin:$PATH"
    export PATH="/usr/bin:$PATH"
    export PATH="/bin:$PATH"
    export PATH="/usr/local/sbin:$PATH"
    export PATH="/usr/local/go/bin/:$PATH"
    export PATH="$XDG_CONFIG_HOME/emacs/bin/:$PATH"
    export PATH="$HOME/.npm-global/bin:$PATH"
    ```


#### Zshrc {#zshrc}

The zshrc file has to be prefixed with a `.` for Zsh to pick it up. It sorta sucks... but it's whatever I'll never really interact with that file.

First let's add the boilderplate header to this file:

```sh
<<boilerplate-file-header>>
```

<!--list-separator-->

-  A E S T H E T I C

    Your terminal, in Technicolor!

    ```sh
    autoload -U colors && colors
    PROMPT="(%B%T%b) %B%F{magenta}λ%f%b "
    ```

<!--list-separator-->

-  Zsh Options

    [Here](https://zsh.sourceforge.io/Doc/Release/Options.html) is a list of all the zsh options that can be set.

    ```sh
    setopt HIST_SAVE_NO_DUPS
    setopt INC_APPEND_HISTORY
    setopt HIST_IGNORE_SPACE

    setopt AUTO_CD
    setopt AUTO_PUSHD
    setopt PUSHD_IGNORE_DUPS
    setopt PUSHD_SILENT

    REPORTTIME=3
    ```

<!--list-separator-->

-  Ghetto Jump

    There are some neat "jump" plugins like `j` and `z`. But we're just going to DIWhy it!

    ```sh
    alias d='dirs -v'
    for index ({1..9}) alias "$index"="cd + ${index}"; unset index
    ```

<!--list-separator-->

-  Completion

    ```sh
    autoload -U compinit
    zstyle ':completion:*' menu select completer _complete _correct _approximate
    zmodload zsh/complist
    compinit
    _comp_options+=(globdots)
    ```


#### Aliases {#aliases}

Before we make an alias file, let's source them from the zshrc file.

```sh
source $ZDOTDIR/aliases
```

Okay, now onto the aliases file. First let's add the boilderplate header to this file:

```sh
<<boilerplate-file-header>>
```

<!--list-separator-->

-  Clear

    ```sh
    alias c!=clear
    ```

<!--list-separator-->

-  Git

    ```sh
    alias g=git
    alias ga="git add"
    alias ga.="git add ."
    alias gb="git branch"
    alias gbd="git branch -D"
    alias gc="git commit"
    alias gcm="git commit -m"
    alias gca="git commit --amend"
    alias gcm!!="git add .; git commit -m "Update!"; git push"
    alias gcl="git clone"
    alias gco="git checkout"
    alias gd="git diff"
    alias gl="git log"
    alias gm="git merge"
    alias gpl="git pull"
    alias gps="git push"
    alias gps!="git push --force"
    alias gpsu="git push -u origin master"
    alias gri="git rebase -i"
    alias gs="git status"
    ```

<!--list-separator-->

-  ls

    ```sh
    alias l="ls"
    alias la="ls -a"
    alias ll="ls -l"
    alias lla="ls -la"
    ```

<!--list-separator-->

-  mbsync

    ```sh
    alias mbsync="mbsync -c ~/.config/isync/mbsyncrc"
    ```


### Kitty {#kitty}

Kitty is the terminal. I prefer to use. I don't use it much, with emacs around,
but sometimes it's nice to have a real terminal. Before we start, let's add the
boilerplate file header:

```sh
<<boilerplate-file-header>>
```


#### Font {#font}

Set the font

```cfg
font_family scientifica
font_size 16.0
```


#### Default Options {#default-options}

```cfg
open_url_with default
enable_audio_bell no
tab_bar_style powerline
background_opacity 0.9
```


#### Mappings {#mappings}

```cfg
map ctrl+h neighboring_window left
map ctrl+j neighboring_window down
map ctrl+k neighboring_window up
map ctrl+l neighboring_window right
map ctrl+] next_tab
map ctrl+[ previious_tab
```


#### Theme {#theme}

Set the theme we'll use

```cfg
include theme.conf
```

And then define that theme. This is not something I created. I got it from [here](https://github.com/mcchrish/zenbones.nvim/raw/main/extras/kitty/zenbones_dark.conf).

```cfg
<<boilerplate-file-header>>
```

<!--list-separator-->

-  Background and Foreground Colors

    ```cfg
    foreground                      #B4BDC3
    background                      #1C1917
    selection_foreground            #B4BDC3
    selection_background            #3D4042
    ```

<!--list-separator-->

-  Cursor Colors

    ```cfg
    cursor                          #C4CACF
    cursor_text_color               #1C1917
    ```

<!--list-separator-->

-  Tab Colors

    ```cfg
    active_tab_foreground           #B4BDC3
    active_tab_background           #65435E
    inactive_tab_foreground         #B4BDC3
    inactive_tab_background         #352F2D
    ```

<!--list-separator-->

-  Color Definitions

    <!--list-separator-->

    -  Black

        ```cfg
        color0 #1C1917
        color8 #403833
        ```

    <!--list-separator-->

    -  Red

        ```cfg
        color1 #DE6E7C
        color9 #E8838F
        ```

    <!--list-separator-->

    -  Green

        ```cfg
        color2  #819B69
        color10 #8BAE68
        ```

    <!--list-separator-->

    -  Yellow

        ```cfg
        color3  #B77E64
        color11 #D68C67
        ```

    <!--list-separator-->

    -  Blue

        ```cfg
        color4  #6099C0
        color12 #61ABDA
        ```

    <!--list-separator-->

    -  Magenta

        ```cfg
        color5  #B279A7
        color13 #CF86C1
        ```

    <!--list-separator-->

    -  Cyan

        ```cfg
        color6  #66A5AD
        color14 #65B8C1
        ```

    <!--list-separator-->

    -  White

        ```cfg
        color7  #B4BDC3
        color15 #888F94
        ```


### Hammerspoon {#hammerspoon}

I really only want Hammerspoon around on Mac, but I'm not currently sure how to
best add that conditional nature into org-babel. So we'll just install it all
and if it's not Mac... well it'll just never get used. Also, should be noted
that Hammerspoon doesn't seem to respect the XDG stuff and just puts its config
directory in the root of your home dir like a pig.


#### Boilerplate {#boilerplate}

```lua
--[[
<<boilerplate-file-header>>
--]]
```

<!--list-separator-->

-  Hammerspoon CLI Tool

    First thing, let's install the hs cli tool so we can call Hammerspoon from the
    terminal if we need to. By default it'll install to the `/usr/local/bin`.

    ```lua
    require'hs.ipc'
    hs.ipc.cliInstall()
    ```

<!--list-separator-->

-  Logger

    ```lua
    local configLog = hs.logger.new('Config')
    ```

<!--list-separator-->

-  Config Reloader

    And let's setup auto reloading for hammerspoon. First the reload function,
    stolen from [here](https://github.com/wangshub/hammerspoon-config/blob/06ff4db98baa4e568db0f79f3de0c58a578535a2/reload/reload.lua):

    ```lua
    reloadConfig = {
      watcher = {},
    }

    function reloadConfig.reloader(paths)
      doReload = false
      for _, file in pairs(paths) do
        if file:sub(-4) == ".lua" then
          print("A Lua configuration file has changed. Reloading...")
          doReload = true
        end
      end
      if not doReload then
        print("No Lua configuration files have changed. Skipping reload...")
        return
      end
      hs.reload()
    end
    ```

    And then we register a path watcher, provide a method to stop the watcher, and
    then kick it off:

    ```lua
    hammerspoonDir = os.getenv("HOME") .. "/.hammerspoon/"

    function reloadConfig.init()
      reloadConfig.watcher = hs.pathwatcher.new(hammerspoonDir, reloadConfig.reloader)
    end

    function reloadConfig.start()
      reloadConfig.watcher:start()
    end

    function reloadConfig.stop()
      reloadConfig.watcher:stop()
    end

    reloadConfig.init()
    reloadConfig.start()
    ```


#### Screens {#screens}

Let's setup the names of our screens for use later...

```lua
laptopScreen = hs.screen.find("Built%-in Retina Display")
leftScreen =  hs.screen.find("DELL P2414H")
```


#### Keybindings {#keybindings}

Next let's setup a few default key combos.

```lua
local meh = {"ctrl", "alt", "shift"}
local super = {"ctrl", "alt", "cmd"}
local hyper = {"ctrl", "alt", "cmd", "shift"}
```

I'm lazy, so we're going to setup some abstraction over the default hammerspoon
hotkey function so I have to type even less!

```lua
function bk(...)
  hs.hotkey.bind(...)
end
```

Since a lot of keybindings are going to be related to running external commands,
we should setup some abstraction to make interacting with HS' api for running
external commands a bit easier -- my inspiration for this from [here](https://github.com/rtauziac/Hammerspoon-Yabai/blob/4c1c130b089a3c8cc87a3f0dcf13ae0ae64b34a0/.hammerspoon/yabai.lua):

```lua
function strToTable(str)
  t = {}
  for word in string.gmatch(str, "[^%s]+") do
    table.insert(t, word)
  end
  return t
end

function runCmd(bin, strArgs)
  args = strToTable(strArgs)
  local t = hs.task.new(bin,
                        function(err, stdout, stderr)
                          print(err, stdout, stderr)
                        end,
                        function(task, stdout, stderr)
                          print(stdout, stderr)
                          return true
                        end, args)
  t:start()
  if tOut then
    print(tOut)
  end
  if tErr then
    print(tErr)
  end
end

function r(b, a)
  return function() runCmd(b, a) end
end
```

<!--list-separator-->

-  Toggle Dark Mode

    It'd be nice to toggle the system dark-mode with a key. But to do that we need to write a little applescript first:

    ```sh
    tell application "System Events"
    	tell appearance preferences
    		set dark mode to not dark mode
    	end tell
    end tell
    ```

    Great, and now for the keybinding:

    ```lua
    bk(hyper, "d", "Toggle: Dark-mode", function() hs.osascript.applescriptFromFile("applescripts/toggle-dark-mode.applescript") end)
    ```


#### Spaces Management {#spaces-management}

```lua
spaces = require('hs.spaces')

function listAllSpaces()
  local allSpaces = {}
  for _, displaySpaces in pairs(hs.spaces.allSpaces()) do
    for _, spaceID in pairs(displaySpaces) do
      table.insert(allSpaces, spaceID)
    end
  end
  return allSpaces
end

existingSpaces = listAllSpaces()

spacesMenu = hs.menubar.new()
spacesMenu:setTitle("👨‍🚀")
spacesMenu:setTooltip("Spaces Manager")

spacesTable = {
  www = {
    id = {}, -- keep this empty
    layout = {}, -- keep this empty
    emoji = "🌐",
    screen = laptopScreen,
    binding = {mod = super, key = "b"},
    apps = {
      {
        id = {},
        name ="Vivaldi",
        layout = hs.layout.maximized,
        binding = {mod = super, key = "v"},
      },
      {
        id = {},
        name ="Firefox",
        layout = hs.layout.maximized,
        binding = {mod = super, key = "f"},
      },
    },
  },
  terminal = {
    id = {}, -- keep this empty
    layout = {}, -- keep this empty
    emoji = "💻️",
    screen = leftScreen,
    binding = nil,
    apps = {
      {
        id = {},
        name ="kitty",
        layout = hs.layout.maximized,
        binding = {mod = super, key = "q"},
      },
    },
  },
  editor = {
    id = {}, -- keep this empty
    layout = {}, -- keep this empty
    emoji = "📜️",
    screen = leftScreen,
    binding = nil,
    apps = {
      {
        id = {},
        name ="emacs",
        layout = hs.layout.maximized,
        binding = {mod = super, key = "e"},
      },
    },
  },
  comms = {
    id = {}, -- keep this empty
    layout = {}, -- keep this empty
    emoji = "🛰️",
    screen = leftScreen,
    binding = {mod = super, key = "c"},
    apps = {
      {
        id = {},
        name ="Slack",
        layout = hs.layout.maximized,
        binding = {mod = super, key = "s"},
      },
      {
        id = {},
        name = "Microsoft Teams",
        layout = hs.layout.maximized,
        binding = {mod = super, key = "t"},
      },
      {
        id = {},
        name = "Discord",
        layout = hs.layout.maximized,
        binding = {mod = super, key = "r"},
      },
      {
        id = {},
        name = "Signal",
        layout = hs.layout.maximized,
        binding = {mod = super, key = "g"},
      },
    },
  }
}

function tableDiff(a, b)
  -- Find the difference between two tables
  -- Assumes that there is only one difference
  local aa = {}
  for k,v in pairs(a) do aa[v]=true end
  for k,v in pairs(b) do aa[v]=nil end
  for k,v in pairs(aa) do return k end
end

function setupSpace(spaceName, spaceSettings)
  spaces.addSpaceToScreen()
  local newSpaces = listAllSpaces()
  local spaceId = tableDiff(newSpaces, existingSpaces)
  spacesTable[spaceName].id = spaceId
  if spaceSettings.binding then
    print(spaceId)
    bk(spaceSettings.binding.mod,
       spaceSettings.binding.key,
       "Goto " .. spaceName,
       function() spaces.gotoSpace(spaceId) end)
  end
  existingSpaces = newSpaces
  for _, app in pairs(spaceSettings.apps) do
    hs.application.launchOrFocus(app.name)
    app.id = hs.application.find(app.name)
    table.insert(spacesTable[spaceName].layout, {
                    app.id,
                    nil,
                    spaceSettings.screen,
                    app.layout,
                    nil, nil
    })
    if app.binding then
      bk(app.binding.mod,
         app.binding.key,
         "Goto " .. app.name,
         function() hs.application.launchOrFocus(app.name) end)
    end
  end
  hs.layout.apply(spaceSettings.layout)
end

function initSpaces(st)
    for spaceName, spaceSettings in pairs(st) do
      setupSpace(spaceName, spaceSettings)
    end
end

function len(t)
  local n = 0
  for _, _ in pairs(t) do
    n = n + 1
  end
  return n
end

function cleanUnusedSpaces()
  local allSpaces = listAllSpaces()
  local allWindows = {}
  local spacesWindowData = {}
  local hasFullscreen = false
  for _, sid in pairs(allSpaces) do
    local windows = spaces.windowsForSpace(sid)
    for _, i in pairs(windows) do
      allWindows[i] = (allWindows[i] or 0) + 1
    end
    if spaces.spaceType(sid) == 'fullscreen' then
      hasFullscreen = true
    end
    spacesWindowData[sid] = windows
  end
  for sid, data in pairs(spacesWindowData) do
    remove = false
    local copy = data
    for index, id in pairs(data) do
      if allWindows[id] > 1 then
        copy[index] = nil
      end
    end
    if not hasFullscreen and len(copy) < 3 or hasFullscreen and len(copy) < 4 then
      configLog.i("Removing", sid)
      spaces.removeSpace(sid)
    end
  end
end

initSpaces(spacesTable)
cleanUnusedSpaces()
```

Then let's cleanup the empty spaces that might have been created.

```lua

function clearEmptySpaces()
    local allSpaces = listAllSpaces()
end
clearEmptySpaces()
```


#### Spoons {#spoons}

<!--list-separator-->

-  Bootstrap

    <!--list-separator-->

    -  spoonManager

        `spoonManager` is a tool that lets us install Spoons from within Hammerspoon,
        which means we don't need to include the Spoon's source in our literate config!

        I was using this to install all spoons but then I noticed that HS has a Spoon
        for installing spoons and it's a bit more feature-complete than SpoonManager, so
        I'm just using this to install dependencies that SpoonInstall doesn't like (ie
        stackline) and SpoonInstall itself and then we'll let everything else use
        SpoonInstall.

        Let's create a table of spoons we want to manage. It's going to be empty for
        now, but entries should include a table name of the spoon and the git repo or
        zip path of the spoon like such:

        ```lua
        {
          name = "spoon's name",
          uri = "git repo",
          path = "path relative to hammerspoon's dir", // this is an optional value and if it isn't provided, just install into the Spoons/ dir
        }
        ```

        ```lua
        spoonList = {
          {
            name = "SpoonInstall",
            uri = "https://github.com/Hammerspoon/Spoons/raw/master/Spoons/SpoonInstall.spoon.zip"
          }
        }
        <<VimModeSpoon>>
        <<editWithEmacsSpoon>>
        ```

        And of course, let's create spoonManager and then kick off the install process

        ```lua
        spoonManager = {}

        <<spoonManagerInstallZip>>

        function spoonManager.installGit(spoon)
          gitCloneTask =hs.task.new('/usr/bin/git', function(a,b,c) return end, {"clone", spoon.uri, spoon.path})
          gitCloneTask:waitUntilExit()
          gitCloneTask:start()
        end

        function spoonManager.install(spoon)
          ext = string.sub(spoon.uri, -4)
          reloadConfig.stop()
          if ext == ".git" then
            spoonManager.installGit(spoon)
          end
          if ext == ".zip" then
            spoonManager.installZip(spoon)
          else
            configLog.e("Unknown file type, not installing " .. spoon.name)
          end
          reloadConfig.start()
        end

        function spoonManager.installMaybe()
          if spoonList == nil then
            return
          end
          for _, spoon in pairs(spoonList) do
            install_path = "Spoons/" .. spoon.name .. ".spoon"
            installed = nil
            if spoon.path then
              install_path = spoon.path
              installed, _ = hs.fs.mkdir(spoon.path)
              installed = not installed
              if not installed then
                hs.fs.rmdir(spoon.path)
              end
            else
              installed = hs.spoons.isInstalled(spoon.name)
            end
            spoon.path = install_path
            if not installed then
              configLog.i("Installing " .. spoon.name .. " from uri: " .. spoon.uri)
              hs.alert("Install " .. spoon.name .. ". This will just take a moment!")
              spoonManager.install(spoon)
            else
              configLog.i(spoon.name .. " already installed!")
            end
          end
        end

        spoonManager.installMaybe()
        ```

        This part of spoonManager is not my work, I took it from the SpoonInstall [source](https://github.com/Hammerspoon/Spoons/blob/master/Source/SpoonInstall.spoon/init.lua)
        and I took it mostly to _install_ SpoonInstall, since it's part of a monorepo
        and I'd rather steal their function for installing something from a zip than try
        to figure out the best to do sparse git stuff.

        <a id="code-snippet--spoonManagerInstallZip"></a>
        ```lua
        -- Execute a command and return its output with trailing EOLs trimmed. If the command fails, an error message is logged.
        function spoonManager.x(cmd, errfmt, ...)
           local output, status = hs.execute(cmd)
           if status then
              local trimstr = string.gsub(output, "\n*$", "")
              return trimstr
           else
              return nil
           end
        end

        function spoonManager._installSpoonFromZipURL(urlparts, status, body, headers)
           local success=nil
           if (status < 100) or (status >= 400) then
              print("Error downloading %s. Error code %d: %s", urlparts.absoluteString, status, body or "<none>")
           else
              -- Write the zip file to disk in a temporary directory
              local tmpdir=spoonManager.x("/usr/bin/mktemp -d", "Error creating temporary directory to download new spoon.")
              if tmpdir then
                 local outfile = string.format("%s/%s", tmpdir, urlparts.lastPathComponent)
                 local f=assert(io.open(outfile, "w"))
                 f:write(body)
                 f:close()

                 -- Check its contents - only one *.spoon directory should be in there
                 output = spoonManager.x(string.format("/usr/bin/unzip -l %s '*.spoon/' | /usr/bin/awk '$NF ~ /\\.spoon\\/$/ { print $NF }' | /usr/bin/wc -l", outfile),
                             "Error examining downloaded zip file %s, leaving it in place for your examination.", outfile)
                 if output then
                    if (tonumber(output) or 0) == 1 then
                       -- Uncompress the zip file
                       local outdir = string.format("%s/Spoons", hs.configdir)
                       if spoonManager.x(string.format("/usr/bin/unzip -o %s -d %s 2>&1", outfile, outdir),
                             "Error uncompressing file %s, leaving it in place for your examination.", outfile) then
                          -- And finally, install it using Hammerspoon itself
                          print("Downloaded and installed %s", urlparts.absoluteString)
                          spoonManager.x(string.format("/bin/rm -rf '%s'", tmpdir), "Error removing directory %s", tmpdir)
                          success=true
                       end
                    else
                       print("The downloaded zip file %s is invalid - it should contain exactly one spoon. Leaving it in place for your examination.", outfile)
                    end
                 end
              end
           end
           return success
        end

        function spoonManager.installSpoonFromZipURL(url)
           local urlparts = hs.http.urlParts(url)
           local dlfile = urlparts.lastPathComponent
           if dlfile and dlfile ~= "" and urlparts.pathExtension == "zip" then
              a,b,c=hs.http.get(url)
              return spoonManager._installSpoonFromZipURL(urlparts, a, b, c)
           else
              print("Invalid URL %s, must point to a zip file", url)
              return nil
           end
        end

        function spoonManager.installZip(spoon)
          spoonManager.installSpoonFromZipURL(spoon.uri)
        end
        ```

    <!--list-separator-->

    -  SpoonInstall

        SpoonInstall is how we're going to install 99% of spoons, because it's a lot
        better than `spoonManager`.

        First let's setup some stuff:

        ```lua
        hs.loadSpoon('SpoonInstall')
        ```

        This will block HS, but I think it's worth it...

        ```lua
        spoon.SpoonInstall.use_syncinstall = true
        ```

        I'm lazy...

        ```lua
        Install = spoon.SpoonInstall
        ```

<!--list-separator-->

-  Color Picker

    This is a tool to help manage picking colors and selecting their value

    ```lua
    Install:andUse("ColorPicker", {
                     hotkeys = {
                       show = { hyper, "c" }
                     },
                     config = {
                       show_in_menubar = false,
                     },
                     start = true,
    })
    ```

<!--list-separator-->

-  Clipboard Tool

    It's a clipboard manager for text.

    ```lua
    Install:andUse("ClipboardTool", {
                     config = {
                        pase_on_select = true,
                        show_copied_alert = false,
                        show_in_menubar = false,
                     },
                     hotkeys = {
                       toggle_clipboard = { hyper, "v" }

                     },
                     start = true,
    })
    ```

<!--list-separator-->

-  Vim Mode

    This spoon lets you have vim style movement in all the places!

    <a id="code-snippet--VimModeSpoon"></a>
    ```lua
    table.insert(spoonList, {
                   name = "VimMode",
                   uri = "https://github.com/dbalatero/VimMode.spoon.git",
                   path = "Spoons/VimMode.spoon"
    })
    ```

    The settings are pretty straightforward...

    ```lua
    -- VimMode = hs.loadSpoon('VimMode')
    -- vim = VimMode:new()
    -- vim:disableForApp('Emacs'):enterWithSequence('jk')
    ```

<!--list-separator-->

-  Edit With Emacs

    This spoon allows me to take any text area and edit that text area with emacs,
    and then send the text edited in Emacs back to that textbox when done. This
    spoon is from the GitHub user [dmgerman](https://github.com/dmgerman/editWithEmacs.spoon/).

    <a id="code-snippet--editWithEmacsSpoon"></a>
    ```lua
    table.insert(spoonList, {
                   name = "editWithEmacs",
                   uri = "https://github.com/dmgerman/editWithEmacs.spoon.git"})
    ```

    <!--list-separator-->

    -  Load Spoon and Add Bindings

        ```lua
        hs.loadSpoon("editWithEmacs")
        if spoon.editWithEmacs then
          local bindings = {
            edit_selection = { hyper, "e" },
            edit_all = { meh, "e" }
          }
          spoon.editWithEmacs:bindHotkeys(bindings)
        end
        ```

    <!--list-separator-->

    -  hammerspoon.el

        For this to work we need to load some elisp with `(load
        "~/.hammerspoon/Spoons/editWithEmacs.spoon/hammerspoon.el")`. Make sure to put
        that somewhere into your emacs config.

        ```emacs-lisp
        ;;; editWithEmacs.el --- communicate with hammerspoon to editWithEmacs anywhere

        ;; Copyright (C) 2021 Daniel M. German <dmg@turingmachine.org>
        ;;                             Jeremy Friesen <emacs@jeremyfriesen.com>
        ;;

        ;; Author: Daniel M. German <dmg@turingmachine.org>
        ;;         Jeremy Friesen <emacs@jeremyfriesen.com>
        ;;
        ;; Maintainer: Daniel M. German <dmg@turingmachine.org>
        ;;
        ;; Keywords: hammerspoon, os x
        ;; Homepage: https://github.com/dmgerman/editWithEmacs.spoon

        ;; GNU Emacs is free software: you can redistribute it and/or modify
        ;; it under the terms of the GNU General Public License as published by
        ;; the Free Software Foundation, either version 3 of the License, or
        ;; (at your option) any later version.

        ;; GNU Emacs is distributed in the hope that it will be useful,
        ;; but WITHOUT ANY WARRANTY; without even the implied warranty of
        ;; MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
        ;; GNU General Public License for more details.

        ;; You should have received a copy of the GNU General Public License
        ;; along with GNU Emacs.  If not, see <https://www.gnu.org/licenses/>.

        ;;; Commentary:

        ;; Use emacs and hammerspoon to edit text in any input box in os x
        ;; See: https://github.com/dmgerman/editWithEmacs.spoon
        ;;

        ;;; Code:


        (defvar hammerspoon-buffer-mode 'markdown-mode
          "Name of major mode for hammerspoon editing")

        (defvar hammerspoon-buffer-name "*hammerspoon_edit*"
          "Name of the buffer used to edit in emacs.")

        (defvar hammerspoon-edit-minor-map nil
          "Keymap used in hammer-edit-minor-mode.")
        (unless hammerspoon-edit-minor-map
          (let ((map (make-sparse-keymap)))

            (define-key map (kbd "C-c C-c") 'hammerspoon-edit-end)
            (define-key map (kbd "C-c m")   'hammerspoon-toggle-mode)
            (define-key map (kbd "C-c h")   'hammerspoon-test) ;; for testing

            (setq hammerspoon-edit-minor-map map)))

        (define-minor-mode hammerspoon-edit-minor-mode
          "Minor mode to help with editing with hammerspoon"

          :global nil
          :lighter   "_hs-edit_"
          :keymap hammerspoon-edit-minor-map

          ;; if disabling `undo-tree-mode', rebuild `buffer-undo-list' from tree so
          ;; Emacs undo can work
          )

        (defun hammerspoon-toggle-mode ()
          "Toggle from Markdown Mode to Org Mode."
          (interactive)
          (if (string-equal "markdown-mode" (format "%s" major-mode))
              (org-mode)
            (markdown-mode))
          (hammerspoon-edit-minor-mode))

        (defun hammerspoon-do (command)
          "Send Hammerspoon the given COMMAND."
          (interactive "sHammerspoon Command:")
          (setq hs-binary (executable-find "hs"))
          (if hs-binary
              (call-process hs-binary
                            nil 0 nil
                            "-c"
                            command)
            (message "Hammerspoon hs executable not found. Make sure you hammerspoon has loaded the ipc module")))

        (defun hammerspoon-alert (message)
          "Show given MESSAGE via Hammerspoon's alert system."
          (hammerspoon-do (concat "hs.alert.show('" message "', 1)")))

        (defun hammerspoon-test ()
          "Show a test message via Hammerspoon's alert system.
        If you see a message, Hammerspoon is working correctly."
          (interactive)
          (hammerspoon-alert "Hammerspoon test message..."))

        (defun hammerspoon-edit-end ()
          "Send, via Hammerspoon, contents of buffer back to originating window."
          (interactive)
          (mark-whole-buffer)
          (call-interactively 'kill-ring-save)
          (hammerspoon-do (concat "spoon.editWithEmacs:endEditing(False)"))
          (previous-buffer))

        (defun hammerspoon-edit-begin ()
          "Receive, from Hammerspoon, text to edit in Emacs"
          (interactive)
          (let ((hs-edit-buffer (get-buffer-create hammerspoon-buffer-name)))
            (switch-to-buffer hs-edit-buffer)
            (erase-buffer) ; Ensure we have a clean buffer
            (yank)
            (funcall hammerspoon-buffer-mode)
            (hammerspoon-edit-minor-mode)
            (message "Type C-c C-c to send back to originating window")
            (exchange-point-and-mark)))
        ```


### isync/mbsync {#isync-mbsync}

This is my isync/mbsync config. I'm using the password store for secrets when
applicable.

```sh
<<boilerplate-file-header>>
```


#### Personal Protonmail Accounts {#personal-protonmail-accounts}

I have a number of protonmail accounts. They have virtually the same settings,
even the same password. I am not using the password store for the Proton email
accounts because this password is only applicable to the Proton bridge on my
machine and I don't have a concern about storing it in plaintext since it's
already available in plaintext on the machine anyway. I am just storing it in a
txt file so that this is a bit more portable.

There must be a empty line between accounts. So for this to work we need one
empty line at the end of each config. `org-tangle` removes trailing whitespace,
so this means we need to add a new line and then an empty comment. There might
be a better way to do this but I am unaware of it.

<!--list-separator-->

- <span class="org-todo todo TODO">TODO</span>  Make this programmatic, so it just needs a list of email addresses

<!--list-separator-->

-  ipringle@protonmail.com

    ```sh
    IMAPAccount ipringle-protonmail
    Host 127.0.0.1
    Port 1143
    User pard@0x44.pw
    PassCmd "cat ~/.config/isync/proton-bridge-password"
    SSLType NONE

    IMAPStore ipringle-protonmail-remote
    Account ipringle-protonmail

    MaildirStore ipringle-protonmail-local
    Subfolders Verbatim
    Path ~/mail/ipringle@protonmail.com/
    Inbox ~/mail/ipringle@protonmail.com/INBOX

    Channel ipringle-protonmail
    Far :ipringle-protonmail-remote:
    Near :ipringle-protonmail-local:
    Patterns *
    CopyArrivalDate yes
    Create Both
    Expunge Both
    SyncState *

    #
    ```

<!--list-separator-->

-  ian@dapringles.com

    ```sh
    IMAPAccount ian-dapringles
    Host 127.0.0.1
    Port 1143
    User ian@dapringles.com
    PassCmd "cat ~/.config/isync/proton-bridge-password"
    SSLType NONE

    IMAPStore ian-dapringles-remote
    Account ian-dapringles

    MaildirStore ian-dapringles-local
    Subfolders Verbatim
    Path ~/mail/ian@dapringles.com/
    Inbox ~/mail/ian@dapringles.com/INBOX

    Channel ian-dapringles
    Far :ian-dapringles-remote:
    Near :ian-dapringles-local:
    Patterns *
    CopyArrivalDate yes
    Create Both
    Expunge Both
    SyncState *

    #
    ```

<!--list-separator-->

-  pard@0x44.pw

    ```sh
    IMAPAccount pard-0x44
    Host 127.0.0.1
    Port 1143
    User pard@0x44.pw
    PassCmd "cat ~/.config/isync/proton-bridge-password"
    SSLType NONE

    IMAPStore pard-0x44-remote
    Account pard-0x44

    MaildirStore pard-0x44-local
    Subfolders Verbatim
    Path ~/mail/pard@0x44.pw/
    Inbox ~/mail/pard@0x44.pw/INBOX

    Channel pard-0x44
    Far :pard-0x44-remote:
    Near :pard-0x44-local:
    Patterns *
    CopyArrivalDate yes
    Create Both
    Expunge Both
    SyncState *

    #
    ```


#### Work Gmail {#work-gmail}

This is my config for my work gmail account. I'm not tangling this because using
Gmail with mbsync/mu4e is super annoying and I cannot get sending to work for
some reason. Plus we're migrating _away_ from Gmail so it's less important.

```sh
# ian@hydrobuilder.com
IMAPAccount work-gmail
Host imap.gmail.com
User ian@hydrobuilder.com
PassCmd "pass email/ian@hydrobuilder.com"
AuthMechs LOGIN
SSLType IMAPS

IMAPStore gmail-remote
Account gmail

MaildirStore gmail-local
Path ~/mail/hydrobuilder/
Inbox ~/mail/hydrobuilder/INBOX
Subfolders Verbatim

Channel gmail
Far :gmail-remote:
Near :gmail-local:
CopyArrivalDate yes
Patterns * ![Gmail]* "[Gmail]/Sent Mail" "[Gmail]/Trash"
Create Both
Expunge Both
SyncState *

#
```


#### Work Outlook {#work-outlook}

Nothing fancy here, should be noted that the password is an app-password because
of 2fa requirements.

```sh
# i.pringle@hbhold.com
IMAPAccount work
Host outlook.office365.com
User i.pringle@hbhold.com
PassCmd "pass email/i.pringle@hbhold.com"
AuthMechs LOGIN
SSLType IMAPS

IMAPStore work-remote
Account work

MaildirStore work-local
Path ~/mail/ipringle@hbhold.com/
Inbox ~/mail/ipringle@hbhold.com/Inbox
Subfolders Verbatim

Channel work
Far :work-remote:
Near :work-local:
Patterns "INBOX" *
CopyArrivalDate yes
Create Both
Sync all
Expunge Both
SyncState *

#
```


### yabai {#yabai}

\#:header-args: :tangle ~/.config/yabai/yabairc :comments link :mkdirp yes :padline no :noweb tangle :cache yes :tangle-mode (identity #o755)


#### Boilerplate {#boilerplate}

```shell
#!/usr/bin/env sh
<<boilerplate-file-header>>
```

Let's make sure yabai is auto-loading the scripting additions on start -- this
requires that we can run `sudo yabai` commands without a password.

```shell
yabai -m signal --add event=dock_did_restart action="sudo yabai --load-sa"
```


#### Config {#config}

<!--list-separator-->

-  Defaults

    We're going to just leave the mouse totally out of the picture:

    ```shell
    # global settings
    yabai -m config mouse_follows_focus          off
    yabai -m config focus_follows_mouse          off
    ```

    And let's make new windows spawn to the left so that they don't take over the
    master window and to the bottom:

    ```shell
    yabai -m config window_placement             first_child
    yabai -m config window_topmost               off
    ```

    The default layout:

    ```shell
    yabai -m config layout                       bsp
    ```

    Let's make room for the bar.

    ```shell
    BAR_HEIGHT=$(spacebar -m config height)
    yabai -m config external_bar all:$BAR_HEIGHT:0
    ```

    These are the things we always want to float:

    ```shell
    yabai -m rule --add app='^System Information$' manage=off
    yabai -m rule --add app='^System Preferences$' manage=off
    yabai -m rule --add title='Preferences$' manage=off
    yabai -m rule --add title='Settings$' manage=off
    ```

    FIN

    ```shell
    echo "yabai configuration loaded.."
    ```

<!--list-separator-->

-  Layouts

    <!--list-separator-->

    -  BSP

    <!--list-separator-->

    -  Float

    <!--list-separator-->

    -  Stacked

        When a space is stacked, these rules apply -- to make it work nicely with
        stackline

        ```shell
        yabai -m signal --add event=window_created action="~/.config/yabai/refresh.sh"
        yabai -m signal --add event=window_destroyed action="~/.config/yabai/refresh.sh"
        ```

        And of course `refresh.sh`, which I stole from these two lovely [comm](https://github.com/AdamWagner/stackline/issues/57#issuecomment-766596737)-[ents](https://github.com/AdamWagner/stackline/issues/57#issuecomment-873378890):
        \##+begin_src shell :tangle ~/.config/yabai/refresh.sh :comments link :mkdirp yes :padline no :noweb tangle :cache yes :tangle-mode (identity #o755)

        ```shell
        #!/usr/bin/env bash

        number_of_windows=$(yabai -m query --windows --space | /usr/local/bin/jq 'length')
        number_of_stacked=$(yabai -m query --windows --space | /usr/local/bin/jq -c 'map(select(."stack-index" != 0)) | length')
        currspace=$(yabai -m query --spaces --space | /usr/local/bin/jq '.index')

        padding=0
        spadding=40

        [[ "$number_of_windows" -eq 1 ]] && padding=0
        [[ "$number_of_stacked" = 0 ]] && spadding=$padding

        yabai -m config --space "$currspace" top_padding $padding
        yabai -m config --space "$currspace" bottom_padding $padding
        yabai -m config --space "$currspace" left_padding $spadding
        yabai -m config --space "$currspace" right_padding $padding
        yabai -m config --space "$currspace" window_gap $padding
        ```

<!--list-separator-->

-  Keybindings

    First, some abstraction to make calling into Yabai a bit simpler -- I stole this
    from [here](https://github.com/rtauziac/Hammerspoon-Yabai/blob/4c1c130b089a3c8cc87a3f0dcf13ae0ae64b34a0/.hammerspoon/yabai.lua):
    \##+begin_src lua :tangle ~/.hammerspoon/init.lua

    ```lua
    function yabaiCmd(args)
      yBin = "/usr/local/bin/yabai"
      runCmd(yBin, args)
    end

    function y(a)
      return function() yabaiCmd(a) end
    end
    ```

    And now the hotkeys!

    Let's setup a reload hotkey for Yabai:
    \##+begin_src lua :tangle ~/.hammerspoon/init.lua

    ```lua
    -- bk(super, "r", "Yabai Reloaded", r("/bin/launchctl", {"kickstart", "-k", "gui/${UID}/homebrew.mxcl.yabai"}))
    bk(super, "r", "Yabai: Reloaded", r("/usr/local/bin/brew", "services restart yabai"))
    ```

    Now some spaces related hotkeys!
    \##+begin_src lua :tangle ~/.hammerspoon/init.lua

    ```lua
    bk(super, "n", "Yabai: Created a new space", y("-m space --create"))
    ```

    It'd be nice to be able to toggle the various layouts for a space:
    \##+begin_src lua :tangle ~/.hammerspoon/init.lua

    ```lua
    bk(super, "s", "Yabai: Stacked Layout", y("-m space --layout stack"))
    bk(super, "b", "Yabai: BSP Layout", y("-m space --layout bsp"))
    bk(super, "f", "Yabai: Floating Layout", y("-m space --layout float"))
    ```

    How about some stacking related one?!
    \##+begin_src lua :tangle ~/.hammerspoon/init.lua

    ```lua

    ```

    These some bindings [someone created](https://github.com/koekeishiya/yabai/issues/203#issuecomment-1088641580) so that you can use the same keys for
    cycling yabai stacked and non-stacked spaces. I need to convert this to Lua so
    we can do this with Hammerspoon.

    ```shell
    cmd + shift - k : if [ "$(yabai -m query --spaces --space | jq -r '.type')" = "stack" ]; then (yabai -m window --focus stack.next || yabai -m window --focus stack.first); else yabai -m window --focus next || yabai -m window --focus first; fi
    cmd + shift - j : if [ "$(yabai -m query --spaces --space | jq -r '.type')" = "stack" ]; then (yabai -m window --focus stack.prev || yabai -m window --focus stack.last); else yabai -m window --focus prev || yabai -m window --focus last; fi
    ```

<!--list-separator-->

-  Named Spaces

    <!--list-separator-->

    -  Comms Space

        This is the space for my communication apps, namely Slack, Teams, Signal, and Discord

        ```shell

        ```


#### Yabai Utils {#yabai-utils}

<!--list-separator-->

-  stackline

    This is a spoon for Hammerspoon and it will help us visualize yabai's stacked
    windows

    <a id="code-snippet--stacklineSpoon"></a>
    ```lua
    table.insert(spoonList, {
                   name = "stackline",
                   uri = "git@github.com:pard68/stackline.git",
                   path = "stackline"
    })
    ```

    Stackline isn't technically a spoon but that might be changing, and since I'd rather just pretend it is than write a special case in the `spoonManager` just for stackline, we're just gonna have to get a little hacky...
    \##+begin_src lua :tangle ~/.hammerspoon/init.lua

    ```lua
    sl = require('stackline')
    sl:init()
    ```

    We also need to add the following to the yabai config so that stackline redraws
    can be triggered with yabai signals to speed up the drawing process. These
    signals came from the these two issues in the stackline repo; "[Trigger refreshes
    using yabail -m signal](https://github.com/AdamWagner/stackline/issues/110)" and "[Reduce redraw lag when switched spaces](https://github.com/AdamWagner/stackline/issues/113)"
    \##+begin_src shell :tangle ~/.config/yabai/yabairc

    ```shell
    yabai -m signal --add event=space_changed action="hs -c 'stackline.manager:update({forceRedraw = true})'"
    yabai -m signal --add event=display_added action="hs -c 'stackline.refresh()"
    yabai -m signal --add event=display_removed action="hs -c 'stackline.refresh()"
    yabai -m signal --add event=display_changed action="hs -c 'stackline.refresh()"
    yabai -m signal --add event=space_changed action="hs -c 'stackline.refresh()"
    yabai -m signal --add event=application_visible action="hs -c 'stackline.refresh()"
    yabai -m signal --add event=application_hidden action="hs -c 'stackline.refresh()"
    yabai -m signal --add event=window_created action="hs -c 'stackline.refresh()"
    yabai -m signal --add event=window_destroyed action="hs -c 'stackline.refresh()"
    ```

    These two lines help with some issues when you switch focus with yabai commands. I found them in this [issue comment](https://github.com/AdamWagner/stackline/issues/67#issuecomment-1193396400).
    \##+begin_src shell :tangle ~/.config/yabai/yabairc

    ```shell
    yabai -m signal --add event=window_created title="Hammerspoon Console" action="yabai -m window \$(yabai -m query --windows | jq '.[] | select(.app==\"Hammerspoon\" and .name==\"Hammerspoon Console\") | .id') --minimize"
    yabai -m rule --add app="Hammerspoon" title="" manage=on layer="above" sticky=on
    ```

    <!--list-separator-->

    -  Keybindings

<!--list-separator-->

-  ymsp

    The Yabai Master-stack Plugin is a tool that takes the BSP mode of Yabai and instead switches Yabai to a master-stack layout like in dwm. This means there is one main window which takes up the majority of the screen and then the remaining windows take up an equal portion of the remaining portion of the screen. To use ymsp we need to install it from npm:

    ```shell
    npm install --global yabai-master-stack-plugin
    ```

    And then add the following config settings for ymsp:
    \##+begin_src json :tangle ~/.config/ymsp/ymsp.config.json :comments no :mkdirp yes :padline no :cache yes

    ```json
    {
      "yabaiPath": "/usr/local/bin/yabai"
    }
    ```

    Finally to connect ymsp to yabai we need to add the following signals:

    ```shell
    yabai -m signal --add event=window_created action='ymsp window-created'
    yabai -m signal --add event=application_launched action='ymsp window-created'
    yabai -m signal --add event=window_moved action='ymsp window-moved'
    ymsp on-yabai-start
    ```


### spacebar {#spacebar}

\#:header-args: :tangle ~/.config/spacebar/spacebarrc :comments link :mkdirp yes :padline no :noweb tangle :cache yes :tangle-mode (identity #o755)


#### Boilerplate {#boilerplate}

```shell
#!/usr/bin/env sh
<<boilerplate-file-header>>
```


#### Config {#config}

<!--list-separator-->

-  Defaults

    ```shell
    spacebar -m config position top
    spacebar -m config height 26
    spacebar -m config title on
    spacebar -m config spaces on
    spacebar -m config clock on
    spacebar -m config power on
    spacebar -m config padding_left 20
    spacebar -m config padding_right 20
    spacebar -m config spacing_left 25
    spacebar -m config spacing_right 15
    spacebar -m config text_font "Helvetica Neue:Bold:12.0"
    spacebar -m config icon_font "Font Awesome 6 Free:Solid:12.0"
    spacebar -m config background_color 0xff202020
    spacebar -m config foreground_color 0xffa8a8a8
    spacebar -m config power_icon_color 0xffcd950c
    spacebar -m config battery_icon_color 0xffd75f5f
    spacebar -m config dnd_icon_color 0xffa8a8a8
    spacebar -m config clock_icon_color 0xffa8a8a8
    spacebar -m config power_icon_strip  
    spacebar -m config space_icon •
    spacebar -m config space_icon_color 0xffffab91
    spacebar -m config space_icon_color_secondary 0xff78c4d4
    spacebar -m config space_icon_color_tertiary 0xfffff9b0
    spacebar -m config space_icon_strip 1 2 3 4 5 6 7 8 9 10
    spacebar -m config clock_icon 
    spacebar -m config dnd_icon 
    spacebar -m config clock_format "%y/%m/%d %R"

    echo "spacebar configuration loaded.."
    ```


## License {#license}

```:tangle
Literate Dotfiles -- Collection of configuration files written and compiled with org-mode
Copyright (C) 2022 Ian S. Pringle

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <https://www.gnu.org/licenses/>.
```
