# Hi, I'm Aldiyes! 👋

Iam student from [Sanata Dharma University](https://www.usd.ac.id/) with ID [205314132](https://belajar.usd.ac.id/user/profile.php?id=16421)

<p align="center">
  <img src="https://belajar.usd.ac.id/pluginfile.php/1/theme_moove/logo/1705975785/logo_usd.png" alt="Sanata Dharma University">
</p>

## 🔎 Overview

This guide walks you through setting up a Ubuntu Server virtual machine (VM) using VirtualBox.

## 🛹 Prerequisites

- VirtualBox installed on your system. You can download it from the official website [here](https://www.virtualbox.org/wiki/Downloads)
- A recent Ubuntu Server ISO image. Download it from the Ubuntu website [here](https://releases.ubuntu.com/22.04.4/ubuntu-22.04.4-live-server-amd64.iso)

### ⚙️ Setting Up the Virtual Machine

#### 1. Create a New VM

Launch VirtualBox and click the "Machine" on the top left side "New" button to create a new virtual machine.

#### 2. Specify VM

- In the wizard, provide a name for your VM (e.g., "db-server").
- Specify where you want to allocate the VM
- Choose ISO Image and navigate to your downloaded Ubuntu Server ISO image and select it.

#### 3. Unattended Guest OS Install Setup

You can configure the unattended guest OS install by modifying `username`, `password`, and `hostname`. Additionally you can enable guest additions install. For Microsoft Windows guests it is possible to provide a product key.

#### 4. Allocate Memory:

Assign a suitable amount of memory (RAM) and processor to your VM. A good starting point RAM is 2048 MB and processor is 1.

#### 5. Create Virtual Hard Disk:

Choose "Create a virtual hard disk now" and allocate Disk Size for your VM.

### 🖥️ Installing Ubuntu Server

#### 1. Start the VM

Once configuration is complete, start your newly created VM

#### 2. Boot from ISO

The VM will boot from the attached ISO image. Choose "Install Ubuntu" to begin the installation process

#### 3. Installation Steps

Follow the on-screen instructions to complete the Ubuntu Server installation. Here are some key points during installation

- Choose your preferred language (e.g., english)
- Choose your keyboard layout
- Choose type of installation (Ubuntu Server Recomended)
- Choose Ubuntu configure proxy (Optional)
- Choose Ubuntu archive mirror (Optional)
- In guide storage configure, you can custom storage layout (optional) if you want
- Set up profile for your main user account. This account will not have root privileges by default
- In SSH Setup, Select Install OpenSSH server (optional)

#### 4. Complete Installation

After following the prompts, the installation will proceed. Once finished, it will prompt you to restart the VM

### 🌕 Post-Installation (Optional)

#### Update and Upgrade

Once booted into your new Ubuntu Server VM, connect to it using VirtualBox console by adding username and password that you created before. Update the package lists and upgrade any available packages using the following commands:

```shell
sudo apt update & sudo apt upgrade -y
```

## 🛜 Editing Netplan Configuration

#### 1. Locate Netplan Config File

The Netplan configuration file typically resides at /etc/netplan/00-installer-config.yaml (or a similar filename). Use your preferred text editor (e.g., nano or vim) to edit the file:

```shell
sudo nano /etc/netplan/00-installer-config.yaml
```

#### 2. Modify IP Configuration

The configuration file uses YAML syntax. Locate the section for your network interface (identified earlier). You'll typically find settings under the ethernets section with your interface name as a key.

**Here's an example configuration for a static IP:**

```shell
network:
  version: 2
  ethernets:
    [INTERFACE_NAME]:
      optional: true
      addresses: [NEW_IP_ADDRESS/SUBNET_MASK]
      routes:
        - to: [NETWORK_TRAFFIC]
          via: [GATEWAY_IP_ADDRESS]
      nameservers:
        addresses: [PRIMARY_DNS_IP_ADDR, SECONDARY_DNS_IP_ADDR]
```

Replace the placeholders with your desired values:

| Name                    | Description                      | Example     |
| :---------------------- | :------------------------------- | :---------- |
| `INTERFACE_NAME`        | name of the network interface    | `enp0s3`    |
| `NEW_IP_ADDRESS`        | new static IP address            | `10.0.2.17` |
| `SUBNET_MASK`           | Subnet Mask                      | `24`        |
| `NETWORK_TRAFFIC`       | Gateway for network traffic      | `default`   |
| `GATEWAY_IP_ADDRESS`    | specifies the gateway IP address | `10.0.2.1`  |
| `PRIMARY_DNS_IP_ADDR`   | primary DNS server               | `8.8.8.8`   |
| `SECONDARY_DNS_IP_ADDR` | Secondary DNS server             | `8.8.4.4`   |

#### 3. Save and Apply Changes:

- Save the modified Netplan configuration file in your editor.
- Apply the changes with the following command:

```shell
sudo netplan apply
```
