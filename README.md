# Hi, I'm Aldiyes! 👋

Iam student from [Sanata Dharma University](https://www.usd.ac.id/) with ID [205314132](https://belajar.usd.ac.id/user/profile.php?id=16421)

<p align="center">
  <img src="https://belajar.usd.ac.id/pluginfile.php/1/theme_moove/logo/1705975785/logo_usd.png" alt="Sanata Dharma University">
</p>

## 🔎 **Features**

- Setup Virtual Machine
- Installing Ubuntu Server
- Editing Netplan Configure
- Setting Hostname
- Clonig A Virtual Machine
- Port Forwarding
- Setup VM-1 to web server and VM-2 to db server
- Install postgresql for db server
- Configure postgresql
- Install nginx for web server
- Configure nginx

## 🛹 **Prerequisites**

- VirtualBox installed on your system. You can download it from the official website [here](https://www.virtualbox.org/wiki/Downloads)
- A recent Ubuntu Server ISO image. Download it from the Ubuntu website [here](https://releases.ubuntu.com/22.04.4/ubuntu-22.04.4-live-server-amd64.iso)

### ⚙️ **Setting Up the Virtual Machine**

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

### 🖥️ **Installing Ubuntu Server**

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

### 🌕 **Post-Installation (Optional)**

#### Update and Upgrade

Once booted into your new Ubuntu Server VM, connect to it using VirtualBox console by adding username and password that you created before. Update the package lists and upgrade any available packages using the following commands:

```shell
sudo apt update & sudo apt upgrade -y
```

## 🛜 **Editing Netplan Configuration**

#### 1. Locate Netplan Config File

The Netplan configuration file typically resides at /etc/netplan/00-installer-config.yaml (or a similar filename). Use your preferred text editor (e.g., nano or vim) to edit the file:

```shell
sudo nano /etc/netplan/00-installer-config.yaml
```

#### 2. Modify IP Configuration

The configuration file uses YAML syntax. Locate the section for your network interface (identified earlier). You'll typically find settings under the ethernets section with your interface name as a key.

**Here's an example configuration for a static IP:**

```tsx
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

## 🧒 **Setting Hostname with `hostnamectl`**

**_Permanent Hostname Change_**

#### 1. Set the hostname with `hostnamectl`:

```shell
sudo hostnamectl set-hostname <new_hostname>
```

replace `<new_hostname>` with your new `hostname`

#### 2. Update the /etc/hostname file:

```shell
sudo nano /etc/hostname
```

- Replace the existing hostname with your new hostname in this file.
- Save and close the file.

#### 3. (Optional) Update the /etc/hosts file (recommended for some applications):

```shell
sudo nano /etc/hosts
```

Edit the line that starts with 127.0.1.1. It should look like this:

```
127.0.1.1   localhost <old_hostname>
```

- Replace `<old_hostname>` with your new `hostname`.
- Save and close the file.

#### 4. Reboot your system for the changes to take full effect.

## 🖥️➡️🖥️ **Cloning a Virtual Machine with VirtualBox**

**_Cloning Process_**

#### 1. Select the VM

Open VirtualBox and choose the VM you want to clone from the list in the main window

#### 2. Right-Click and Clone

Right-click on the selected VM and select "Clone" from the context menu or you can press `ctrl+o`

#### 3. Set VM name and path

- **Name:** Provide a new name for the cloned VM. This helps differentiate it from the original.
- **Path:** Specify where you want to allocate the VM
- In clone type `choose full` clone and click `finish`

## 🦖 Configure VM

**_Virtual Machine 1_**
| Name | Value|
| :---------------- | :----------|
| `Username` | iyesss |
| `Hostname` | web-server |
| `IP address` | 10.0.2.17 |
| `Submask` | 24 |

**_Virtual Machine 2_**
| Name | Value |
| :---------------- | --------- |
| `Username` | iyesss |
| `Hostname` | db-server |
| `IP address` | 10.0.2.11 |
| `Submask` | 24 |

## ⚙️🛜 **Port Forwarding with NAT Network in VirtualBox**

NAT Network is a VirtualBox networking mode where the host machine acts as a router for the VM. The VM gets a private IP address on the internal network and cannot be directly accessed from the outside world. Port forwarding allows you to bridge this gap and expose specific services running inside your VM to external access.

#### **_Setting Up Port Forwarding_**

##### 1. Open VirtualBox Network Manager

Right-click on `Tools` in the VirtualBox list, click `Tools`and select `Network Manager`. Or you can press `ctrl+H` for shortcut

##### 2. Create New Nat Network

Navigate into NAT Network and click Create at the top. That will be create a new Nat Network

##### 3. Open Port Forwarding

Navigate into NAT Network and click Port Forwading bellow.

##### 4. Add New Rule

in IPv4 click the "+" button to add a new port forwarding rule

##### 5. Define Rule Details:

- **Name:** Provide a descriptive name for the rule (e.g., "Web Server").
- **Protocol:** Choose the protocol for the traffic (TCP or UDP).
- **Host Port:** Specify the port number on your host machine that will receive the incoming traffic (e.g., 80 for web traffic).
- **Guest IP:** Define IP Address for guest
- **Guest Port:** Define the port number inside your VM where you want to redirect the traffic (e.g., 80 for a web server running on the VM)

**_Here's an example configuration for Port Forwarding Rules:_**

| Name | Protocol | Host IP | Host Port | Guest IP  | Guest Port |
| ---- | -------- | ------- | --------- | --------- | ---------- |
| WEB  | TCP      |         | 2222      | 10.0.2.17 | 22         |
| DB   | TCP      |         | 2224      | 10.0.2.11 | 22         |

##### 6. Setting Up Network in VM

- Select `VM-1` and right click and then select `Settings`
- Navigate to Network
- Checked Enable Network Adapter and Attached to NAT Network
- And then press ok
- Do same thing to `VM-2`

#### **_Verification_**

To Verify your Port Forwarding is work, you can try accessing VM from your device using SSH

##### 1. Open `bash` or `powershell` from your device

##### 2. Make sure that your VM is run/start

##### 3. Follow this command to access your web-server VM:

```shell
ssh iyesss@localhost -p 2222
```

##### 4. Do same thing to access your db-server VM with different port (in this case, port 2224):

```shell
ssh iyesss@localhost -p 2224
```

##### 5. If it's work, you can access your VM from `bash` or `powershell` on your device and you can ping each VM with this command:

**_From VM-1_**

```shell
ping 10.0.2.11
```

**_From VM 2_**

```shell
ping 10.0.2.17
```
