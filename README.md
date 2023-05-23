# ubuntu
setups 

## zsh oh-my-zsh

```
sudo apt install zsh
sudo apt install curl wget git axel 
chsh -s $(which zsh)
```

reference:  https://www.tecmint.com/install-oh-my-zsh-in-ubuntu/


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

## sublime via snap
```
snap install sublime-text --classic
```

