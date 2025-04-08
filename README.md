# MakerHub

Makerhub is software forked from Protovac.  The Universal Computer for Protospace.  It will be used to display
random info about Kamloops Makerspace and printing nametags and labels.

Here are some of its screens:

```


                                       
 /$$      /$$           /$$                           /$$   /$$           /$$      
| $$$    /$$$          | $$                          | $$  | $$          | $$      
| $$$$  /$$$$  /$$$$$$ | $$   /$$  /$$$$$$   /$$$$$$ | $$  | $$ /$$   /$$| $$$$$$$ 
| $$ $$/$$ $$ |____  $$| $$  /$$/ /$$__  $$ /$$__  $$| $$$$$$$$| $$  | $$| $$__  $$
| $$  $$$| $$  /$$$$$$$| $$$$$$/ | $$$$$$$$| $$  \__/| $$__  $$| $$  | $$| $$  \ $$
| $$\  $ | $$ /$$__  $$| $$_  $$ | $$_____/| $$      | $$  | $$| $$  | $$| $$  | $$
| $$ \/  | $$|  $$$$$$$| $$ \  $$|  $$$$$$$| $$      | $$  | $$|  $$$$$$/| $$$$$$$/
|__/     |__/ \_______/|__/  \__/ \_______/|__/      |__/  |__/ \______/ |_______/ 
                                                                                   
                                                                                   
   [N] Nametags

   [S] Stats

   [E] Events

   [T] Think

   [A] About

```

```
MakerHub

Makerspace Stats
================

Next meeting: Thu Sep 15, 2022  7:00 PM

Next Event:   Workshop
              Sat Sep 10, 2022  2:00 PM

Last Event:   Fundraiser
              Sat Sep 3, 2022  10:00 AM

Member count: 249   Green: 208   Paused / expired: 1209

Card scans:   14



[B] Back

```

```
MakerHub

Events
==================             

Fundraiser
Sun Aug 28, 2022  2:00 PM     

Workshop
Fri Sep 2, 2022  7:00 PM

Board Game Night
Sun Sep 11, 2022  2:00 PM     


[B] Back  [J] Down  [K] Up
```

```
MakerHub

Think
=====

Give MakerHub something to think about.

   [E] Edit prompt

Examples:

   42 + 69
   55 kg to lbs
   density of lead
   if x = 4, what is 3x + 50?
   force m=150g, a=50cm/s^2
   boiling point of benzene at 550 torr
   goats with highest milk yield
   how long did the Aztec empire last?




[B] Back
```

## Development Setup

For developing on your own Ubuntu / Debian machine:

```
$ sudo apt update
$ sudo apt install python3 python3-dev python3-pip python3-virtualenv libopenjp2-7 git
$ git clone https://github.com/KamloopsMakerspace/MakerHub/makerhub.git
$ cd makerhub/
$ virtualenv -p python3 env
$ source env/bin/activate
(env) $ pip install -r requirements.txt
(env) $ python main.py
```

## Rasberry Pi Setup

### SD Card

Format SD card, mount on your own system.

```
$ cd boot/  # on SD card
$ touch ssh
$ touch wpa_supplicant.conf
```

Edit `wpa_supplicant.conf`:

```
country=CA
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
network={
    ssid="YOUR_NETWORK_NAME"
    psk="YOUR_PASSWORD"
    key_mgmt=WPA-PSK
}
```

Append to `config.txt`:

```
enable_uart=1
```

Optionally set up a cronjob:

```
$ cd rootfs/  # on SD card
$ sudo touch var/spool/cron/crontabs/pi
$ sudo chmod u=rw,og= var/spool/cron/crontabs/pi
$ sudo chown pi:crontab var/spool/cron/crontabs/pi
```

Edit `var/spool/cron/crontabs/pi` (optional):

```
*/5 * * * *  # example command that runs every 5 minutes
```

### On Raspberry Pi

SSH into the Raspberry Pi for setup:

```
$ sudo apt update
$ sudo apt install python3 python3-dev python3-pip python3-virtualenv libopenjp2-7 vim byobu git
$ sudo hostnamectl set-hostname makerhub
```

Replace "raspberrypi" with "makerhub" in `/etc/hosts`.

Create "makerhub" user:

```
$ sudo adduser makerhub
$ sudo usermod -aG dialout makerhub
$ sudo usermod -aG lp makerhub
$ sudo usermod -aG gpio makerhub
```

Edit `/lib/systemd/system/serial-getty@.service`:

```
ExecStart=-/sbin/agetty --autologin makerhub 9600 %I mt70
```

Edit `/etc/passwd`:

```
makerhub:x:1001:1001:,,,:/home/makerhub:/home/pi/makerhub/main.py
```

```
$ cd
$ chmod o+rx ~
$ git clone https://github.com/KamloopsMakerspace/MakerHub/makerhub.git
$ cd makerhub/
$ sudo tic -o /lib/terminfo/ mt70
$ virtualenv -p python3 env
$ source env/bin/activate
(env) $ pip install -r requirements.txt
(env) $ python main.py

# Make sure it loads, then press D, then X to exit
```

Restart getty:

```
$ sudo systemctl daemon-reload
$ sudo systemctl restart serial-getty@ttyS0.service
```

### Brother QL-700 Setup

Make sure it's connected with `sudo lsusb`:

```
Bus 001 Device 002: ID 0922:0020 Brother QL-700
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```

Install CUPS:

```
$ sudo apt-get update
$ sudo apt-get install cups cups-client printer-driver-dymo
$ sudo lpadmin -p dymo -v `sudo lpinfo -v | grep DYMO | cut -d" " -f2` -m 'dymo:0/cups/model/lw450.ppd'
# wait...
$ sudo cupsenable dymo
$ sudo cupsaccept dymo
```

Test the printer:

```
$ echo "hello world" | lp -d dymo -
```
