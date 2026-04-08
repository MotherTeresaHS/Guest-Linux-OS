# Guest Linux OS

The follwoing are the instructions to create a Linux computer that students can log into as a "Guest", where the home directory is just mounted to a RAM disk. This way as each new student logs in, the "Guest" account is completly fresh and has no saved data. But the students still have access to all the locally loaded programs.

## Install base OS

- for this installation I am using Debian 13, Trixie
  - downlaod the latest ISO and burn it to a USB
  - go through the standard install
    - ensure you pick "gnome" desktop and "ssh server"  would also be helpful 
  - for the user create:
    - login: guest
  - remember that the "guest" user by default does not have "sudo" privileges and that is what we what, DO NOT give guest "sudo" access

## Mount /home/guest to RAM

- to make the "guest" user home directory in RAM we will use "tmpfs"
  - login as "root"
  -  in /etc/fstab, add the following to the end:
    tmpfs  /home/guest  tmpfs  defaults,size=1G,mode=0700,uid=guest,gid=guest  0  0
  - Since the RAM disk is wiped every reboot, the guest user won't have a .bashrc or desktop folders. You can use systemd-tmpfiles to automatically copy default files from /etc/skel every time the machine boots.
    -  nano /etc/tmpfiles.d/guest-home.conf
      C  /home/guest  -  -  -  -  /etc/skel
      Z  /home/guest  -  guest  guest  -  -
  - Clear out any existing physical files in the guest home to avoid conflicts:
    rm -rf /home/guest/*
  - Mount the new RAM disk:
    systemctl daemon-reload
    mount /home/guest
  - Now, every time you reboot, any files the "guest" user creates will vanish, and a fresh set of default files from /etc/skel will be waiting for them.

## Load APT Software

- login as "root"
- load the following software:
  apt install git curl python-is-python3 default-jdk black cpplint build-essential -y

## Load VS Code

- login as "root"
- goto VS Code downloads webpage and get the AMD64 deb version:
  apt install ./xxx.deb

## Install Brave Browser

- login as "root":
  curl -fsSLo /usr/share/keyrings/brave-browser-archive-keyring.gpg https://brave-browser-apt-release.s3.brave.com/brave-browser-archive-keyring.gpg
  echo "deb [signed-by=/usr/share/keyrings/brave-browser-archive-keyring.gpg] https://brave-browser-apt-release.s3.brave.com/ stable main" | tee /etc/apt/sources.list.d/brave-browser-release.list
  apt update && apt install brave-browser -y
  
## Change Guest's Dot Files

- login as "root"
- goto "/etc/skel":
  mkdir -p /etc/skel/.config/Code/User/
  nano /etc/skel/.config/Code/User/settings.json
  -> Paste your JSON settings into this file
  -> then to "rehydrate the home directory":
    rm -rf /home/guest/*

## Remove Gnome Tour

- login as "root"
- unistall the "tour" app

## Set Guest to Dark Mode

- login as "root"
- run:
  mkdir -p /etc/dconf/db/local.d/
  nano /etc/dconf/db/local.d/01-dark-mode
- add in:
  [org/gnome/desktop/interface]
  color-scheme='prefer-dark'
  gtk-theme='Adwaita-dark'

