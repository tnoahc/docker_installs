# docker_installs
This script will help install any, or all, of Docker-CE, Docker-Compose, NGinX Proxy Manager, Dockhand, Navidrome, Remotely, and Guacamole.

## Reason for Making this Script
This script was created by Brian McGonagill and copied from his gitlab https://gitlab.com/bmcgonag/docker_installs because I found it very usefull and beneficial, so all credit goes to him for the awesome work done.

## Using this script

1. Clone the repo
`git clone https://github.com/tnoahc/docker_installs.git`

, or copy / paste the code from the `install_docker.sh` file into a file on your server. 

`nano install_docker.sh`

to open a text editor in the terminal, then use CTRL + Shift + V to paste into it.

Save with CTRL + O, then Enter to confirm, and exit the nano editor with CTRL + X.

2. Change the permissions of the .sh file to make it executable with.

`chmod +x install_docker.sh`

3. Run the installer with

`./install_docker.sh`

## Prompts from the script:
First, you'll be prompted to select the number for your OS / Distro.  Currently I support RaspbianOS (latest), CentOS 7 and 8, Debian 10 and 11, Ubuntu 18.04, 20.04, 22.04, 23.05, Arch Linux, and Open Suse (tested on Leap 15.4). 

Next, you'll be asked to answer "y" to any of the software packages you'd like to install. 
- Docker-CE (you'll need this for the others to work)
- Docker-Compose (you'll need this for any of the applications to start properly)
- NGinx Proxy Manager
- Navidrome (music player)
- Dockhand (Docker management GUI, on port 3000)
- Remotely (Remote Desktop Support Tool)
- Guacamole (Remote Desktop Protocol in the Browser)

Answering "n" to any of them will cause them to be skipped.

### NOTE
* You must have Docker-CE (or some version of Docker) installed in order to run any of the other packages.
* You must have Docker-Compose installed in order to run NGinX Proxy Manager, Dockhand, Navidrome, Remotely, or Guacamole with this script.

Before prompting to install Docker or Docker-Compose, I do try to see if you already have them installed, and I skip the prompt if you do (or I try to anyway).

If you want to run `docker` without `sudo` after installing, add your user to the docker group, then log out and back in:

`sudo usermod -aG docker $USER`
