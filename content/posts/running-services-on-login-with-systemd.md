+++
title = "Running Services on Login with systemd"
date = "2020-11-10"
description = "systemd is great for managing system services, so why not extend its capabilities to running things on login."
+++

`systemd` is great for managing system services, so why not extend its capabilities to running things on login. Doing this allows you to take advantage of systemd's features like restarting on failure for long-running daemons, or using slices/cgroups for resource managment.

My unit files are simple since I'm running oneshot commands.

Your user units are placed in `~/.config/systemd/user` and are activated with `systemctl --user enable <service>`.

The important part of my unit files is the `graphical-session.target`. This is because I want the command to run when the desktop is ready, and not just as soon as I login.

A template of my unit file is as follows:

```systemd
[Unit]
Description=A 'systemd --user' unit that does something on login.
After=graphical-session.target

[Service]
ExecStart=/usr/bin/program

[Install]
WantedBy=graphical-session.target
```
