# Linux Fresh Install Setup Record

This document captures the main setup activities from the command history. It is grouped by task so you can follow the same steps again on a fresh install.

Firstly,
- Fix scaling in (right click @destop) > Display
- Switch Fn and R.Ctrl in BIOS (Already done)
- Log on into mozilla for sync 
- Add fingerprint in GNOME Settings (Directly)
- For emoji, Use Super+. (Period)
    - type search terms and hit space and then enter 😀
## Update the system

```bash
sudo apt upgrade
```

## Install GNOME utilities and tweaks

```bash
sudo dnf install gnome-extensions-app
sudo dnf install gnome-tweaks
```


### Icon Set
```bash
sudo dnf install papirus-icon-theme
```

## Configure Bluetooth
Disable auto power off

```bash
printf "options btusb enable_autosuspend=n\n" > /etc/modprobe.d/btusb.conf
```


## Install Flatpack
```bash
sudo flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

## Install Applications:
- [Dash to Panel](extensions.gnome.org/extension/1160/dash-to-panel/)
    - Then size the panel as needed.
    - Edit icon paddings etc.
- [Seahorse](https://flathub.org/en/apps/org.gnome.seahorse.Application)
    - Then remove keyring password as its uncomfortable to login into keyring on every logon
- [Lollypop Music Player](https://flathub.org/en/apps/org.gnome.Lollypop)
- [VS Code](https://code.visualstudio.com/download)
    - Fix the zoom (Settings > window.zoomLevel)
- [Extension Manager](https://flathub.org/en/apps/com.mattjakeman.ExtensionManager)
- [Copious](https://extensions.gnome.org/extension/8834/copyous/)
    - Edit settings in `Extension Manager`>`Installed Tab`>`Copyous`> ⚙️
        - `Customization` > `Profiles` = `Compact`
- For Emoji
    - [Smile](https://flathub.org/en/apps/it.mijorus.smile)
    - [Smile Gnome Connector](https://extensions.gnome.org/extension/6096/smile-complementary-extension/)
    - View and remove internal emoji-picker shortcut from gnome:
        ```bash
        ravi@thinkpad:~$ gsettings get org.freedesktop.ibus.panel.emoji hotkey
        ['<Super>period', '<Super>semicolon']
        ravi@thinkpad:~$ gsettings set org.freedesktop.ibus.panel.emoji hotkey "[]"
        ravi@thinkpad:~$ gsettings get org.freedesktop.ibus.panel.emoji hotkey
        @as []
        ```
    - Add Keyboard shortcut in gnome:<br/>
        Shortcut: `Super` + `;`<br/>
        Command: 
        ```
        flatpak run it.mijorus.smile
        ```
    - In `Smile` > `Preferences` > `Run in background`

- [Passwords & Keys (seahorse)](https://flathub.org/en/apps/org.gnome.seahorse.Application)
    - remove keyring password as we wont store any sensitive passwords there.
    - firefox can have a password manager if needed.
    - GNOME does not support keyring fingerprint unlock
- [ONLYOFFICE](https://flathub.org/en/apps/org.onlyoffice.desktopeditors)
- [Fragments - Torrents](https://flathub.org/en/apps/de.haeckerfelix.Fragments)