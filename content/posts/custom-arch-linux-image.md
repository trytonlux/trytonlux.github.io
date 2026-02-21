+++
title = "Custom Arch Linux Image"
date = "2020-04-17"
description = "Creating my own Arch Live image with the GNOME desktop and more."
+++

One of the tools I like to keep on my toolbelt is a USB drive with some kind of live Linux system. This can be used to show someone Linux, troubleshooting peoples devices, or just arch-chroot'ing into my desktop to fix something. Since Arch Linux is my OS of choice, it would be ideal to have an Arch USB. But, this obviously means not having a Desktop Environment to work from. I could use a Fedora Workstation live USB, but it would be nice to have my own tweaks on top. So I decided to give customizing [Archiso](https://wiki.archlinux.org/index.php/Archiso) a try.

Following the instructions on the Archiso wiki page, after installing the `archiso` package, you can copy the `/usr/share/archiso/configs/releng` folder to get a base to work from. I'm going to go over the various tweaks I made.

# build.sh

The releng profile has a `build.sh` script that will build you an iso file. I modified the script to create a loopback image with an EFI System Partition and copy the nessacary files to that. This removes the dependecy of syslinux/isolinux and makes the image simplier. This results in a img file instead of an ISO, and it won't boot on legacy BIOS systems. But that's not an issue for me.

I also cleaned up the script, making sure it passed [ShellCheck](https://github.com/koalaman/shellcheck).

# Netboot

Upon booting, you can press the **Space** key to trigger the boot menu and choose [NETBOOT.XYZ](https://netboot.xyz/). As long as there is a network connection, this one boot USB can be used to try out other Distros and Desktop Environments.

# Packages

By editing `packages.x86_64`, you can change what packages are installed on the live system image. I stripped plenty of packages I don't need; things like utilites for connecting to various VPNs or dial-up networks.

I added the GNOME Desktop with some basics:

- File Roller
- GNOME Calculator
- GNOME Screenshot
- GNOME System Monitor
- GNOME Terminal
- Nautilus
- Gedit
- Evince

GNOME Disk Utility and GParted are included for disk managment.

Some CLI tools are included:

- [inxi](https://github.com/smxi/inxi) --- System information tool.
- [starship](https://github.com/starship/starship) --- Shell prompt.
- [fish](https://github.com/fish-shell/fish-shell) --- The user-friendly command line shell.
- [bat](https://github.com/sharkdp/bat) --- A cat(1) clone with wings.

# Root Filesystem

Various tweaks are made in the usual way of editing system files. The `airootfs` folder acts as an overlay and you can make these customizations here.

> [!TIP]
> Any administrative task that you would do while following the installation guide (or after installation) can be scripted by editing `airootfs/root/customize_airootfs.sh`, except for package installation. The script is written from the perspective of the running live system, i.e. in the script the path / refers to the root of the running live system.

In `customize_airootfs.sh`, I do things like adding the live user, enabling systemd units, and sed'ing configs.

# Firefox

For Firefox tweaks, I created two files.

`airootfs/usr/lib/firefox/defaults/pref/local-settings.js`:

```js
pref("general.config.obscure_value", 0);
pref("general.config.filename", "mozilla.cfg");
```

And then the actual configuration `airootfs/usr/lib/firefox/mozilla.cfg`:

```js
//
// ...my settings...
// For example, setting dark mode
pref("extensions.activeThemeID", "firefox-compact-dark@mozilla.org");
```

The first line must be exactly "//". The syntax of the file is similar to `user.js`. I've also included the uBlock Origin extension by default.

> [!NOTE]
> Arch Wiki page for reference: <https://wiki.archlinux.org/index.php/Firefox#Configuration>

# Dconf

Similar to Firefox, two files are created.

The user profile `airootfs/etc/dconf/profile/user`:

```txt
user-db:user
system-db:local
```

And the keyfile for the local database `airootfs/etc/dconf/db/local.d/00-defaults`:

```ini
[org/gnome/desktop/interface]
gtk-theme="Adwaita-dark"
```

You can use `dconf-editor` to find the keys you want to include in this file.

I also run `dconf update` in `airootfs/root/customize_airootfs.sh` to update the system databases.

> [!NOTE]
> GNOME docs for reference: <https://help.gnome.org/admin/system-admin-guide/stable/dconf-custom-defaults.html>

# Dotfiles

In `airootfs/etc/skel`, I've included the files to be placed into the live users home upon creating. These include the configs for Fish and the starship prompt, .desktop files to hide some applications in the menu, and binaries in `~/.local/bin/` for `inxi` and `starship` since they aren't in the Arch repos.

# Misc

A couple other minor tweaks are things like configuring GDM to autologin. Everything is a dark theme by default, and I'm using the [Dracula](https://draculatheme.com/) theme throughout the system. And, the locale is set to `en_CA.UTF-8`, with the timezone set to `/usr/share/zoneinfo/EST5EDT`.

# Boot USB with Data Partition

I plan to use this with a 64GB USB drive. Of course, this is much bigger than the ~2GB image.

Instead of writing the img file to the USB drive, I partition the drive like this:

- A 2GB FAT32 parition
- An EXT4 parition that uses 100% of the space left

And then I mount the img file and copy the files to the ESP. With this, I have my bootable Arch USB with space for shortlived files for transfering, or copy files off a device I'm booted into.

I look forward to putting this USB drive to use and will be tweaking my custom image as I come across any new additons I wish to make.

The source can be found [here](https://github.com/tryton-vanmeer/archlinux) if you want more detail.
