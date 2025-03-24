# ubuntu & windows
[a good example setups](https://innovativeinnovation.github.io/ubuntu-setup/settings/)

## set nvim+lsp 
https://www.lazyvim.org/

## clang clangd 
```
wget https://apt.llvm.org/llvm.sh
chmod +x llvm.sh
sudo ./llvm.sh <version number> all # say, <version number> = 18
```

## multiple versions
```
sudo update-alternatives --install /usr/bin/clang clang /usr/bin/clang-18 100
sudo update-alternatives --install /usr/bin/clang++ clang++ /usr/bin/clang++-18 100
sudo update-alternatives --install /usr/bin/clangd clangd /usr/bin/clangd-18 100
```

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
[change open terminal as terminator by change preference of gnome-ternimal](https://askubuntu.com/questions/76712/setting-nautilus-open-terminal-to-launch-terminator-rather-than-gnome-terminal)

'''
pip3 install nautilus-open-any-terminal --break-system-packages
glib-compile-schemas ~/.local/share/glib-2.0/schemas/
gsettings set com.github.stunkymonkey.nautilus-open-any-terminal terminal "terminator"
'''

## vi keyboard issue  
```
echo "set nocompatible" >> ~/.vimrc
```
## sublime

```
sudo snap install sublime-text --classic
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

## developper 
## essentials
```
sudo apt install build-essential ninja-build git 
```

### visual studio code
```
sudo apt install software-properties-common
wget -q https://packages.microsoft.com/keys/microsoft.asc -O- | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main"
sudo apt install code -y
```
settings. 
1. User setting. Ctrl+Shift+P for user setting (json)
2. add:
```
    "cmake.configureSettings": {
       "CMAKE_TOOLCHAIN_FILE": "${vcpkg_dir}/scripts/buildsystems/vcpkg.cmake"
    },
    "cmake.configureOnOpen": true,
    "cmake.cmakePath": "${cmake_dir}/bin/cmake"  # this will overwride the system cmake or cmake in the path
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

### miniconda
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

### qt for paraview 
```
sudo apt install -y qtcreator
```

### onnx runtime 
```
mkdir /tmp/onnxInstall
cd /tmp/onnxInstall
wget -O onnx_archive.nupkg https://www.nuget.org/api/v2/package/Microsoft.ML.OnnxRuntime/1.15.1
unzip onnx_archive.nupkg
```

```
cd runtimes/linux-x64/native/
ln -s libonnxruntime.so libonnxruntime.so.1.15.1
sudo cp libonnxruntime.so /usr/local/lib/
sudo cp libonnxruntime.so.1.15.1 /usr/local/lib/

cd /tmp/onnxInstall
sudo mkdir -p /usr/local/include/onnxruntime/
sudo cp -r build/native/include/ /usr/local/include/onnxruntime/
```

cmake stuffs: <br>
sudo nano /usr/local/share/cmake/onnxruntime/onnxruntimeVersion.cmake

```
set(PACKAGE_VERSION "1.15.1")

# Check whether the requested PACKAGE_FIND_VERSION is compatible
if("${PACKAGE_VERSION}" VERSION_LESS "${PACKAGE_FIND_VERSION}")
  set(PACKAGE_VERSION_COMPATIBLE FALSE)
else()
  set(PACKAGE_VERSION_COMPATIBLE TRUE)
  if("${PACKAGE_VERSION}" VERSION_EQUAL "${PACKAGE_FIND_VERSION}")
    set(PACKAGE_VERSION_EXACT TRUE)
  endif()
endif()
```
sudo nano /usr/local/share/cmake/onnxruntime/onnxruntimeConfig.cmake

```
# Custom cmake config file by jcarius to enable find_package(onnxruntime) without modifying LIBRARY_PATH and LD_LIBRARY_PATH
#
# This will define the following variables:
#   onnxruntime_FOUND        -- True if the system has the onnxruntime library
#   onnxruntime_INCLUDE_DIRS -- The include directories for onnxruntime
#   onnxruntime_LIBRARIES    -- Libraries to link against
#   onnxruntime_CXX_FLAGS    -- Additional (required) compiler flags

include(FindPackageHandleStandardArgs)

# Assume we are in <install-prefix>/share/cmake/onnxruntime/onnxruntimeConfig.cmake
get_filename_component(CMAKE_CURRENT_LIST_DIR "${CMAKE_CURRENT_LIST_FILE}" PATH)
get_filename_component(onnxruntime_INSTALL_PREFIX "${CMAKE_CURRENT_LIST_DIR}/../../../" ABSOLUTE)

set(onnxruntime_INCLUDE_DIRS ${onnxruntime_INSTALL_PREFIX}/include/onnxruntime/include)
set(onnxruntime_LIBRARIES onnxruntime)
set(onnxruntime_CXX_FLAGS "") # no flags needed


find_library(onnxruntime_LIBRARY onnxruntime
    PATHS "${onnxruntime_INSTALL_PREFIX}/lib"
)

add_library(onnxruntime SHARED IMPORTED)
set_property(TARGET onnxruntime PROPERTY IMPORTED_LOCATION "${onnxruntime_LIBRARY}")
set_property(TARGET onnxruntime PROPERTY INTERFACE_INCLUDE_DIRECTORIES "${onnxruntime_INCLUDE_DIRS}")
set_property(TARGET onnxruntime PROPERTY INTERFACE_COMPILE_OPTIONS "${onnxruntime_CXX_FLAGS}")

find_package_handle_standard_args(onnxruntime DEFAULT_MSG onnxruntime_LIBRARY onnxruntime_INCLUDE_DIRS)
```

CMakeLists.txt

```
find_package(onnxruntime)

include_directories(${onnxruntime_INCLUDE_DIRS}/onnxruntime/include)

add_executable(${EXE_NAME} main.cpp)

target_link_libraries(${EXE_NAME}
    ${onnxruntime_LIBRARY}
)
```



## sublime via snap
```
snap install sublime-text --classic
```
## sensors 
```
sudo apt install lm-sensors psensors
```

## auto start
create /etc/systemd/system/get_cpu_temperature.service, with below:

```
[Unit]
Description=Get CPU Temperature
After=network.target

[Service]
ExecStart=/XXX/XXX/cpu_logger.py
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
```
python3 -m pip install 'tensorflow[and-cuda]'==2.15.1
```
```
 sudo apt install nvidia-cuda-toolkit
```
from nvidia, [install cuda 12.6](https://github.com/dicksondickson/ComfyUI-Clean-Install/blob/main/cuda-12.6-install-for-ubuntu-22.04.sh) 
```
echo "Install CUDA"
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-ubuntu2204.pin
sudo mv cuda-ubuntu2204.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/12.6.0/local_installers/cuda-repo-ubuntu2204-12-6-local_12.6.0-560.28.03-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu2204-12-6-local_12.6.0-560.28.03-1_amd64.deb
sudo cp /var/cuda-repo-ubuntu2204-12-6-local/cuda-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cuda-toolkit-12-6
```
[cudnn](https://davidblog.si/2022/12/24/setting-up-cuda-and-cudnn-in-ubuntu-22-04/)
```
wget https://developer.download.nvidia.com/compute/cudnn/9.3.0/local_installers/cudnn-local-repo-ubuntu2204-9.3.0_1.0-1_amd64.deb
sudo dpkg -i cudnn-local-repo-ubuntu2204-9.3.0_1.0-1_amd64.deb
sudo cp /var/cudnn-local-repo-ubuntu2204-9.3.0/cudnn-*-keyring.gpg /usr/share/keyrings/
sudo apt-get updatesudo apt-get -y install cudnn
```

https://docs.nvidia.com/deeplearning/tensorrt/install-guide/index.html#installing
dpkg-query -W tensorrt

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
