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
- load the following software:
  ```bash
  apt install git curl python-is-python3 default-jdk black cpplint build-essential freecad python3-pip gimp mgba-qt -y
  ```
- then install:
```bash
pip3 install uflash --break-system-packages
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
  Extract libcs50-*.*
  cd libcs50-*
  sudo make install
  ```
  
## Install MicroBlocks

- login as "root":
- follow these instructions: https://microblocks.fun/get-started

## Install Brave Browser

- login as "root":
  ```bash
  curl -fsSLo /usr/share/keyrings/brave-browser-archive-keyring.gpg https://brave-browser-apt-release.s3.brave.com/brave-browser-archive-keyring.gpg
  echo "deb [signed-by=/usr/share/keyrings/brave-browser-archive-keyring.gpg] https://brave-browser-apt-release.s3.brave.com/ stable main" | tee /etc/apt/sources.list.d/brave-browser-release.list
  apt update && apt install brave-browser -y
  ```
- now to set Brave as the default browser:
  ```bash
  update-alternatives --install /usr/bin/x-www-browser x-www-browser /usr/bin/brave-browser 200
  update-alternatives --set x-www-browser /usr/bin/brave-browser
  update-alternatives --install /usr/bin/gnome-www-browser gnome-www-browser /usr/bin/brave-browser 200
  update-alternatives --set gnome-www-browser /usr/bin/brave-browser
  ```
- now change the default landing page:
  ```bash
  mkdir -p /etc/brave/policies/managed
  nano /etc/brave/policies/managed/initial_settings.json
  ```
  - add to this new file:
  ```bash
  {
    "RestoreOnStartup": 4,
    "RestoreOnStartupURLs": [
      "http://172.22.52.50"
    ],
    "HomepageLocation": "http://172.22.52.50",
    "HomepageIsNewTabPage": false
  }
  ```
    - add the following to "/etc/brave/policies/managed/search_policy.json:
  ```bash
{
  "DefaultSearchProviderEnabled": true,
  "DefaultSearchProviderName": "Google",
  "DefaultSearchProviderSearchURL": "https://google.ca{searchTerms}",
  "DefaultSearchProviderSuggestURL": "https://google.ca{searchTerms}",
  "DefaultSearchProviderNewTabURL": "https://google.ca",
  "DefaultSearchProviderKeyword": "google.ca"
}
  ```
  - now update the "Exec" line: nano /usr/share/applications/brave-browser.desktop, with:
  ```bash
  Exec=/usr/bin/brave-browser-stable --no-default-browser-check %U http://172.22.52.50
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
- create directory: mkdir -p /etc/dconf/db/local.d/
- create a file named /etc/dconf/db/local.d/00-favorite-apps:
  ```bash
  [org/gnome/shell]
  favorite-apps=['brave-browser.desktop', 'org.gnome.Nautilus.desktop', 'code.desktop', 'freecad.desktop']
  ```
- update:
  ```bash
  sudo dconf update
  ```

## Set Git user Name and Email

- login as "root"
- run:
  ```BASH
  git config --system user.name "Guest student"
  git config --system user.email "guest.student@stu.ocsb.ca"
  ```
