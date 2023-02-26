---
title: "The First Steps"
date: 2023-02-26T18:32:30-00:00
categories:
  - blog
tags:
  - programming
  - tools
  - NanoPi
  - RaspberryPi
  - weather
  - IoT
  - ssh
  - IP
---

It’s time to get our hands dirty :) We’re going to install NanoPi so that we can communicate with it and execute a few commands. It is important to tell that I have NanoPi NEO3 version.

We basically follow the instructions from [the manufacturer's webpage][manufacturers-webpage]. We start from downloading FriendlyCore system image. This is operating system provided by FriendlyElec – NanoPi’s producer.


We put the compressed folder into `Documents/nanopi` directory. Then we extract folder into the very same location. 

Next we need to know what file in the `/dev` folder corresponds to SD card slot. In order to do this we use bash operator `ls` to list the contents of forder `/dev`. By default `/dev` folder contains files corresponding to devices managed by the system. We make sure that SD card that is going to be used with NanoPi is NOT inserted into computer and I list dev folder’s contents into newly created `ls_no_card`’ file:

```bash
ls /dev > ls_no_card
```

Then I insert SD card into computer and repeat the same steps, only now I use `ls_with_card` file.

Next I use bash command `diff` to find the difference between two files:

```bash
diff ls_no_card ls_with_card
```

The output of the command is as follows:

```bash
65a66,75
> mmcblk0
> mmcblk0p1
> mmcblk0p2
> mmcblk0p3
> mmcblk0p4
> mmcblk0p5
> mmcblk0p6
> mmcblk0p7
> mmcblk0p8
> mmcblk0p9
```
I can see that the name of the file corresponding to SD slot is `mmcblk0`. The forthcoming names are names of the partitions. Once I know this I go on and use `dd` command. Of course I do this with root priviliges.

**Important! It is crucial to paste exactly this command with no additional spaces as we can end up with overwriting root folder and this is something we want to avoid**

The `dd` command which works for me is as follows:

```bash
sudo dd if=rk3328-sd-friendlycore-lite-focal-5.15-arm64-20220919.img of=/dev/mmcblk0 status=progress
```
We copy system image file byte by byte into SD card. It can be a loong process. That's why I add progress flag to observe the status of copying.

Once this stage is finished I extract SD card and insert it again. I can see now that system image on SD card resembles a lot the one on my PC (if you use Linux :)) 

Next we want to set static IP address so that we can connect with NanoPi. To do it we search for `etc/network/interfaces.d` folder on SD card and we open `eth0` file in `vim` with root priviliges. Then we change its contents according to instruction from FriendlyElec’s site. The `etc` directory contains configuration of various system services. In our case we set static IP to `192.168.1.3` for our ethernet connection for NanoPi board:

```bash
auto eth0
iface eth0 inet static
address 192.168.1.3
netmask 255.255.255.0
gateway 192.168.1.1
```

We also configure the address of the DNS server in `etc/systemd/resolved.conf`:

```bash
[Resolve]
DNS=192.168.1.1
```

Finally we set up the static address for our PC wired connection. We choose different then NanoPi IP address (for example: `192.168.1.4`).

Now we are ready to connect to our NanoPi:) We insert SD card, plug in the power and we connect our PC via ethernet cable to our NanoPi board. Next, we open terminal and we try to login via ssh using default credentials:

```bash
ssh pi@192.168.1.3
```

If everything works ok, we should see the welcome screen from our NanoPi.

We are happy that we accomplished the important milestone: we launched our NanoPi board:)

[manufacturers-webpage]: https://wiki.friendlyelec.com/wiki/index.php/NanoPi_NEO3
