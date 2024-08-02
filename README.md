# ubuntu & windows
setups 

## zsh oh-my-zsh

```
sudo apt install zsh
sudo apt install curl wget git axel 
chsh -s $(which zsh)
sh -c "$(wget https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"
```

reference:  https://www.tecmint.com/install-oh-my-zsh-in-ubuntu/

## terminator 

```
sudo apt install terminator
```


## vi keyboard issue  
```
echo "set nocompatible" >> ~/.vimrc
```


## boot menu font size
1. Choose a font, in this example I chose DejaVuSansMono.ttf
2. Convert the font in a format GRUB understands:
3. ```sudo grub-mkfont -s 34 -o /boot/grub/DejaVuSansMono.pf2 /usr/share/fonts/truetype/dejavu/DejaVuSansMono.ttf```
4. Edit the /etc/default/grub file adding a line:
  ```GRUB_FONT=/boot/grub/DejaVuSansMono.pf2```
Update GRUB configuration with:
BIOS: sudo grub-mkconfig -o /boot/grub/grub.cfg
reboot. 

## dual boot, time setting
1. on ubuntu, set rtc in local time zone:  ```timedatectl set-local-rtc 1 --adjust-system-clock```
2. on windows, ```Reg add HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_QWORD /d 1```
reference: [why this happens](https://ubuntuhandbook.org/index.php/2016/05/time-differences-ubuntu-1604-windows-10/) 

## visual studio code
```
sudo apt install software-properties-common
wget -q https://packages.microsoft.com/keys/microsoft.asc -O- | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main"
sudo apt install code -y
```
settings. 
1. User setting. Ctrl+Shift+P for user setting (json)
2. add:
``` "cmake.configureSettings": {
       "CMAKE_TOOLCHAIN_FILE": "/home/comkadia/repositories/vcpkg/scripts/buildsystems/vcpkg.cmake"
    },
    "cmake.configureOnOpen": true,
    "cmake.cmakePath": "/home/comkadia/cmake-3.27.4-linux-x86_64/bin/cmake"
```
3. add vcpkg.json file in the project root. e.g.,
```
{
  "dependencies": [
    {
      "name": "spdlog",
      "version>=": "1.12.0"
    },
    {
      "name": "vtk",
      "version>=": "9.2.0-pv5.11.0#10"
    }
  ],
  "builtin-baseline": "9fd83fa98b5f2ae6188a6ba59a49837061db0a3a"
}
```

## miniconda
```
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
chmod +x Miniconda3-latest-Linux-x86_64.sh
./Miniconda3-latest-Linux-x86_64.sh

```
export env
```
conda env export  --no-builds | grep -v "prefix" > environment.yml
conda env create -f environment.yml, or
conda env create -f environment.yml -p /home/XXX/anaconda3/envs/env_name
```
if pip dependece error happens, edit the yml file, and run
```
conda clean --all 
conda env update -f environment.yml
```
then, activate 
```
conda activate env_name
```

## sublime via snap
```
snap install sublime-text --classic
```

## auto start
create /etc/systemd/system/get_cpu_temperature.service, with below:

```
[Unit]
Description=Get CPU Temperature
After=network.target

[Service]
ExecStart=/home/comkadia/Downloads/cpu_logger.py
Restart=always  
User=root
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=get_cpu_temperature

[Install]
WantedBy=multi-user.target
```
change Restart from always to no if you want to run once.

enable, start, stop, status 
```
sudo systemctl disable get_cpu_temperature.service
sudo systemctl enable get_cpu_temperature.service
sudo systemctl start get_cpu_temperature.service
sudo systemctl stop get_cpu_temperature.service
sudo systemctl status get_cpu_temperature.service
```
## mount ntfs 
```
sudo apt update
sudo apt install ntfs-3g
sudo mount -t ntfs-3g /dev/nvme1n1p2 /mnt/data
#note 1: disable fast-startup and hibernation on windows at power options
#note 2: set options in fstab /src/ /des/, ntfs-3g, uid=kli, gid=kli, 0 0 
```

## install cuda and cudnn8, and tensorrt
https://davidblog.si/2022/12/24/setting-up-cuda-and-cudnn-in-ubuntu-22-04/
https://docs.nvidia.com/deeplearning/tensorrt/install-guide/index.html#installing

## bluetooth issues <br>
https://askubuntu.com/questions/676853/bluetooth-headset-with-poor-sound-quality-on-ubuntu

## usb wifi
check the usb model:  lsusb
```
Bus 006 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 005 Device 004: ID 046b:ff10 American Megatrends, Inc. Virtual Keyboard and Mouse
Bus 005 Device 003: ID 046b:ffb0 American Megatrends, Inc. Virtual Ethernet
Bus 005 Device 002: ID 046b:ff01 American Megatrends, Inc. Virtual Hub
Bus 005 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 004 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 003 Device 005: ID 8087:0032 Intel Corp. AX210 Bluetooth
Bus 003 Device 004: ID 26ce:01a2 ASRock LED Controller
Bus 003 Device 003: ID 05e3:0608 Genesys Logic, Inc. Hub
Bus 003 Device 002: ID 26ce:0a05 Generic USB Audio
Bus 003 Device 006: ID 0bda:b812 Realtek Semiconductor Corp. RTL88x2bu [AC1200 Techkey]
Bus 003 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 008 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 007 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 009 Device 003: ID 046d:c548 Logitech, Inc. USB Receiver
Bus 009 Device 002: ID 046d:c52b Logitech, Inc. Unifying Receiver
Bus 009 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 010 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
```
<br> the wifi model is  RTL88x2bu
```
git clone https://github.com/RinCat/RTL88x2BU-Linux-Driver.git
cd RTL88x2BU-Linux-Driver/
make
sudo make install
sudo modprobe 88x2bu
```

After each kernel update, you must recompile:
```
cd ~/RTL88x2BU-Linux-Driver/
make clean
git pull
make
sudo make install
sudo modprobe 88x2bu
```
