+++
title = "Shells on the Beach 🦀 🏖️ 🐟"
date = "2021-01-13"
description = "A software spotlight of some of my terminal tools."
+++

Here are some of the terminal tools I use, some of which share a certain ocean theme.

# [Tilix](https://gnunn1.github.io/tilix-web/)

Appropriately, the first item on the list is the terminal I use. Like GNOME's Terminal, it is GTK-based and uses the VTE library. Tilix had a nice CSD headerbar, in accordance to the GNOME HIG, before GNOME Terminal implemented one. Along with more options for tweaking, Tilix has tiling support built in with vertical and horizontal panes.

![Tilix Screenshot](/images/Shells-on-the-Beach-Tilix.png#center)

# [Fish Shell](https://fishshell.com/)

I was finally trying ZSH and had configured it to my liking. Then I decided to finally try Fish; a lot of the features I just configured ZSH to do were available in Fish out-of-the-box. So Fish has been my shell of choice since then.

Fish keeps all of its files in `~/.config/fish`, with no such file like `.bashrc` in `~`. This helps keep `~` clean of dotfiles which I appreciate. I also love the `conf.d` and `functions` folders within `~/.config/fish` that allow for modularity.

Fish uses functions instead of aliases (The [alias](https://fishshell.com/docs/current/cmds/alias.html) command is a wrapper for the function builtin), and a neat feature of Fish is autoloading functions.

> [!TIP]
> When fish encounters a command, it attempts to autoload a function for that command, by looking for a file with the name of that command in `~/.config/fish/functions`

So, I have a file for each of my functions or "aliases".

A simple example is my `cdg` function:

```fish
function cdg -d "Change directory to root of GIT repo"
    cd (git rev-parse --show-toplevel)
end
```

And something more complex, `podrun`:

```fish
function podrun -d "Run container interactively and delete after"
    if count $argv > /dev/null
        if test $argv[2]
            podman run -it --rm $argv[1]:$argv[2]
        else
            podman run -it --rm $argv[1]
        end
    else
        echo "podrun IMAGE <TAG>"
    end
end
```

Which also has its own completion file, `~/.config/fish/completions/podrun.fish`:

```fish
se base-devel

# First argument: DISTRO
complete -f -c podrun -n "not __fish_seen_subcommand_from $images" -a "$images"

# Optional second argument for distro version.
complete -f -c podrun -n "__fish_seen_subcommand_from fedora" -a "$fedora"
complete -f -c podrun -n "__fish_seen_subcommand_from ubuntu" -a "$ubuntu"
complete -f -c podrun -n "__fish_seen_subcommand_from centos " -a "$centos"
complete -f -c podrun -n "__fish_seen_subcommand_from archlinux" -a "$archlinux"
```

# [Starship](https://starship.rs/)

Long before Fish, I played with building my own shell prompt for Bash — both in Bash itself and Python. It was something fun to build, and I liked the idea of modular powerline-type prompts, but prefered the simple look of just text, without the coloured background connecting blocks.

Eventually I found the Starship project. It was really fast, has modular configuration, and the out-of-the-box design was exaclty what I liked: simple text without background colors and a newline between prompt and user input.

![Starship Screenshot](/images/Shells-on-the-Beach-Starship.png#border#center)

# [exa](https://the.exa.website/)

`exa` is a modern replacement for `ls`. `exa` has out-of-the-box support for distinguishing file types with colour and it has other neat features like Git support and icons, which I use in my `la` alias (table ouput using `--long` option).

![exa Screenshot](/images/Shells-on-the-Beach-exa.png#border#center)

# [pydf](https://github.com/k4rtik/pydf-pypi)

`pydf` is a `df` clone written in Python. I like the output it gives with ascii bar graphs and that certain filesystems are ignored by default (/dev, /run, tmpfs).

An example of the output:

```sh
Filesystem      Size Used Avail Use%                                         Mounted on
/dev/nvme0n1p2  100G 60G    40G 60.0 [#######################..............] /
```

# [Bat](https://github.com/sharkdp/bat)

I use `bat` as a replacment for `less` to provide syntaxt highlighting to files, and its Git support is nice.

![Bat Screenshot](/images/Shells-on-the-Beach-Bat.png#border#center)

# [micro](https://micro-editor.github.io/)

As a long term user of `nano` for simple file edits in the terminal, `micro` is that and more. `micro` has intuitive shortcuts, like **CTRL+S** for saving. Syntax highlighting seems better than `nano` in my experience, and has more supported languages out-of-the-box. One of my favourite featues is that when saving a root-owned file, `micro` prompts you to save using `sudo`. This means never having to run `micro` as root, which is just more secure overall.

# Conclusion

There you have it, many of the CLI tools I use. Most of which are replacements for the usual coreutils.

Did you figure out what the ocean theme was? The **Fish** shell, is an obvious one, but many of the tools like `starship`, `exa`, and `bat` are written in Rust.

# Installing

| Package  | Fedora | Arch Linux | Ubuntu |
| -------- | ------ | ---------- | ------ |
| tilix    | ✓      | ✓          | ✓      |
| fish     | ✓      | ✓          | ✓      |
| starship | ✓      | ✓          | x      |
| exa      | ✓      | ✓          | ✓      |
| pydf     | ✓      | ✓          | ✓      |
| bat      | ✓      | ✓          | ✓      |
| micro    | ✓      | ✓          | ✓      |
