# Acquired Knowledge

A running list of random things I've learned about computery things.

## 2023

### Run command with sudo but maintain PATH

In particular this helps with errors like "sudo: npm: command not found" or "sudo: node: command not found":

```shell
sudo -E env "PATH=$PATH" <command>
```

[source](https://stackoverflow.com/a/29400598)

### Create an animated `.gif` from a bunch of `.png` files

```bash
convert -extent 600x600 -delay 8 -monitor -background none -gravity Center -loop 0 -alpha set -dispose previous *.png animation.gif
```

### Debug Mocha tests in the browser when they keep opening new debuggers

If you try to debug Mocha tests in Chrome's Node inspector tool but Mocha keeps opening new debuggers and either skipping your debugger or waiting for eternity for your debugger to attach (the behavior depends on which inspect flag you use), make sure that you've added all ports as connections.

1. run Mocha with `--inspect-brk` to make it pause
2. open Chrome
3. go to `chrome://inspect/#devices` in Chrome
4. click `Open dedicated DevTools for Node`
5. click on the `Connection` tab (next to the `Console` tab probably)
6. find, in your terminal that orignially ran Mocha, the latest `Debugger listening on` log
7. copy the host from the websocket (`ws://`) URL (it should look like `127.0.0.1:XXXX`, make sure to include the XXXX part in your copy, the port number, but leave out the protocol, `ws://`)
8. back in Chrome, click `Add connection`
9. paste in the copied host

Now Chrome will also attach itself to debuggers on that host and you should be able to debug Mocha again!

### ~~Fit incredibly slow autocomplete for zsh on macOS when inside of git repositories~~

This doesn't actually work

1. Install `omz`: https://github.com/ohmyzsh/ohmyzsh#basic-installation
2. Copy `~/.zshrc.pre-oh-my-zsh` back to `~/.zshrc`
3. add `plugins=(gitfast)` to `~/.zshrc`

## 2022

### Automatically change node version on directory change in zsh

Put the following in your `~/.zshrc` file:

```shell
# place this after nvm initialization!
autoload -U add-zsh-hook
load-nvmrc() {
  local node_version="$(nvm version)"
  local nvmrc_path="$(nvm_find_nvmrc)"

  if [ -n "$nvmrc_path" ]; then
    local nvmrc_node_version=$(nvm version "$(cat "${nvmrc_path}")")

    if [ "$nvmrc_node_version" = "N/A" ]; then
      nvm install
    elif [ "$nvmrc_node_version" != "$node_version" ]; then
      nvm use
    fi
  elif [ "$node_version" != "$(nvm version default)" ]; then
    echo "Switching to default node version"
    nvm use default
  fi
}
add-zsh-hook chpwd load-nvmrc
load-nvmrc
```

[source](https://stackoverflow.com/a/57839539)

For any directory that should have a specific Node version, create a `.nvmrc` file in that directory and simply paste the Node version into that file.

Examples:
  - `v16.16.0`
  - `16`
  - `12.22.12`



### Enable touch id for terminal `sudo`

Add the line
```
auth       sufficient     pam_tid.so
```

at the top of `/etc/pam.d/sudo` (will need sudo access to write that file).

You'll need to do this after at least every OS update as that file gets overwritten in those updates.

[source](https://apple.stackexchange.com/a/306324)

### Add upstream remote for a repo fork

```bash
git remote add upstream <URL-to-main-repo>
```

[source](https://stackoverflow.com/a/7244456)

### Wrap a native web component in a React component

https://coryrylan.com/blog/using-web-components-in-react

### Change default npm license

```bash
npm config set init-license "MIT"
```

## 2021

### Fix `git commit` stalling due to gpg signing

```killall gpg-agent && gpg-agent --daemon```

### Photoshop keybindings for GIMP

https://github.com/electrovir/photoshop-keymap-for-gimp

### Prevent Firefox from popping a menu-bar open after pressing alt

1. go to about:config
2. If you haven't accepted the risk warning already, read that and accept it. (If you don't accept it you can't fix this.)
3. search for `ui.key.menuAccessKeyFocuses`
4. set it to `false`

[source](https://support.mozilla.org/en-US/questions/1278533)

### Fix navigation keyboard shorctus on macOS terminal and in `nano`

1. Install [Karabiner Elements](https://karabiner-elements.pqrs.org).
2. Install [Homebrew](https://brew.sh).
3. Follow instructions from my mirror of the [karabiner-elements-terminal-navigation](https://github.com/electrovir/karabiner-elements-terminal-navigation) config.
4. Update `nano` with `brew install nano`.
6. Insert the following code into your `~/.nanorc` file (create the file if necessary, start with an empty file). There are probably other keybindings that need to be fixed for nano but I haven't run into them yet.
   ```
   unbind M-B all 
   bind M-B prevword main 
   unbind M-F all 
   bind M-F nextword main
   unbind ^W all 
   bind ^W cutwordleft main
   unbind M-D all 
   bind M-D cutwordright main
   ```

[source](https://unix.stackexchange.com/a/392309)

### macOS high trustd cpu usage

Boot into recovery mode and rename `valid.sqlite3` in `/private/var/protected/trustd/` (Big Sur, macOS 11) or `/Library/Keychains/crls/`. Reboot.

[source](https://lapcatsoftware.com/articles/trustd.html)

### Frozen `Ubuntu Software`

It's actually the snap store. Do this:

```bash
sudo killall snap-store
```

### Find all new instances of `console.log` on your branch

```
git diff -U0 --right-only --cherry-pick origin/master...HEAD -S"console.log"
```

### Find all open PRs on your repos (in GitHub)

Use `org:[username]`. Example:

```
is:open is:pr org:electrovir
```

### Fix audio volume on Linux

1. Use the following CLI: `alsamixer`
2. Press `F6` to pick your output device.
3. Inspect the `PCM` setting. Turn up or down as needed (navigate with arrow keys).

[source](https://askubuntu.com/a/390744)


### Add quit option to Finder

Works in macOS Big Sur at least.

```bash
defaults write com.apple.finder QuitMenuItem -bool false; killall Finder
```

[source](https://www.defaults-write.com/adding-quit-option-to-os-x-finder/)

### Reset a repository to its base (template) repository

Just treat it as another remote and then reset to it:

```bash
git remote add base <git URL/SSH of template repo here>
git fetch base
git reset --hard base/master
```

### Install Discord update on Ubuntu

Discord has a ridiculous `must be your lucky day` pop up when there's an update but provides no steps on how to isntall the update, which fails to install on Ubuntu because it just says "Oh yeah Discord is already installed what are you even trying to do?"

1. Download the Discord `.deb` file for Ubuntu
2. `cd` to the directory that it's downloaded in
3. Run the following code:
   ```bash
   sudo apt install ./<discord-update-file>.deb
   ```

[source](https://askubuntu.com/a/1274186)

### Replace duplicate lines with global regex

  * the Regex (in JavaScript, note the `g` flag)
    ```javascript
    /^(.*)(\n\1)+$/g
    ```
   * the replacement
     ```
     $1
     ```
[source](https://toniguga.it/blog/2020/03/17/how-to-remove-duplicate-lines-in-visual-studio-code/)

### find open files from drive on Mac

This is useful for tracking down what's preventing a drive from being ejected.

```bash
sudo lsof | grep /Volumes/<drive-name-here>
```

[source](https://serverfault.com/questions/159422/os-x-determine-which-application-is-accessing-a-hdd-and-preventing-ejection)

If it's Spotlight, go to System Preferences > Spotlight > Privacy > `+` > select drive

[source](https://discussions.apple.com/thread/3166572)

### globally git ignore `.DS_Store` files (on Mac)

```bash
echo .DS_Store > ~/.gitignore_global
git config --global core.excludesfile ~/.gitignore_global
```

[source (coderwall.com)](https://coderwall.com/p/vz6ymw/how-to-globally-gitignore-ds_store)

### .local domain warning

If Ubuntu regularly throws the following error:

> Your current network has a .local domain... Avahi network... the service has been disabled.

You can turn off Avahi by setting the following in `/etc/default/avahi-daemon`:

```
AVAHI_DAEMON_DETECT_LOCAL=0
```

[source](https://askubuntu.com/a/339709)

## 2020

### git prune

> Auto packing the repository in background for optimum performance.

> See "git help gc" for manual housekeeping.

When the above starts happening a lot, the following are potential solutions:

  * Prune now
    
    From https://stackoverflow.com/a/32670806
    ```bash
    git reflog expire --expire-unreachable=now --all
    git gc --prune=yesterday
    ```
  * Prune more often
  
    From https://stackoverflow.com/a/41771616
    ```bash
    git config gc.pruneExpire 1.week.ago
    ```

### Modify Mac OS Dock timing

```bash
defaults write com.apple.dock autohide-time-modifier -float 0 # make dock animation duration shorter
defaults write com.apple.dock autohide-delay -float 0 # make dock animation delay much shorter
killall Dock # relaunch dock
```

### DisplayLink Troubleshooting

Use the [`displaylink-debian` repo](https://github.com/AdnanHodzic/displaylink-debian) for installation.

  - [common-issues](https://github.com/AdnanHodzic/displaylink-debian/blob/master/post-install-guide.md#troubleshooting-most-common-issues)
  - [post-install guide](https://github.com/AdnanHodzic/displaylink-debian/blob/master/post-install-guide.md)

Note that the "common-issues" link is actually inside of the "post-install guide" link.

### Rename multiple files (Ubuntu)

```bash
# syntax
rename 's/<old-string>/<new-string>/g' <files>
# example
rename 's/.ts/.icon.ts/g' *.ts
```

### Type special characters in Linux (Ubuntu)

Press `ctrl+shift+u` then the unicode character code.

For example, to type "•", do the following:

1. hit `ctrl+shift+u`
2. type `2022`
3. type a space

No you have a •!

### Remove a known host for ssh (on Mac at least)

This is useful when, for example, you insert a new SD card into a Raspberry Pi that you've connected to before. This is needed to prevent the `Someone could be eavesdropping on you right now (man-in-the-middle attack)!` ssh error.

Open this file 
```bash
code ~/.ssh/known_hosts
```

Search for the IP address of the Pi in the file, remove that line.

### Enable `code` CLI for VS Code on Mac

In VS Code, press `cmd+p` and type the following:

```
>Shell Command: Install 'code' command in PATH
```

Run the command. `code` will now be available in new Terminal shells.

### Enable `ssh` for headless Raspberry Pi

Mount the Raspberry Pi's SD card on another computer.

```bash
# cd into the mounted SD card
cd /Volumes/boot
touch ssh
```

Eject the SD card and insert it back into the Pi. `ssh` will be enabled now.

### Keyboard shortcut to toggle Apple keyboard Fn mode on Linux

Change the owner of the `fnmode` file:

```bash
sudo chown <your-username> /sys/module/hid_apple/parameters/fnmode
```

**Note**: this `chown` does not persist through reboots. You must either write a startup script the changes it, change it in your `.bashrc`, or somehow give the keyboard shortcut execution environment access to `sudo`.

Create a `.sh` file with the following contents:

```bash
#!/usr/bin/env bash

path="/sys/module/hid_apple/parameters/fnmode"
currentValue=$(cat "$path");

if [ "$currentValue" -eq "1" ]; then
    echo "2" > "$path"
else
    echo "1" > "$path"
fi
```

Make that script executable:

```bash
chmod +x <path/to/script>
```

Open Ubuntu's `Settings` app. Navigate to Devices > Keyboard. Scroll to the bottom and click `+`. Enter a `Name` and `Shortcut`. For `Command`, use the following:

```
bash /absolute/path/to/script.sh
```

The path to your script _must_ be an absolute path.

Now the `Shortcut` you entered should toggle the Fn mode between media keys and function keys!

### Setup GitHub Actions

My [catch-exit repo has an example](https://github.com/electrovir/catch-exit/blob/master/.github/workflows/tests.yml) of using GitHub Actions to run tests.

## 2019

### Edit macOS terminal.app prompt

Enter the following into `~/.bash_profile`:
```bash
export PS1="\[\e[33m\]\D{%F-%R} \[\e[34m\]\u@\h \[\e[33m\]\w\[\e[m\]\\$ "
```

* `\[\e[32m\]`: colors from [this wikipedia page](https://en.wikipedia.org/wiki/ANSI_escape_code#3/4_bit)
* `\D{%F-%R}`: date / time formatting from [here](https://ss64.com/osx/syntax-strftime.html)
* `\u@\h`: username @ host from [here](https://ss64.com/bash/syntax-prompt.html)

You can type just plain `PS1="<your-format-here>"` into the shell to test out a format without opening a new shell over and over.
