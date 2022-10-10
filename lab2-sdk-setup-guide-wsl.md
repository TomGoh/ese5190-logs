# Raspberry Pi RP2040 C SDK Setup for WSL



Author: Haoze Wu

This guide covers the environments setup, SDK setup and tools setup on **Windows Subsystem for Linux (WSL)** on Windows 11 on the following machine and system:

>**Test Machine:** Lenovo Thinkbook 16+ with Intel i5-12500H
>
>**System Version**: Windows 11 21H2 build 22000.1042
>
>**WSL Version**: WSL2 with Kernel version 5.10.102.1



## Terminal Setup

No need to setup additional terminal for this program, using the default Windows Terminal.



## WSL2 with Ubuntu 20.04 Setup

In this section, the guide would go over the process of setting up WSL2 on Windows 11.

### Install WSL2

Open Windows Terminal, make sure the build version of Windows is higher than 19041, and input

```shell
wsl --list --online
```

to check the available WSL distributions.

Here, we choose the Ubuntu 20.04 LTS as the WSL distribution and install it:

```shell
wsl --install -d Ubuntu-20.04
```

If you find trouble installing the WSL distributions, you may need to check the kernel version to be the latest by the command

```shell
wsl --status
```

And if the kernel is not the latest, download the update pack for WSL [Here](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi).

After installing proper WSL distribution and following the pop-up terminal initialization, open a new Windows Terminal dialog to  update the WSL distribution and restart to update:

```shell
wsl --update
wsl --restart
```



### Connect RP2040 to WSL

Since WSL is a subsystem inside the Windows, there are some more steps to project the RP2040 on the hardware connection port to the WSL's virtual USB port.

We need to setup the tools for attaching real USB devices to WSL called **usbipd**, you can view the details [Here](https://github.com/dorssel/usbipd-win).

First, we need to download and install the usbipd software on the Windows by downloading the latest client for Windows [Here](https://github.com/dorssel/usbipd-win/releases/latest).

After finishing the installation on Windows, setup the usbipd tool in WSL. Before installing it in WSL, we need to first update the software source in the WSL dialog through Windows Terminal:

```shell
sudo apt update
```

Then, install the usbipd:

```shell
sudo apt install linux-tools-5.4.0-77-generic hwdata
sudo update-alternatives --install /usr/local/bin/usbip usbip /usr/lib/linux-tools/5.4.0-77-generic/usbip 20
```

After WSL installing the usbipd, check the USB port usage in Windows dialog through Windows Terminal:

```shell
usbipd wsl --list
```

the possible output could be as follows:

![image-20221010151127066](assets\image-20221010151127066.png)

The **BUSID** would be used to recognized the port of the devices, and the **STATE** indicated whether the device is attached to WSL or not. Among all devices listed above, the device on bus `2-8` is the RP2040 to be attached.

Mount the RP2040 to WSL by:

```shell
usbipd wsl attach --busid 2-8
```

Then, check the status of usbipd again:

```shell
usbipd wsl list
```

You will fins the status  of the device on bus 2-8 has changed:

![image-20221010151421548](C:\Users\Tom-G\GitHub\ese5190-logs\assets\image-20221010151421548.png)

which means it is successfully attached to our WSL.

Then, in WSL, using `screen` to monitor the output from RP2040:

```bash
sudo screen /dev/ttyACM0 115200
```

Then, a new window would show the monitor of RP2040.

Hint: WSL may not be installed screen in advance, you may need to install it manually by:

```shell
sudo apt install screen
```



### Install Visual Studio Code on Windows

Since the WSL is a subsystem inside the Windows, we could connect it through Visual Studio Code by installing some addons, which makes development more easily.

Download and install Visual Studio Code [Here](https://code.visualstudio.com/download) for Windows. 

After installation, open VS Code and install some addons, includes:

- WSL
- C/C++
- CMake
- CMake Tools
- C/C++ Themes

The latter four addons should be installed in VS Code for WSL.

Then, you can connect WSL through VS Code by clicking the Remote Explorer button on the left navigation dock of VS Code to add remote connection to WSL.

 

## Setting Up C/C++ SDK for RP2040 in WSL

After the environment setup, we are ready to set up the SDK in WSL.

C/C++ SDK could be downloaded from Raspberry website [Here](https://github.com/raspberrypi/pico-sdk) manually, we are going to use Git to download it.

WSL may not contain git, you may need to install it manually:

```shell
sudo apt install git
```

Then, making a folder where you want to put the SDK, here I choose `/home/tom/codes/embedding/pico`, and then using git to download the SDK and examples into that folder:

```shell
cd /home/tom/codes/embedding/pico
git clone -b master https://github.com/raspberrypi/pico-sdk.git
cd pico-sdk
git submodule update --init
cd ..
git clone -b master https://github.com/raspberrypi/pico-examples.git
```

Also follow the instructions to install toolchains for the SDK:

```shell
sudo apt install cmake gcc-arm-none-eabi libnewlib-arm-none-eabi build-essential gcc g++
```

Here, the SDK is successfully downloaded into the WSL, we still need to use vim to write the path of it into WSL by editing `~/.bashrc` file:

```shell
vim ~/.bashrc
```

In the vim editing interface, press `i` to insert. Insert the following line at the end of the file:

```shell
export PICO_SDK_PATH="/home/tom/codes/embedding/pico/pico-sdk"
```

Then, press `Esc` and input `:wq` to quit and save the change.

To make the change activated at once, input:

```shell
source ~/.bashrc
```

Here, the configuration of SDK for RP2040 on WSL is done.



## Build Examples using SDK

After configuring the SDK on WSL, we could compile the example downloaded above to test the SDK.

First, switch working directory to the example and make a new directory named `build` and switch into it:

```shell
cd /home/tom/codes/embedding/pico/pico-example
mkdir build
cd build
```

Then, using CMake to build the example directory:

```
cmake ..
```

Here, we are going to print Hello World exmaple through USB output of the RP2040, so switch to the built USB Hello World example and make the example:

```
cd hello_world/usb
make -j4
```

After CMake finishing the process, there would be a `.uf2` file in the directory. Copy it from the WSL folder to the RP2040 connected as a USB device. To connect the RP2040 in USB mode, you should press the boot button when plugging into the USB port or holding the boot button after connected and then press reset button. Drag the `.uf2` file into the RP2040 USB device in Windows, then the USB drive would disappear from the File Explorer. 

Then follow the guide in the [previous section](#Connect RP2040 to WSL) of this guide to connect it to WSL, you could see the output of Hello World in the Terminal:

![image-20221010163642601](C:\Users\Tom-G\GitHub\ese5190-logs\assets\image-20221010163642601.png)



## Reference

[Raspberry Pico C SDK Manual](https://datasheets.raspberrypi.com/pico/raspberry-pi-pico-c-sdk.pdf)

[Raspberry Pico C Getting Started Guide](https://datasheets.raspberrypi.com/pico/getting-started-with-pico.pdf)

[Install Linux on Windows with WSL](https://learn.microsoft.com/en-us/windows/wsl/install)

[usbipd-win](https://github.com/dorssel/usbipd-win)

[Overview -  Adafruit QT Py RP2040](https://learn.adafruit.com/adafruit-qt-py-2040)

