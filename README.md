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
