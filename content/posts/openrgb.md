+++
title = "OpenRGB"
date = "2020-11-16"
description = "Does your computer have RGB? Time to give this piece of software a try!"
+++

[OpenRGB](https://gitlab.com/CalcProgrammer1/OpenRGB) is amazing open source software, that supports Windows and Linux, for managing your computers RGB lighting.
It doesn't depend on the software of manufacturers, and allows you to control the RGB lighting where previously you might not have before on Linux.

![OpenRGB Screenshot](/images/Screenshot-OpenRGB.png#center)

OpenRGB gives you the ability to save and load profiles. This can be done in the GUI or through the CLI using `openrgb --profile filename.orp`.

You can also programmatically configure devices on the CLI. I use this since I just want to set every device to red on startup. I do this as explained in [Running Services on Login with systemd](../running-services-on-login-with-systemd/). The complete `openrgb.service` is as follows:

```systemd
[Unit]
Description=Load OpenRGB profile
After=graphical-session.target

[Service]
ExecStart=/usr/bin/openrgb --color "FF0000"

[Install]
WantedBy=graphical-session.target
```

# Installing

| Arch Linux  | Fedora  | Ubuntu |
| :---------: | :-----: | :----: |
| aur/openrgb | openrgb | **NA** |
