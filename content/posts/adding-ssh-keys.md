+++
title = "Adding SSH Keys"
date = "2020-12-18"
description = "Notes about adding SSH keys (and using Git[Hub|Lab] for easy provisioning)."
+++

Just some notes about provisioning machines with SSH keys.

# Copy with ssh-copy-id

If you have ssh access to the machine with a password, you can use `ssh-copy-id`.

```bash
$ ssh-copy-id user@host
# or to specify the key
$ ssh-copy-id -i ~/.ssh/mykey user@host
```

# Using Git Platforms

Both GitHub and GitLab allow you to grab a users public SSH keys.

You can grab them like so:

```bash
$ curl https://github.com/<username>.keys
$ curl https://gitlab.com/<username>.keys

# And I would:
$ curl https://github.com/tryton-vanmeer.keys
$ curl https://gitlab.com/tryton-vanmeer.keys
```

And to add the keys to a machine you have access to:

```bash
curl https://github.com/tryton-vanmeer.keys | tee -a ~/.ssh/authorized_keys
```

You would now be able to SSH into that machine using any machine that has SSH access to your Git platforms.

# Permissions

If the SSH folder had to be created for a user, make sure the permissions are correctly set for the folder itself,
and the authorized_keys file.

```bash
$ chmod 700 ~/.ssh
$ chmod 600 ~/.ssh/authorized_keys
```
