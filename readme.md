# Linux Cheat Sheet

Linux Cheat Sheet is a collection of useful commands and shortcuts for Linux.

* [Automatic Shutdown](#automatic-shutdown)
* [Changing monitor or screen Brightness and Gamma](#changing-monitor-or-screen-brightness-and-gamma)
* [Systemd, systemctl](#systemd-systemctl)
  * [Reloading](#reloading)
  * [Find services failed to start](#find-services-failed-to-start)
  * [Journal Size](#journal-size)
  * [Starting a script after GUI has loaded](#starting-a-script-after-gui-has-loaded)
  * [Run a script after suspending has finished (resume)](#run-a-script-after-suspending-has-finished-resume)
  * [Run a script after system-sleep resume](#run-a-script-after-system-sleep-resume)
  * [Unit files' locations](#unit-files-locations)
* [XDG](#xdg)
  * [Make a startup script using XDG startup](#make-a-startup-script-using-xdg-startup)
* [Autostarts and Startup scripts and programs locations](#autostarts-and-startup-scripts-and-programs-locations)
* [VPN](#vpn)
  * [Redirecting the whole traffic](#redirecting-the-whole-traffic)
* [Font](#font)
  * [Location](#location)
  * [List](#list)
  * [Fira Code](#fira-code)
* [Backup using Rsync](#backup-using-rsync)
* [Fixing broken grub](#fixing-broken-grub)
* [Things to do after installing Manjaro/Arch Linux](#things-to-do-after-installing-manjaroarch-linux)
* [Things to do after installing Windows 11](#things-to-do-after-installing-windows-11)

## Automatic Shutdown

```bash
sudo shutdown -P +220 ## in 220 minutes, 3:30 hours
```

## Changing monitor or screen Brightness and Gamma

```bash
xrandr --output HDMI-A-0 --brightness 0.75 --gamma 0.9:0.9:0.9 
```

## Systemd, systemctl

### Reloading

```bash
systemctl daemon-reload
```

### Find services failed to start

```bash
systemctl --state=failed
```

### Journal Size

```bash
sudo nano /etc/systemd/journald.conf
```

```bash
SystemMaxUse=100M
```

### Starting a script after GUI has loaded

```bash
sudo nano /lib/systemd/system/myprogram.service
```

```bash
[Unit]
Description=Set gamma on system boots up
After=network.target
After=systemd-user-sessions.service
After=network-online.target

[Service]
Type=idle
ExecStartPost=/bin/sleep 5
ExecStart=/usr/bin/myprogram
[Install]
WantedBy=graphical.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable myprogram.service
systemctl status myprogram
journalctl -u myprogram
```

### Run a script after suspending has finished (resume)

```bash
sudo nano /etc/systemd/system/myprogram.service
```

```bash
[Unit]
After=suspend.target
[Service]  
Type=simple
ExecStart=/usr/bin/myprogram
[Install]
WantedBy=suspend.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable myprogram.service
systemctl status myprogram
journalctl -u myprogram
```

### Run a script after system-sleep resume

```bash
sudo nano /lib/systemd/system-sleep/myprogram.sh
```

```bash
#!/bin/sh
PATH=/sbin:/usr/sbin:/bin:/usr/bin:/usr/local/sbin:/usr/local/bin
case $1 in
 post)
  /usr/bin/myprogram
 ;;
esac

exit 0
```

```bash
sudo chmod +x /lib/systemd/system-sleep/myprogram
sudo systemctl enable myprogram
```

### Unit files' locations

```bash
systemctl show --property=UnitPath
```

## XDG

### Make a startup script using XDG startup

```bash
nano ~/.config/autostart/gamma_on_startup.desktop
```

```bash
[Desktop Entry]
Name=gamma-on-startup
Type=Application
Exec=bash -c  "/usr/bin/gamma_on_startup &> /dev/null" 
Terminal=true
```

```bash
desktop-file-validate ~/.config/autostart/gamma_on_startup.desktop
chmod +x ~/.config/autostart/gamma_on_startup.desktop
```

Example program:

```bash
nano /usr/bin/gamma_on_startup

sleep 5
xrandr --output HDMI-A-0 --brightness 0.75 --gamma 0.9:0.9:0.9 
echo "gamma is changed"
```

## Autostarts and Startup scripts and programs locations

* nano .profile
* nano /etc/profile
* ls /etc/profile.d/
* ls ~/.config/autostart
* ls /etc/xdg/autostart
* ls /usr/share/xsessions
* ls -1 /lib/systemd/system/*.service /etc/systemd/system/*.service
* sudo systemctl list-unit-files --type=service --state=enabled --all
* ls /etc/init.d/
* nano ~/.xinitrc
* nano /etc/X11/xinit/xinitrc
* ls /etc/X11/xinit/xinitrc.d/
* cat ~/.xserverrc
* cat /etc/pam.d/
* ls /etc/xdg/lxsession/LXDE/autostart
* ls ~/.config/lxsession/LXDE/autostart
* crontab -e
* sudo crontab -e

* nano .bash_profile
* nano .bashrc
* nano /etc/bash.bashrc

## VPN

### Redirecting the whole traffic

```bash
sudo ip route add 192.168.1.0/24 dev ppp0
# ppp0: vpn name
# 192.168.1.0: IP range
```

## Install Genymotoin Android emulator

```bash
sudo pamac install genymotion
```

## Font

### Location

* `~/.local/share/fonts/`
* `/usr/local/share/fonts/`
* `/usr/share/fonts/`

### List

List installed fonts

```bash
fc-match -a
```

### Fira Code

A popular code font.

```bash
sudo pacman -S ttf-fira-code
fc-match -a | grep -i fira
```

## Backup using Rsync

```bash
sudo rsync -aAXHv --exclude={"/dev/*","/proc/*","/sys/*","/tmp/*","/run/*","/mnt/*","/var/*","/media/*","/usr/*","/lib/*","/lib64/","/lost+found","/swapfile",".npm*",".npm/*","node_modules*","node_modules/*","mesa_shader_cache*","steamapps*","Data*","Steam*"} / /run/media/mlibre/H/OS/full-copy/
```

## Resetting sound, audio

```bash
pulseaudio --kill
# pulseaudio --start
```

## Fixing broken grub

Boot a live Manjaro image

```bash
sudo manjaro-chroot -a
grub-install
update-grub
```

It will detect your current installed linux.  
Restart the computer and it will boot the installed linux.  
Then run:

```bash
update-grub
```

## Things to do after and before installing Manjaro/Arch Linux

* Backup important data. Recovery-keys, Passwords, Postman and ...
* Mark EFI partition while installing Manjaro/Arch Linux as /boot/efi. Don't check Format option.
* Make an XDG autostart script for gamma adjustment
* Put the gamma script in the `.bashrc` and `.zshrc` as well
* Install vscode

```bash
pamac update --force-refresh
pamac install visual-studio-code-bin
```

* Install ProtonVPN, mailspring
  
```bash
pamac build protonvpn
pamac build mailspring
```

* Remove Mainspring from startups. Use 24-hour clock. Uncheck automatically load images. Disable mail signature.
* Setting -> Startup and Shutdown: Start with empty session, Choose KDE Screen Saver and review background services.
* Pin Firefox, Terminal, ProtonVPN, Kate and VSCode to the panel
* Software Center: Disable automatic updates, Add AUR support
* Remove Virtual Desktops
* Pacman downloads parallel

```bash
sudo nano /etc/pacman.conf
ParallelDownloads = 5
```

* Theme: Breeze Dark
* Font: +1 PT

* Remembers sudo password

```bash
sudo nano /etc/sudoers
Defaults        timestamp_timeout=300 # 5 hours
```

* Make a Swapfile

```bash
sudo dd if=/dev/zero of=/swapfile bs=1M count=4096 status=progress
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
cat /etc/fstab
sudo bash -c "echo /swapfile none swap defaults 0 0 >> /etc/fstab"
```

* open an application using tor over socks

```bash
torsocks deluge
```

* Fix time difference between linux and windows

```bash
timedatectl set-local-rtc 1 --adjust-system-clock
# sudo timedatectl set-local-rtc 1
sudo ntpdate time.nist.gov # update time
```

* Softwares

```bash
sudo pacman -R thunderbird hplip cups yakuake manjaro-printer gutenprint cups-pdf qbittorrent snapd libpamac-snap-plugin flatpak libpamac-flatpak-plugin
sudo pacman-mirrors --fasttrack
sudo pacman -Syyuu
sudo pacman -S deluge clamav electrum chromium firefox gimp gparted libreoffice-fresh meld vlc ntfs-3g firewalld aria2 ttf-ubuntu-font-family gnome-keyring libsecret telegram-desktop core/iputils clinfo tor torsocks

sudo systemctl disable bluetooth.service
sudo systemctl disable tor.service

sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --permanent --add-port=30303/tcp # geth 
sudo firewall-cmd --permanent --add-port=30303/udp # geth

sudo systemctl enable firewalld
sudo systemctl restart firewalld

sudo systemctl restart --now clamav-daemon
sudo freshclam
clamscan --recursive --infected /home
sudo systemctl disable --now clamav-freshclam
sudo systemctl disable --now clamav-daemon
# sudo systemctl enable --now clamav-daemon
# sudo systemctl enable --now clamav-freshclam
```

* Firefox: Enable DNS over HTTPS
* Enable automatic mounting of external drives: Settings -> Hardware -> Removable Storage -> Automount
* Konsole config:
  * Font: Monospace 13pt
  * Theme: Breath (customized, a bit darker)
  * Shell: Manjaro zsh

### Install AMDGPU-PRO

```bash
# https://wiki.archlinux.org/title/AMDGPU_PRO
pamac install vulkan-amdgpu-pro
pamac install lib32-vulkan-amdgpu-pro 
# pamac install amdgpu-pro-libgl
# pamac install lib32-amdgpu-pro-libgl
# pamac install opencl-amd
# pamac install amf-amdgpu-pro
glxinfo | grep "OpenGL vendor string"
lspci -v | grep -A 10 VGA
```

### Uninstall AMDGPU-PRO

```bash
pacman -R $(pacman -Qg Radeon_Software_for_Linux | cut -f2 -d" ")
```

### Blacklist Radeon

```bash
sudo nano /etc/modprobe.d/radeon.conf 
blacklist radeon
```

## Things to do after installing Windows 11

* Download and install all the updates
* Enable Ransomware protection
* Download DimScreen, Copy it to the download folder. Open. click on the settings. set brightness to 20%. Make a shortcut to the desktop
* search for gamma calibration in windows settings. set it to minimum
* Adjust date and time: auto. Timezone tehran +3:30
* Downloading updates active hours: 24 hours format. 1->11
* Disable all data usages settings in privacy and security
* uninstall mail, teams, one drive. xbox, facebook, microsoft todo, sticky notes, tips, weather
* Install firefox and login
* Install protonVPN
* personalize: sunrise
* network connection: metered connection
* Display: 3840x2160, 200%
* Steam: add your games' location to the Games' folder library. make it as default
* Steam: If your games are in a `NTFS` file system, follow [this](https://github.com/ValveSoftware/Proton/wiki/Using-a-NTFS-disk-with-Linux-and-Windows) to make game compatible with Linux.
* Pause windows updates for 5 weeks
* Check windows startups apps
* App store: disable automatic update
* Leave from "AMD user experience program". AMD settings -> last tab -> last option
* windows features: WSL, virtual machine, hyper-v (for android and linux apps)
* wsl --update
* wsl --install -d Ubuntu
* Disable Error Reporting: WIN+R -> services.msc -> Windows Error Reporting Service -> Properties -> disable

## Install a new os on the phone

### backup data

```bash
adb backup -apk -shared -all -f backup-file.adb
```

### Enable Developer options

* Several times clicks on the kernel tab
* Allow OEM unlocking
* Enter Download mod:
  1. Turn off the phone.  
  2. Hold vol key up + down.
  3. Now in the warning message page. choose "unlocking bootloader" that will perform a factory reset

### Install odin tools for Samsung >= 3.14

<https://samsungodin.com/>

### Install samsung driver

<https://developer.samsung.com/android-usb-driver>

### Install adb

<https://developer.android.com/studio/releases/platform-tools>

```bash
adb reboot download
```

### Download TWRP

<https://www.droidthunder.com/install-twrp-recovery-on-galaxy-A10/>
<https://twrp.me/samsung/samsunggalaxya10.html>

#### convert it to .md5

<https://www.droidthunder.com/convert-img-to-tar-md5/>

### Run Odin as administrator

follow the instructions
<https://www.droidthunder.com/install-twrp-recovery-on-galaxy-A10/>

### Install bluestack

<https://www.bluestacks.com/>

Download, login and install whatsapp, telegram and google
