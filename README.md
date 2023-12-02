# nixos-hetzner-cloud-starter

This repository is intended to be a sane, batteries-included starter template
for running a LunarVim-powered remote NixOS development environment on Hetzner
Cloud. It exists as a sister-project of
[nixos-wsl-starter](https://github.com/lgug2z/nixos-wsl-starter).

If you don't want to dig into NixOS too much right now, the only file you need
to concern yourself with is [home.nix](home.nix). This is where you can add and
remove binaries to your global `$PATH`.

Go to [https://search.nixos.org](https://search.nixos.org/packages) to find the
correct package names, though usually they will be what you expect them to be
in other package managers.

`unstable-packages` is for packages that you want to always keep at the latest
released versions, and `stable-packages` is for packages that you want to track
with the current release of NixOS (currently 23.11).

If you want to update the versions of the available `unstable-packages`, run
`nix flake update` to pull the latest version of the Nixpkgs repository and
then apply the changes.

Make sure to look at all the `FIXME` notices in the various files which are
intended to direct you to places where you may want to make configuration
tweaks.

If you found this starter template useful, please consider
[sponsoring](https://github.com/sponsors/LGUG2Z) and [subscribing to my YouTube
channel](https://www.youtube.com/channel/UCeai3-do-9O4MNy9_xjO6mg?sub_confirmation=1).

## What Is Included

This starter is a lightly-opinionated take on a productive terminal-driven
development environment based on my own preferences. However, it is trivial to
customize to your liking both by removing and adding tools that you prefer.

* The default editor is `lvim`
* The default shell is `zsh`
* `docker` is enabled by default
* The prompt is [Starship](https://starship.rs/)
* [`fzf`](https://github.com/junegunn/fzf),
  [`lsd`](https://github.com/lsd-rs/lsd),
  [`zoxide`](https://github.com/ajeetdsouza/zoxide), and
  [`broot`](https://github.com/Canop/broot) are integrated into `zsh` by
  default
    * These can all be disabled easily by setting `enable = false` in
      [home.nix](home.nix), or just removing the lines all together
* [`direnv`](https://github.com/direnv/direnv) is integrated into `zsh` by
  default
* `git` config is generated in [home.nix](home.nix) with options provided to
  enable private HTTPS clones with secret tokens
* `zsh` config is generated in [home.nix](home.nix) and includes git aliases,
  useful WSL aliases, and
  [sensible`$WORDCHARS`](https://lgug2z.com/articles/sensible-wordchars-for-most-developers/)

## Quickstart

[![Watch the walkthrough video](https://img.youtube.com/vi/wr22CyoyRo4/hqdefault.jpg)](https://www.youtube.com/watch?v=wr22CyoyRo4)


* Create a server on Hetzner Cloud
    * Location: Any
    * Image: Ubuntu 22.04
    * Type: Any (Shared vCPU `CPX11` is a good starting point)
    * SSH: Use your "default" SSH public key for ease
    * Architecture: x86
    * Volumes: Not required
    * Firewalls: Not required
    * Backups: Not required
    * Placement Groups: Not required
    * Labels: Not required
    * Cloud Config: Leave blank
    * Name: Any
* `cp ~/.ssh/id_rsa.pub .` to make sure you have your SSH public key in this
  repo
* Go through all the `FIXME:` notices in this repo and make changes wherever
  you want
* Run [`nixos-anywhere`](https://github.com/nix-community/nixos-anywhere)
  against `root@<server-ip-address>`
```bash
nix run github:numtide/nixos-anywhere -- --flake .#nixos root@<server-ip-address>
```
* Wait for the installation to complete
* Try to SSH into the server with `ssh <your-username-selected-in-flake.nix>@<server-ip-address>`
* You'll probably receive an error like the one below; follow the steps to remove the ip address from `known_hosts`
```
  @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ED25519 key sent by the remote host is
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX.
Please contact your system administrator.
Add correct host key in ~/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in ~/.ssh/known_hosts:6
  remove with:
  ssh-keygen -f ~/.ssh/known_hosts" -R "<ip addrress>"
Host key for <ip_address> has changed and you have requested strict checking.
Host key verification failed.
```
* Now you can SSH into the server
* In a local terminal window, apply updated configurations to the remote server
```bash
nixos-rebuild switch --flake .#nixos --target-host root@<server-ip-address>
```

Note: If developing in Rust, you'll still be managing your toolchains and components like `rust-analyzer` with `rustup`!

## Project Layout

In order to keep the template as approachable as possible for new NixOS users,
this project uses a flat layout without any nesting or modularization.

* `flake.nix` is where dependencies are specified
    * `nixpkgs` is the current release of NixOS
    * `nixpkgs-unstable` is the current trunk branch of NixOS (ie. all the
      latest packages)
    * `home-manager` is used to manage everything related to your home
      directory (dotfiles etc.)
    * `nur` is the community-maintained [Nix User
      Repositories](https://nur.nix-community.org/) for packages that may not
      be available in the NixOS repository
    * `nix-index-database` tells you how to install a package when you run a
      command which requires a binary not in the `$PATH`
    * `disko` is used to prepare VM storage for NixOS
* `hetzner.nix` is where OpenSSH is configured and where the `root` SSH public
  key is set
* `linux.nix` is where the VM is configured
    * The hostname is set here
    * The default shell is set here
    * User groups are set here
    * NixOS options are set here
* `home.nix` is where packages, dotfiles, terminal tools, environment variables
  and aliases are configured
