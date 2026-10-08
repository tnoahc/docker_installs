# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A single interactive Bash installer, `install_docker.sh`, that sets up Docker-CE, Docker-Compose and optional self-hosted apps (NGinX Proxy Manager, Dockhand, Navidrome, Remotely, Guacamole) on a Linux server. It is based on Brian McGonagill's https://gitlab.com/bmcgonag/docker_installs; credit stays with him (see README).

There is no build, package manager or test suite. The script targets Linux servers and is run with `sudo` access; it cannot be meaningfully executed on the Windows dev machine.

## Checking changes

- Syntax check: `bash -n install_docker.sh`
- Lint (if installed): `shellcheck install_docker.sh`
- A real run needs a disposable Linux VM/container for the target distro, since it upgrades the system, installs packages, adds the user to the `docker` group and starts containers.

## How the script is structured

Control flow is three stages, all in one file:

1. **Top-level `select` menu** (bottom of the file) asks for the OS. The chosen menu number is stored in `$REPLY` and copied to `$OS` inside `installApps`. Every distro branch later in the script keys off these numbers:
   `1` CentOS/Fedora, `2` Debian, `3` Ubuntu 18.04, `4` Ubuntu 20.04+, `5` Arch, `6` openSUSE, `7` Arm64/Raspbian, `8` exit.
   Adding or reordering a menu entry means updating every `[[ "$OS" == ... ]]` check.
2. **`installApps`** detects whether Docker is active (`$ISACT`) and whether `docker-compose` exists (`$ISCOMP`), skipping those prompts if so, then collects y/n answers into `DOCK`, `DCOMP`, `NPM`, `NAVID`, `DOCKHAND`, `REMOTELY`, `GUAC`.
3. **`startInstall`** runs, in order: per-distro system update + prerequisites + Docker install → add user to `docker` group → per-distro Docker-Compose install (standalone binary from GitHub releases on Debian/Ubuntu/CentOS/openSUSE, `pip3` on Raspbian, `pacman` on Arch) → wait for the Docker service → `docker network create my-main-net` → one block per app.

Each app block follows the same pattern: `mkdir -p ~/docker/<app>`, put a `docker-compose.yml` there, then `docker-compose up -d` (without `sudo` on CentOS, with `sudo` elsewhere). The compose file comes from one of two places:

- **Dockhand** writes its compose file inline with a heredoc (port 3000, mounts `/var/run/docker.sock`, joins `my-main-net` as an external network).
- **NPM, Navidrome, Remotely, Guacamole** `curl` theirs from the **upstream GitLab repo** (`https://gitlab.com/bmcgonag/docker_installs/-/raw/main/docker_compose_*.yml`). Those files are not stored in this repo, so changing their config means switching to an inline heredoc like Dockhand's or changing the URL.

New apps should join the `my-main-net` network so NGinX Proxy Manager can reach them by container name.

Command output is redirected to `~/docker-script-install.log`. Long-running steps run in the background (`... &` then `pid=$!`) with a `kill -0 $pid` spinner loop, which is copy-pasted throughout rather than factored into a function.

## Known quirks in the current script

- openSUSE installs then removes `docker-compose` instead of installing `docker`, and its update log is written relative to the current directory, not `~`.
- The script ends with `exit 1` even on success.
