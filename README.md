# Hi, I'm Aldiyes! 👋

Iam student from [Sanata Dharma University](https://www.usd.ac.id/) with ID [205314132](https://belajar.usd.ac.id/user/profile.php?id=16421)

<p align="center">
  <img src="https://belajar.usd.ac.id/pluginfile.php/1/theme_moove/logo/1705975785/logo_usd.png" alt="Sanata Dharma University">
</p>

## 🔎 **Documentation**

- [Prerequisites](#-prerequisites)
- [Virtual Macine](#%EF%B8%8F-setting-up-the-virtual-machine)
  - [Installing Ubuntu Server](#%EF%B8%8F-installing-ubuntu-server)
  - [Post-Installation (Optional)](#-post-installation-optional)
  - [Editing Netplan Configuration](#-editing-netplan-configuration)
  - [Setting Hostname with hostnamectl](#-setting-hostname-with-hostnamectl)
  - [Cloning a Virtual Machine with VirtualBox](#%EF%B8%8F%EF%B8%8F%EF%B8%8F-cloning-a-virtual-machine-with-virtualbox)
  - [Configure VM](#-configure-vm)
  - [Port Forwarding with NAT Network in VirtualBox](#%EF%B8%8F-port-forwarding-with-nat-network-in-virtualbox)
  - [Install And Configure PostgreSQL in VM 2 db-server](#-install-and-configure-postgresql-in-vm-2-db-server)
  - [Enable The Connection PostgreSQL To Remote Server](#%EF%B8%8F%EF%B8%8F%EF%B8%8F-enable-the-connection-postgresql-to-remote-server)
  - [Adding Rules Port Forwarding For PostgreSQL](#%EF%B8%8F-adding-rules-port-forwarding-for-postgresql)
  - [Install NGINX in VM 1 web-server](#-install-nginx-in-vm-1-web-server)
  - [Adding Rules Port Forwarding For NGINX](#%EF%B8%8F-adding-rules-port-forwarding-for-nginx)
  - [Install Node.js and npm](#-install-nodejs-and-npm)
  - [Connect VM-1 (WEB-Server) and VM-2 (DB-Server) using Prisma](#-%EF%B8%8F-connect-vm-1-web-server-and-vm-2-db-server-using-prisma)
  - [Run Next-Todos Project](#-run-next-todos-project)
  - [Install JDK](#-install-jdk)
  - [Create First Java Project](#-create-first-java-project)
- [Docker](#-docker)

## 🛹 **Prerequisites**

- VirtualBox installed on your system. You can download it from the official website [here](https://www.virtualbox.org/wiki/Downloads)
- A recent Ubuntu Server ISO image. Download it from the Ubuntu website [here](https://releases.ubuntu.com/22.04.4/ubuntu-22.04.4-live-server-amd64.iso)

## ⚙️ **Setting Up the Virtual Machine**

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

## 🦖 **Configure VM**

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

## 🏬 **Install And Configure PostgreSQL in VM 2 `db-server`**

#### **_Installation Steps:_**

##### 1. Update Package Lists:

```shell
sudo apt update
```

##### 2. Install [PostgreSQL](https://www.postgresql.org/download/linux/ubuntu/) Packages

- Automated repository configuration:

```shell
sudo apt install -y postgresql-common
sudo /usr/share/postgresql-common/pgdg/apt.postgresql.org.sh
```

- Import the repository signing key:

```shell
sudo apt install curl ca-certificates
sudo install -d /usr/share/postgresql-common/pgdg
sudo curl -o /usr/share/postgresql-common/pgdg/apt.postgresql.org.asc --fail https://www.postgresql.org/media/keys/ACCC4CF8.asc
```

- Create the repository configuration file:

```shell
sudo sh -c 'echo "deb [signed-by=/usr/share/postgresql-common/pgdg/apt.postgresql.org.asc] https://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
```

- Install the latest version of PostgreSQL:
  If you want a specific version, use 'postgresql-16' or similar instead of 'postgresql'

```shell
sudo apt -y install postgresql
```

##### 3. Start the PostgreSQL Service:

After installation, start the PostgreSQL service with:

```shell
sudo systemctl start postgresql
```

##### 4. Verify Service Status:

Use the following command to check if the PostgreSQL service is running:

```shell
sudo systemctl status postgresql
```

---

#### **_Configure PostgreSQL_**

##### 1. Switch user to postgres

```shell
sudo su - postgres
```

##### 2. Connect to the PostgreSQL server using the `psql` client utility

```shell
psql
```

##### 3. Add password into `posetgres` user:

```mysql
ALTER USER postgres WITH ENCRYPTED PASSWORD "<your_password>";
```

##### 4. Create user from the start with CREATEROLE and CREATEDB permissions

```mysql
CREATE ROLE <user_name> PASSWORD '<password>' NOSUPERUSER CREATEDB CREATEROLE INHERIT LOGIN;
```

| Command     | Description                                                                                                                    |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------ |
| NOSUPERUSER | The user being created does not have superuser rights (like the postgres user, recommended)                                    |
| CREATEDB    | The user being created can create databases which it will own                                                                  |
| CREATEROLE  | The user being created can create roles (logins) for objects it owns or has specific access to (like databases it has created) |
| INHERIT     | The user being created inherits some default options, optional                                                                 |
| LOGIN       | The user being created has the right to log in                                                                                 |

**Here's an example configuration for create user:**

```mysql
CREATE ROLE iyesss PASSWORD 'aldiyes17032002' NOSUPERUSER CREATEDB CREATEROLE INHERIT LOGIN;
```

##### 4. Check if user being created successfully

```mysql
\du
```

##### 5. Create database called next_todos_pgdb

```mysql
CREATE DATABASE next_todos_pgdb;
```

##### 6. Grant privileges for user `iyesss` to `next_todos_pgdb`

- Navigate inside next_todos_pgdb:

```mysql
\c next_todos_pgdb
```

- Grant privileges on database:

```mysql
GRANT ALL PRIVILEGES ON DATABASE next_todos_pgdb TO iyesss;
```

- Grant privileges for schema public:

```mysql
GRANT ALL ON SCHEMA public TO iyesss;
```

##### 7. Exit from postgres and login via `iyesss`

```mysql
\q
```

on postgress root:

```mysql
psql -h localhost -U iyesss -p 5432 -d next_todos_pgdb
```

## 🖥️↔️🖥️ **Enable The Connection PostgreSQL To Remote Server**

If you want to access PostgreSQL from remote server, you need make some changes inside `pg_hba.conf` and `postgresql.conf`. Here some step that you need to follow:

#### **_make some changes for `pg_hba.conf` and `postgresql.conf`_**

##### 1. Find `pg_hba.conf`:

By following this command will return `pg_hba.conf` located is:

```shell
sudo find / -name pg_hba.conf
```

##### 2. Locate `pg_hba.conf` file:

In this case, my `pg_hba.conf` is inside `/etc/postgresql/16/main`. Use your preferred text editor (e.g., `nano` or `vim`) to edit the file:

```shell
sudo nano /etc/postgresql/16/main/pg_hba.conf
```

##### 3. Find `# IPv4 local connections` and added some configure:

- scroll down until you find #listen_addresses
- add this configure bellow:
  |TYPE|DATABASE|USER|ADDRESS|METHOD|
  |----|-------|------|------|-----|
  |host|all|all|<your remote ip address>/<submask>|md5|

Example:
|TYPE|DATABASE|USER|ADDRESS|METHOD|
|----|-------|------|------|-----|
|host|all|all|10.0.2.17/24|md5|

IP Address `10.0.2.17/24` is my VM-1. After that, save and exit from nano.

##### 4. Find `postgresql.conf`:

By following this command will return `postgresql.conf` located is:

```shell
sudo find / -name postgresql.conf
```

##### 5. Locate `postgresql.conf` file:

In this case, my `pg_hba.conf` is inside `/etc/postgresql/16/main`. Use your preferred text editor (e.g., nano or vim) to edit the file:

```shell
sudo nano /etc/postgresql/16/main/postgresql.conf
```

##### 6. Find `listen_addresses` and make some change

- Scroll down until you find `#listen_addresses`
- Change `'localhost'` to your VM IP Address. In this case my IP Address is `10.0.2.11`.
- Make sure delete comment from listen_addresses (`#`). After that, save and exit

##### 7. restart postgresql:

After make some changes inside pg_hba configure and postgres configure, you need to resart the postgres:

```shell
sudo systemctl restart postgresql
```

## ⚙️🛜🏬 **Adding Rules Port Forwarding For PostgreSQL**

| Name   | Protocol | Host IP | Host Port | Guest IP    | Guest Port |
| ------ | -------- | ------- | --------- | ----------- | ---------- |
| WEB    | TCP      |         | 2222      | 10.0.2.17   | 22         |
| DB     | TCP      |         | 2224      | 10.0.2.11   | 22         |
| `PGDB` | `TCP`    |         | `5432`    | `10.0.2.11` | `5432`     |

## 📱 **Install NGINX in VM 1 `web-server`**

#### **_Installation_**

#### 1. Update package lists:

```shell
sudo apt update
```

#### 2. Install NGINX:

```shell
sudo apt install nginx
```

#### 3. Start NGINX:

```shell
sudo systemctl start nginx
```

#### 4. Enable NGINX to Start on Boot:

```shell
sudo systemctl enable nginx
```

#### **_Verification_**

For verify your NGINX is running or not, you can run this command:

```shell
curl localhost
```

If NGINX is running and configured to use the default port 80, then curl localhost should return the default NGINX welcome page, indicating successful verification

## ⚙️🛜📱 **Adding Rules Port Forwarding For NGINX**

| Name    | Protocol | Host IP | Host Port | Guest IP    | Guest Port |
| ------- | -------- | ------- | --------- | ----------- | ---------- |
| WEB     | TCP      |         | 2222      | 10.0.2.17   | 22         |
| DB      | TCP      |         | 2224      | 10.0.2.11   | 22         |
| PGDB    | TCP      |         | 5432      | 10.0.2.11   | 5432       |
| `NGINX` | `TCP`    |         | `3000`    | `10.0.2.17` | `80`       |

## 👽 **Install Node.js and npm**

#### **_Installation_**

#### 1. Update and Install Dependencies:

```shell
sudo apt-get update && sudo apt-get install -y ca-certificates curl gnupg
```

#### 2. Import NodeSource GPG Key:

```shell
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg
```

#### 3. Set Node.js Version and Add Repository:

```shell
NODE_MAJOR=20
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | sudo tee /etc/apt/sources.list.d/nodesource.list
```

#### 4. Update Repository and Install Node.js:

```shell
sudo apt-get update && sudo apt-get install nodejs -y
```

#### **_Verification_**

**_For both methods:_**

#### 1. Check Node.js version:

```shell
node --version
```

#### 2. Check npm version:

```shell
npm --version
```

Since Apr 10, 2024, npm latest version is 10.5.2. If you want to update your npm, make sure to run this command:

```shell
sudo npm install -g npm@latest
```

After that, check one more time your npm version

## 🏬 ↔️📱 **Connect VM-1 (WEB-Server) and VM-2 (DB-Server) using [Prisma](https://www.prisma.io/docs/getting-started/quickstart)**

This guide outlines how to establish a connection between VM-1, acting as your `web server`, and VM-2, hosting your `db server`, using Prisma. For `web server`, we use existing project from this [repostory](https://github.com/aldiyespaskalisbirta/next-todos) for NextJs project. **Make sure both of VM is running/start**.

#### **On VM-1 (`WEB-Server`)**

#### 1. Install PM2 Globally:

```shell
sudo npm install pm2@latest -g
```

#### 2. Use Git to Fetch Next.js Project from [GitHub](https://github.com/):

```shell
git clone https://github.com/aldiyespaskalisbirta/next-todos.git
```

#### 3. Copy .env-example into .env and install package list for NextJs project:

```shell
cd next-todos
cp .env.example .env
npm install
```

#### 4. Configure .env file:

```shell
sudo nano .env
```

For details on next-todos project, please visit the [Documentation](https://github.com/aldiyespaskalisbirta/next-todos) for Step-by-Step Guide.

#### 5. Generate And Migrate Shema Prisma:

```shell
npx prisma generate
npx prisma migrate dev
```

#### 6. Run Next.js Project with PM2 and Serve with NGINX:

```shell
npm run build
pm2 start npm --name "next_todos_app" -- start
```

#### 7. Configure PM2 to Start Automatically:

```shell
pm2 save
pm2 startup
```

#### 8. Configure Nginx to Serve the Next.js Application:

- Create Nginx Configuration File:

```shell
sudo nano /etc/nginx/sites-available/next_todos
```

- Configure Nginx:

```shell
server {
    listen 80;
    server_name 10.0.2.17;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

```

- Create a Symbolic Link to Enable the Configuration:

```shell
sudo ln -s /etc/nginx/sites-available/nextjs-app /etc/nginx/sites-enabled/
```

- Change Nginx Configure for default file:

```shell
sudo nano /etc/nginx/sites-available/default
```

replace `listen` port `80` to other port configure (e.g., 123). After that save and exit.

- Test Nginx Configuration:

```shell
sudo nginx -t
```

- Restart Nginx to Apply Changes:

```shell
sudo systemctl restart nginx
```

## ⚡ **Run Next-Todos Project**

#### 1. Navigate to next-todos folder

```shell
cd next-todos
```

#### 2. Run the project:

```shell
npm run dev
```

#### 3. Open browser and search for `localhost:3000`

## 🍵 **Install [JDK](https://www.digitalocean.com/community/tutorials/how-to-install-java-with-apt-on-ubuntu-22-04)**

This is the recommended method for most users as it leverages the pre-packaged OpenJDK available through Ubuntu's repositories. OpenJDK is a free and open-source implementation of the Java platform.

#### **_Instalation_**

#### 1. Update package lists:

```shell
sudo apt update
```

#### 2. Install the default JDK:

```shell
sudo apt install default-jdk
```

#### **_Verification_**

For verfiy the installation, you can heck the installed Java version:

```shell
java -version
```

## 🧾🍵 **Create First Java Project**

#### 1. Create a Project Directory:

Open a terminal on your server and create a directory to hold your project files

```shell
mkdir java-projects
cd java-projects
```

#### 2. Create a Java Class:

Use a text editor (like `nano` or `vim`) to create a new file named `HelloWorld.java` inside your project directory:

```shell
sudo nano HelloWorld.java
```

Paste the following basic code into the file:

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

#### 3. Compile the Java Class:

Navigate to the project directory in your terminal and compile the `.java` file into a `.class` file using the `javac` compiler:

```shell
javac HelloWorld.java
```

This will create a `HelloWorld.class` file if compilation is successful.

#### 4. Run the Java Program:

Execute the compiled program using the `java` command followed by the class name:

```shell
java HelloWorld
```

You should see the output "Hello, World!" printed on your terminal.

## 🐳 **Docker**

**_There is no documentation yet_**
