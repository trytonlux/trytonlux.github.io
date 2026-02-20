+++
title = "YubiKey"
date = "2020-12-18"
description = "Notes about YubiKey and setting up on Linux."
+++

Notes about setting up a YubiKey on Linux (Arch Linux / Fedora).

## Packages

Install the following packages:

### Arch Linux

```bash
$ sudo pacman -S pam-u2f yubikey-manager libfido2
```
<!--
<note tip>
Make sure `pcscd.service` (PC/SC Smart Card Daemon) is enabled on Arch Linux.
</note> -->

### Fedora

```bash
$ sudo dnf install pam-u2f yubikey-manager libfido2 pamu2fcfg
```

## PAM

Create `/etc/u2f_keys` and append the output of `pamu2fcfg` to it.

`pamu2fcfg -n` will create the same output without a username. This is usefull for appending additional keys.

Add the line `auth sufficient pam_u2f.so authfile=/etc/u2f_keys cue` to the top of any PAM config file in `/etc/pam.d`. Such as `sudo`, `gdm-password`, `polkit-1`.

This adds the YubiKey as an auth method. Sufficient means that only the key is needed. If the key is removed, password auth will be used as normal.

## SSH

Create an SSH key like normal, but specify the `ecdsa-sk` key type (sk stands for security key).

```bash
$ ssh-keygen -t ecdsa-sk
```

Then add the key to the remote machine as usual.

## Disable OTP on Touch

When the YubiKey is touched, it acts as a keyboard outputing an OPT and sending [ENTER].

To disable it:

```bash
$ ykman config usb --disable otp
```
