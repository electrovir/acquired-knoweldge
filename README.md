# Acquired Knowledge

A running list of random things I've learned about computery things.
## 2021

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
# make dock animation duration shorter (250 ms)
defaults write com.apple.dock autohide-time-modifier -float 0.25
# make dock animation delay much shorter (100 ms)
defaults write com.apple.dock autohide-delay -float 0.1
# relaunch dock
killall Dock
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
