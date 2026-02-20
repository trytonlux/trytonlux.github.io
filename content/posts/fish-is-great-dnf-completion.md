+++
title = "Fish is Awesome! And tweaking the DNF completion."
date = "2020-03-27"
description = "I have really been enjoying fish as my default shell. And I finally got around to tweaking the one annoyance I had with Fedora..."
+++

A while ago I decided to finally switched to ZSH and give it a try. Got it mostly configured to how I wanted it and was satisfied.

Then a friend mentioned he uses Fish. Well, I'm already trying an alternative shell, so why not give Fish a go.

Fish. Is. Awesome.

I've heard about how great Fish and ZSH were but always stuck to just using Bash. I was now thinking to myself, why didn't I try these earlier.

As great as ZSH was, I really like Fish's out of the box defaults. I love that I can keep all Fish configs in `~/.config/fish/` and keep `$HOME` clutter free.

In the process of trying these new shells I also depreciated my custom bash powered prompt and starting using [Starship](https://starship.rs/). Starship gave me the style I wanted in my prompt, but so much faster.

# DNF Completion

Recently I've been giving Fedora a try and quite enjoy it. The only real complaint I have had is how package names are displayed in some cases when using DNF.

For example when searching:

```shell
$ dnf search dconf
===== Name Exactly Matched: dconf =====
dconf.x86_64 : A configuration system
dconf.i686 : A configuration system
===== Name & Summary Matched: dconf
dconf-editor.x86_64 : Configuration editor for dconf
dconf-devel.i686 : Header files and libraries for dconf development
dconf-devel.x86_64 : Header files and libraries for dconf development
```

And when tab completing with Fish:

```shell
$ dnf install dconf<TAB><TAB>
dconf-0.36.0-1.fc32.i686        dconf-devel-0.36.0-1.fc32.x86_64
dconf-0.36.0-1.fc32.x86_64      dconf-editor-3.36.0-1.fc32.x86_64
dconf-devel-0.36.0-1.fc32.i686
```

Of course this doesn't break any functionality and is only aesthetic. But as an Arch Linux user it does hurt me on a minimalist level. I don't need to see metadata like the Fedora version or even the version of the package. I especially don't need to see that I'm installing an x86_64 package on my 64bit install of Linux.

So I looked into the fish completion for DNF. And here is the relevent part:

```fish
function __dnf_list_available_packages
    if type -q sqlite3
        sqlite3 /var/cache/dnf/packages.db "SELECT pkg FROM available WHERE pkg LIKE \"$cur%\"" 2>/dev/null
    end
end
```

The ZSH completion is similar and it makes sense. The SQL query is really fast. But it returns the undesired results above.

As part of looking into a solution I came across the [string](https://fishshell.com/docs/current/cmds/string.html) command. This was another awesome feature of Fish and I realized how useful Fish could be for scripting without having to resort to Python for advanced string manipulation.

Now the command can be:

```fish
sqlite3 /var/cache/dnf/packages.db "SELECT pkg FROM available WHERE pkg LIKE \"$cur%\"" 2>/dev/null | \
string replace -r ".fc.*" "" | string replace -r "\-[0-9].*" ""
```

In both cases we use the replace regex subcommand. First to remove the fedora release and architecture of the package. Then everything after the first instance of a dash (-) followed by a digit ([0-9]).

Which results in a more minimal tab completion:

```shell
$ dnf install dconf<TAB><TAB>
dconf  dconf-devel  dconf-editor
```

This is an issue for packages such as `gnome-2048`, but I'm not to concerned.

I am, however open to suggestions that can overcome this issue and properly separate the version from the package name.
