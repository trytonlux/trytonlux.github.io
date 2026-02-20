+++
title = "Arch Linux with GNOME on the Surface Pro 3"
date = "2020-03-30"
description = "I recently acquired a SP3 and here's my experience with using Linux on it."
+++

Recently, I acquired a Surface Pro 3 for a great deal. I was looking for a small computing device and wanted something I could run desktop Linux on, so an Android tablet or Chromebook were out of the race already.

I settled on the SP3 for a couple reasons:

- It has x86 hardware so no issues installing Linux.
- The SP3, while quite old at this point (released in 2014), has out of the box Linux support.
  - Despite being an Arch Linux user, I am still lazy. I wanted everything to work without custom kernels.
- I got it used for a really good deal.

So far it has been completely usable and really handy as my companion device when playing D&D.

These are some notes and tweaks based on running Arch Linux on the SP3 with GNOME as the desktop environment.

## Display

A handy feature since GNOME 3.36 (albeit experimental and only in Wayland) is fractional scaling.

It can easily be enabled with the following command:

```fish
gsettings set org.gnome.mutter experimental-features "['scale-monitor-framebuffer']"
```

For the 2160x1440 screen on the SP3, I personally find a 125% scaling really nice.

## Bluetooth

If you want bluetooth available on boot, enable it, since it may not be already.

```shell
systemctl enable --now bluetooth
```

## Autorotation

Only a single package needs to be installed to enable screen rotation when the surface is rotated.

```shell
pacman -S iio-sensor-proxy
```

GNOME will now display a button for (un)locking screen rotation.

![SP3 Screen Rotate Lock](/images/Screenshot-SP3-Screen-Rotate-Lock.png)

# Touchscreen

GNOME on Wayland works really well with a touchscreen. And the GNOME apps handle touch input wonderfully.

I set two enviroment variables for Firefox:

- MOZ_ENABLE_WAYLAND=1
- MOZ_USE_XINPUT2=1

Firefox's touch support is still finicky, so for most of my browsing I use GNOME Web.

GNOME Web still has a way to go as a browser, but there's a lot to like about it. Especially the touch gestures.

# Stylus

While I'm no artists, I have a surface pen and it works with no issue.

I've played around with [MyPaint](http://mypaint.org/) and the pen works as expected, specifically erasing when flipped around.
