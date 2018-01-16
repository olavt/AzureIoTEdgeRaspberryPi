# How to setup IoT Edge to run on Raspberry PI 3 with Raspbian Stretch
### Author: [Olav Tollefsen](https://www.linkedin.com/in/olavtollefsen/)

## Introduction

This article documents the process of installing a Raspberry PI 3 from scratch to be able to run Azure IoT Edge.

### What you will need

- A minimum of 1 x Raspberry PI 3 Model B with Micro SD card, power supply
- A computer with the ability to connect a Micro SD card and software to burn an image to the card

## Prepare the Raspberry PI 3 Operating System on a Micro SD card

On a computer download the Linux operating system for Raspberry PI 3. I'm using Raspbian Stretch Lite, which you can dowload from [The Raspberry Pi Foundation Raspbian Download Page](https://www.raspberrypi.org/downloads/raspbian/).

Burn the downloaded operating system image to the Micro SD card. You will find more details in this [Installation Guide](https://www.raspberrypi.org/documentation/installation/installing-images/README.md)

## Add a new user and remove the default user

It's s good idea to create a new user for you on the Raspberry PI and remove the default user.

Add a new user:
```
$ sudo adduser <new-username>
```

Add the new user to sudoers:
```
$ sudo usermod -aG sudo <new-username>
```

To remove the default user you may need to reboot the Raspberry PI, login using the new-username and then perform this command:

```
$ sudo userdel -r pi
```

## Update the Raspbian OS

```
$ sudo apt-get update
$ sudo apt-get upgrade
```

## Change hostname / enable SSH / change local timezone / WiFi networking

Invoke the raspi-config utility to change the hostname, enable SSH and change the local timezone:
```
sudo raspi-config
```
You will find the SSH option under "5 Interfacing Options".

You may also want to change the local timezone. The "Change Timezone" option is under "4 Localisation Options".

### Enable WiFi networking

In raspi-config, select option "2 Network Options" and then "N2 WiFi". Enter SSID and password.

## Reboot the Raspberry PI
```
$ sudo reboot
```

## Install Docker

```
$ curl -fsSL get.docker.com -o get-docker.sh
$ sudo sh get-docker.sh
```

To be able to issue Docker commands without sudo:
```
$ sudo usermod -aG docker <your-username>
```

Logout from the current user and login again for the above command to take effect.

## Install and configure Azure IoT Edge

### Install PIP

```
$ sudo apt-get install python-pip -y 
```

### Prepare Python environment

```
$ sudo pip install --upgrade setuptools pip
$ sudo apt install python2.7-dev libffi-dev libssl-dev -y
```

###  Download the IoT Edge control script

```
$ sudo pip install -U azure-iot-edge-runtime-ctl
```

### Configure the runtime with your IoT Edge device connection string (you get this from the Azure IoT Hub)

```
$ sudo iotedgectl setup --connection-string "<device connection string>" --auto-cert-gen-force-no-passwords
```

### Start the runtime

```
$ sudo iotedgectl start
```

### Check Docker to see that the IoT Edge agent is running as a module

```
$ docker ps
```

### Authenticate against your private Docker registry

```
$ sudo iotedgectl login --address <your container registry address> --username <username> --password <password>
```

Your Raspberry PI 3 device is now ready to be used as an Azure IoT Edge device.
