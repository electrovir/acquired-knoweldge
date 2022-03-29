# SD Card

An SD card needs to be initialized first from another computer for the Pi to use.

1. Install the imager software: https://www.raspberrypi.com/software.
2. Insert/mount your microSD card.
3. Run the imager software (on macOS it's called `Raspberry Pi Imager.app`).
4. Click `CHOOSE OS`. (The sub steps below explain how to find the 64 bit OS. [The Pi 3 and beyond support 64 bit.](https://www.raspberrypi.com/news/raspberry-pi-os-64-bit/))
    1. Click `Raspberry Pi OS (other)`.
    2. Click `Raspberry Pi OS (64-bit)`.
4. Click `CHOOSE STORAGE`.
    1. Find your microSD card.
    2. Double check that you selected the proper storage device.
    3. Triple check that you selected the microSD card. _You don't want to accidentally write the OS to one of your external drives._
6. Click the gear in the bottom right to configure default settings.
    1. Set a good host name.
    2. Check `Enable SSH`.
    3. You can set the username and password but I've found that this step happens during the first-run wizard anyway.
    4. Check `Configure wifi` and enter the information for your wifi network. Alternatively, have an ethernet cord ready.
    5. You can check `Set locale settings` but I've found the first-run wizard goes through this anyway.
    6. Click `Save`.
7. Click `WRITE`.
8. Wait for the write process to complete.
9. Make sure that the microSD card has been ejected (check your relevant file system app).
10. Remove the microSD card.
11. Insert the microSD card into the Raspberry Pi. It should slide in easily, if you need to apply force try flipping it around.

# Startup the Pi

1. Plug in the power supply.
2. Plug in a screen (turn the screen on), keyboard, and mouse. Optionally connect an ethernet cord (if not, make sure you have Wi-Fi running).
3. Follow the first-run wizard.
    1. Once updates have finished in the last step, restart the Pi as directed. (If you don't see a restart prompt or button, type `sudo reboot` in the terminal.)
4. Set the Wi-Fi country code with `sudo raspi-config`. Despite setting it in the first-run wizard, I also had to do this.
    1. Navigate to (using the arrow keys) `Localisation Options` and press the `enter` key.
    2. Navigate to `WLAN Country`.
    3. Navigate to your country. Note that `United States` is under `US` and because the list is alphabetical, you'll have to scroll nearly all the way to the bottom. You can speed that up by using the `page down` or `end` buttons.
    4. Navigate to `Ok` by pressing the right arrow key.
    5. Reboot as requested.

# Personalized setup

The following setup instructions may be irrelevant based on the intended use case of your Pi.

## Install Node.js

1. Install `nvm` from the directions here: https://github.com/nvm-sh/nvm#installing-and-updating
2. Install Node.js via `nvm` with `nvm install --lts`. (See the [`nvm` usage guide](https://github.com/nvm-sh/nvm#usage) for instructions on installing ther Node.js versions.)

## Add another user

If you add the same user as your main personal computer, then you can `ssh` into the pi without specifying a username. For example, if this is not done you must run `ssh pi@<ip-address>` to connect. If your PC's user is `Bob` and you add a Pi user with the name `Bob` then you can connect simply via `ssh <ip-address>`.

1. Add the user: `sudo useradd -m -s "$(which bash)" -G sudo <username>`
    1. This would be `sudo useradd -m -s "$(which bash)" -G sudo Bob` from the example above.
    2. `-m` creates a home directory for this user.
    3. `-s "$(which bash)" sets `bash` as the user's login shell.
    4. `-G sudo` grants the user `sudo` permissions. If this is not wanted, omit these arguments.
2. Set a password for the new user: `passwd <username>`
    1. This would be `sudo passwd Bob` from the example above.

## Disable GUI Autologin

1. `sudo raspi-config`
2. `System Options`
3. `Boot / Auto Login`
4. `Desktop`
5. Exit `raspi-config` and reboot.

## Lock the default `pi` user

The `pi` user is not secure but due to its integration with Raspbian OS it's problematic to remove it entirely so we'll just lock it. Make sure to add another user first so you have another account to login to.

1. `sudo passwd --lock pi`

## Give the Pi a static IP

This will need to be done on your router our your DHCP server. (If you're running a Pi-hole already you can run it as your DHCP server as well.) This will make `ssh`ing into your new Pi much easier.

## Give the static Pi IP a nickname

This only makes sense if your Pi has a static local IP.

1. `sudo nano /etc/hosts`
2. Add an entry for your Pi: `192.168.0.123 name-for-your-pi`

Now you can ssh via `ssh name-for-your-pi`.

## Disable root user

1. If you created a root password, expire / lock it with `sudo passwd -l root`.
2. Prevent root login via `ssh`
    1. `sudo nano /etc/ssh/sshd_config`
    2. Add `PermitRootLogin no`. Note that in the deafult `ssh_config` file, `PermitRootLogin` is already present but commented out. You can leave that line or uncomment it and change the value to `no`, either will work.
    3. `sudo service ssh restart`

## Install [Starship](https://starship.rs)

### Installing a font

Starship suggests installing a [Nerd Font](https://www.nerdfonts.com/font-downloads): fonts with tons of symbol glphys. I suggest downloading Meslo: https://github.com/ryanoasis/nerd-fonts/releases/download/v2.1.0/Meslo.zip. I use `Meslo LG S Regular Nerd Font Complete Windows Compatible` at size `18`.

1. `wget -P ~/Downloads https://github.com/ryanoasis/nerd-fonts/releases/download/v2.1.0/Meslo.zip` to download the font.
2. `cd ~/Downloads`
3. `mkdir Meslo`
4. `unzip Meslo.zip -d Meslo`
5. cd `Meslo`
6. `mkdir -p ~/.local/share/fonts`
7. `cp Meslo\ LG\ S\ Regular\ Nerd\ Font\ Complete\ Windows\ Compatible.ttf ~/.local/share/fonts`
8.  `fc-cache -f -v` to reset the font cache. If you look at the output of this command, you should see something like `.local/share/fonts: caching, new cache contents: 1 fonts, 0 dirs`

### Installing Starship

1. Follow the Linux install instruction (Step 1): https://starship.rs/guide/#step-1-install-starship
2. Follow the Bash setup instruction (Step 2): https://starship.rs/guide/#step-2-setup-your-shell-to-use-starship
4. `source ~/.bashrc` to reload without needing to restart your shell.
5. Create a config file at `~/.config/starship.toml`. I've included my configuration file here: https://raw.githubusercontent.com/electrovir/acquired-knoweldge/master/starship.toml
