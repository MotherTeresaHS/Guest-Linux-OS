# Guest Linux OS

The follwoing are the instructions to create a Linux computer that students can log into as a "Guest", where the home directory is just mounted to a RAM disk. This way as each new student logs in, the "Guest" account is completly fresh and has no saved data. But the students still have access to all the locally loaded programs.

## Install base OS

- for this installation I am using Debian 13, Trixie
  - downlaod the latest ISO and burn it to a USB
  - go through the standard install
    - host is: "cs" 
    - ensure you pick "gnome" desktop and "ssh server"  would also be helpful 
  - for the user create:
    - login: guest
  - remember that the "guest" user by default does not have "sudo" privileges and that is what we what, DO NOT give guest "sudo" access

## Change Power Settings
- login as "root"
- run this command:
```BASH
systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
```

## Cleanup Boot Screen

- login as "root"
- to remove as much "linux text" startup screen:
  - in "/etc/default/grub" file change:
  ```BASH
  GRUB_CMDLINE_LINUX_DEFAULT="quiet splash loglevel=3"
  GRUB_TIMEOUT=0
  GRUB_TIMEOUT_STYLE=hidden
  GRUB_RECORDFAIL_TIMEOUT=0
  ```
- then to make the change permanent:
```BASH
update-grub
```

## Mount /home/guest to RAM

- to make the "guest" user home directory in RAM we will use "tmpfs"
  - login as "root"
  -  in /etc/fstab, add the following to the end:
    ```bash
    tmpfs  /home/guest  tmpfs  defaults,size=1G,mode=0700,uid=guest,gid=guest  0  0
    ```
  - Since the RAM disk is wiped every reboot, the guest user won't have a .bashrc or desktop folders. You can use systemd-tmpfiles to automatically copy default files from /etc/skel every time the machine boots.
    -  nano /etc/tmpfiles.d/guest-home.conf
      ```bash
      C  /home/guest  -  -  -  -  /etc/skel
      Z  /home/guest  -  guest  guest  -  -
      ```
  - Clear out any existing physical files in the guest home to avoid conflicts:
    ```bash
    rm -rf /home/guest/*
    ```
  - Mount the new RAM disk:
    ```bash
    systemctl daemon-reload
    mount /home/guest
    ```
  - Now, every time you reboot, any files the "guest" user creates will vanish, and a fresh set of default files from /etc/skel will be waiting for them.

## Load APT Software

- login as "root"
- load and remove the following software:
  ```bash
  apt install git curl python-is-python3 python3-pip black default-jdk build-essential gdb cpplint arduino freecad gimp mgba-qt -y
  ```

## Load VS Code

- login as "root"
- goto VS Code downloads webpage and get the AMD64 deb version:
- get it from: https://packages.microsoft.com/repos/vscode/pool/main/c/code/
  ```bash
  apt install ./xxx.deb
  ```
## Load CS50 Library

- login as "root"
- run these commands
  ```bash
  Download the latest release from https://github.com/cs50/libcs50/releases
  Extract libcs50-*.*: tar -xvzf ./v...
  cd libcs50-*
  make install
  ```
  
## Install MicroBlocks

- login as "root":
- run this:
```BASH
wget https://microblocks.fun/downloads/latest/packages/ublocks-amd64.deb
apt install ./ublocks-amd64.deb -y
```
- follow these instructions: https://microblocks.fun/get-started:
```BASH
usermod -a -G dialout guest
usermod -a -G tty guest
```

## FireFox Fix
  
- create file:
  ```bash
  nano /usr/lib/firefox-esr/distribution/policies.json
  ```
  
- add in:
  ```bash
  {
    "policies": {
      "OverrideFirstRunPage": "",
      "OverridePostUpdatePage": "",
      "DontCheckDefaultBrowser": true,
      "DisableTelemetry": true,
      "NoDefaultBookmarks": true
    }
  }
  ```
      
- edit file:
  ```bash
  nano /etc/firefox-esr/firefox-esr.js
  ```

- add in:
  ```bash
  // startup tabs
  pref("browser.startup.homepage", "http://172.22.52.50|https://gmail.com");
  ```

## Change Guest's Dot Files

- login as "root"
- goto "/etc/skel":
  ```bash
  mkdir -p /etc/skel/.config/Code/User/
  nano /etc/skel/.config/Code/User/settings.json
  ```
  -> Paste your JSON settings into this file
  -> then to "rehydrate the home directory":
    ```bash
    rm -rf /home/guest/*
    ```

## Remove Gnome Tour

- login as "root"
- unistall the "tour" app
```BASH
apt remove gnome-tour -y
```

## Set Guest to Dark Mode

- login as "root"
- run:
  ```bash
  mkdir -p /etc/dconf/db/local.d/
  nano /etc/dconf/db/local.d/01-dark-mode
  ```
- add in:
  ```bash
  [org/gnome/desktop/interface]
  color-scheme='prefer-dark'
  gtk-theme='Adwaita-dark'
  ```

## Set Gnome Toolbar

- login as "root"
- create file /etc/dconf/profile/user:
  ```bash
  user-db:user
  system-db:local
  ```
  
- create a file named /etc/dconf/db/local.d/00-favorite-apps:
  ```bash
  [org/gnome/shell]
  favorite-apps=['firefox-esr.desktop', 'org.gnome.Nautilus.desktop', 'code.desktop']
  ```
- update:
  ```bash
  dconf update
  ```

## Set Git user Name and Email

- login as "root"
- run:
  ```BASH
  git config --system user.name "Guest student"
  git config --system user.email "guest.student@stu.ocsb.ca"
  ```
