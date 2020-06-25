# Acquired Knowledge

A running list of random things I've learned about computery things.

## 2020

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
export PS1="\[\e[32m\]\D{%F-%R} \[\e[34m\]\u@\h \[\e[32m\]\W\[\e[m\]\\$ "
```

* `\[\e[32m\]`: colors from [this wikipedia page](https://en.wikipedia.org/wiki/ANSI_escape_code#3/4_bit)
* `\D{%F-%R}`: date / time formatting from [here](https://ss64.com/osx/syntax-strftime.html)
* `\u@\h`: username @ host from [here](https://ss64.com/bash/syntax-prompt.html)

You can type just plain `PS1="<your-format-here>"` into the shell to test out a format without opening a new shell over and over.
