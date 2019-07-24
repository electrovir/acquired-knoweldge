# Mac Style Ubuntu

This is a WIP as I'm porting it from my work's Confluence pages that I made.

I'm using an Ubuntu laptop at work but I'm primarily a Mac user. This is my attempt to keep myself sane while constantly switching between the two.

NOTE: I'm doing this in Ubuntu 18.04 with the Ubuntu Gnome desktop environment.

## Swapping modifier key layout

In Tweaks > Keyboard & Mouse > Additional Layout Options there is an option that properly fixes the layout of the left side of the keyboard. However, there isn't an option that allows changing to the right side to the proper layout.

You can add your own option manually (however, as far as I can tell it won't actually show up in this checkbox list even after a restart) as explained here: https://askubuntu.com/a/885047/838097

Note that you will use dconf instead of Tweaks to finish it.

## Keyboard shortcuts

There are additional shortcuts all over the place that must be purged. Most of my text editing happens in VS Code so it can mostly be fixed by modifying the shortcuts in that.

### Revert modifier key remapping for the terminal

In the terminal I want the actual "Ctrl" key to map to Ctrl since Macs use the same ctrl commands, allowing cmd shortcuts to be normal. This will take some tweaks since alt will have to be mapped to ctrl for many shortcuts, but not for others... ugh lol.

See section **Complete keyboard shortcut remapping**.

### Different Home/End shortcuts

On a mac you use `alt+arrow` keys to move across words and `cmd+arrow` keys for home/end. Due to my ctrl/alt swapping, now the equivalent on my machine for going across words is cmd+arrow keys. This is obviously wrong.

## Almost Spotlight-like application search

This includes a "Mission Control" like view as well.

Settings > Devices > Keyboard. Then scroll down to the System section and change "Show all Applications" to Ctrl+Space if you did the above alt/ctrl/super remapping or alt+space if not.

This allows you to hit the shortcut and type an application name to quickly find and launch it. If you press esc after doing this you get the Mission Control view. Esc again to exit.

You can also map a shortcut to "Show the activities overview" to go straight to the mission control like view.

You might have to delete some other shortcuts if you have issues with this. Ubuntu likes having the super/meta/windows key pull up this screen a lot.

## Changing Alt+Tab to Ctrl+Tab

Change Settings > Devices > Keyboard > Navigation > Switch applications to Ctrl+tab

You'll also want to change Navigation > Switch windows of an application to Ctrl+`

## Mac-like theming

You'll need to look up how to install themes elsewhere. Once you get it setup, it's very easy to install new ones (just drag and drop a folder to the right spot).

### Application Theme

McOS-MJV-Dark-mode-<version-number> is my my favorite theme from [McOS-themes](https://www.gnome-look.org/p/1241688/).

### Icon theme: OS X imperssion

To fix the weird "Settings" icon looking like Hal from 2001: A Space Odyssey, go to the theme folder then go to the following path OS X Imperssion/categories/scalable and replace gnome-control-center.svg with a duplicate of applications-system.svg.

### Shell Theme

Majove_NIGHT from the same site as the application theme.

### Mac-like Dock

You only need the Gnome extension Dash to Dock. In the extension settings you can change the location on the screen of the dock, make it autohide, and make it show up on every screen like it does on a Mac.

Finally I can copy any Mac keyboard shortcuts that I want! Application specific keyboard shortcut mapping is possible, as well as arbitrary script execution!

# Complete keyboard remapping

## Install the thing

for gnome, the default window manager / desktop environment / idk what it actually is but it's Gnome

sudo apt install autokeys-gtk

## Open the thing

Open the activities or Applications overview or whatever it's called (if you map my Mac keyboard shortcuts like in my other post then that'll be ctrl (the alt key) + space). Don't try to open it from the command line (this didn't work for me).

Note: Open Edit > Preferences > Special Hotkeys and clear the preset hotkeys (if you desire) to prevent clashing with other shortcuts you use (I ran into this in VS Code as I have meta + shift + k mapped to delete lines).
Add the thing to startup applications

Open gnome-tweaks (might need to install with apt) and add AutoKey as a startup application.
Making Scripts

    Create a new folder
        A good place to put it is in ~/.config/autokey/data/
    Select your new folder in the autokey gui and add a new script
    Go crazy writing any script you want

        To send a keyboard shortcut:

        keyboard.send_keys('<ctrl>+<page_down>')


    To set a keyboard shortcut to the script click "Set" next to "Hotkey:"
    Set a window filter (see the same image above)
        I usually leave the setting to match the whole application

## Freezing

I've encountered or made errors in autokey with it freezing or not firing my scripts sometimes. When this happens run sudo killall autokey-gtk in the terminal and then reopen autokey and it'll work again (you can close the window afterwards and it'll keep running the script executor).

## My examples

### Skip to end of line with arrow key

Easily duplicated for beginning of line with left arrow.

Hotkey: `<ctrl>+<right>`

```
keyboard.send_keys('<end>')
```

### Skip each word with Alt

Hotkey: `<alt>+<right>`

```
keyboard.send_keys('<ctrl>+<right>')
```

### Next tab in browsers:

Hokey: `<ctrl>+<shift>+]`

Window Filter: (Navigator.Firefox|google-chrome.Google-chrome)

```
keyboard.send_keys('<ctrl>+<page_down>')
```

From this a "previous tab in browsers" can easily be created

### Paste in Terminal

Include scripts like this as well for copying

Hotkey: `<ctrl>+v`

Window Filter: gnome-terminal-server.Gnome-terminal

```
keyboard.send_keys('<ctrl>+<shift>+v')
```

Make sure to include the following as well:

Hotkey: `<super>+v`

Window Filter: gnome-terminal-server.Gnome-terminal

```
keyboard.send_keys('<ctrl>+v')
```

## Guides

There are some useful but very simple scripts included with the app under "Sample Scripts".

More detailed guides:

https://github.com/autokey/autokey/wiki/Scripting

https://github.com/autokey/autokey/wiki/Special-Keys
