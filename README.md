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
  - Clear out any existing physical files in the guest home to avoid conflicts:
    rm -rf /home/guest/*
  - Mount the new RAM disk:
    systemctl daemon-reload
    mount /home/guest
  - Now, every time you reboot, any files the "guest" user creates will vanish, and a fresh set of default files from /etc/skel will be waiting for them.

## Load APT Software

- login as "root"
- load the following software:
  apt install git python-is-python3 default-jdk black cpplint build-essential -y


 
