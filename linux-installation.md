---
title: Linux Installation
---

[Home](index.md) | [Getting Started](getting-started.md) | [Enable AI](enable-ai.md) | [Local AI Model](local-ai-model.md) | [Impala + Big Data](omniscope-impala-big-data.md) | [Concurrent Jobs](concurrent-workflow-jobs.md) | [In-Memory](running-in-memory.md)

---

# Linux Installation

## Installing using a cloud marketplace

If you're installing on a VM in the Amazon AWS EC2 or Microsoft Azure clouds, it's much easier — almost one-click — to use our marketplace listing:

- [Omniscope Evo on Amazon AWS Marketplace](https://aws.amazon.com/marketplace/pp/prodview-bs76pbnvfj4lk)
- [Omniscope Evo on Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/visokiouklimited1661943152060.visokio-omniscope?tab=PlansAndPrice)

You can install manually on a Linux cloud VM if you prefer. If so, read on...

## Installing manually on a Linux server

Before starting, if updating an existing installation, be sure to check [this guide](https://help.visokio.com/support/solutions/articles/42000055078-upgrading-omniscope-to-a-latest-version).

Omniscope is available for common Linux distributions as both a desktop application and a headless server, whether in-cloud or on-premise. It requires a recent version of any popular Linux distributions on intel/amd 64-bit hardware. We've tested **Ubuntu** Desktop & Server since 12.04 (latest LTS is recommended), and the **Amazon** EC2 AMI.

> _N.B. Omniscope Classic desktop interface is not supported on Linux._
> _These instructions are tailored to Ubuntu servers._

## Installation on a Linux server via terminal

This describes how to install Omniscope on a 64-bit Linux server without a graphical desktop environment, such as Ubuntu Server via an SSH terminal session.

### 1. Read and approve the EULA

Read and approve the [EULA](http://kb.visokio.com/eula) before proceeding.

### 2. Log in via SSH

Log in via SSH as the user account you wish to use for Omniscope.

This account will be the activated account, and the account that runs the Omniscope Server service. You should arrive in your home directory (`~/`, or `/home/username`). These instructions assume you remain in this directory.

### 3. Stop and remove any previous installation

Skip this section on a new server.

To stop an existing service, execute:

```bash
sudo service omniscope-server stop
```

or

```bash
kill -15 -f com.visokio
```

Once all Omniscope processes have been stopped, remove the installation. (But first, if you previously customised the `visokio-omniscope/_launch.sh` file, copy it and restore it after extracting the new version.)

To permanently remove the existing version:

```bash
rm -rf ~/visokio-omniscope
```

Alternatively, rename the folder so it is available for restoring later if you run into issues with the new version:

```bash
mv visokio-omniscope visokio-omniscope-old
```

Either way, make sure that neither `VisokioOmniscope-Linux.tgz` file nor a `visokio-omniscope` folder exist in your home directory.

### 4. Download the Linux "tgz" to your home directory

To download the latest Rock build (more stable, less frequently updated):

```bash
wget https://visokio.com/downloads/Omniscope/Rock/Linux -O VisokioOmniscope-Linux.tgz
```

Alternatively, to download a specific daily build, visit the Omniscope [download page](https://visokio.com/evo/). Choose the version you want, and copy the Linux link URL (a TGZ file). Then enter:

```bash
wget <url>
```

You should now have `VisokioOmniscope-Linux.tgz` in your current (home) directory as shown by `ls`.

### 5. Extract files

```bash
tar zxf VisokioOmniscope-Linux.tgz
ls
```

You should now have `visokio-omniscope` in your current (home) directory as shown by `ls`.

```bash
rm VisokioOmniscope-Linux.tgz
```

### 6. Move to chosen installation location

You can place the `visokio-omniscope` folder anywhere, but we recommend in your home directory. This does not require superuser privileges/sudo, and is only available to the user who installed it. **It should already be in this location.**

For example: `/users/ubuntu/visokio-omniscope`

Omniscope launch command: `~/visokio-omniscope/omniscope-evo.sh`

### 7. Activate and set up Omniscope from the command line

**Pre-requisite:**

On some Linux distributions, the Omniscope setup command below might report an error, because some of the fonts are not installed on the system. To prevent this problem, first install the package **fontconfig**. On Ubuntu and Debian based distributions:

```bash
sudo apt-get update && sudo apt -y install fontconfig
```

Or, if your system uses Yum (e.g. CentOS):

```bash
sudo yum -y install fontconfig
```

You should use the **Omniscope Setup Console** to activate and configure the server with a minimal configuration for web-based administration:

```bash
~/visokio-omniscope/omniscope-evo.sh -setup
```

This has an interactive console-based menu. Use this to activate with your licence key (contact support@visokio.com), then run again to configure an external web server and admin account.

**Or, for a fully automated/scripted setup:**

```bash
~/visokio-omniscope/omniscope-evo.sh -autosetup LICENSE_KEY ADMIN_PASSWORD
```

### 8. (Optional) Running Omniscope as a service (systemd)

It is possible to install Omniscope to run as a system service using systemd.

Create and edit the service description:

```bash
sudo nano /etc/systemd/system/omniscope-headless.service
```

Paste the following content:

```ini
[Unit]
Description=Omniscope Server
After=syslog.target network.target remote-fs.target nss-lookup.target

[Service]
Type=simple
User=<INSERT OMNISCOPE USER HERE>
WorkingDirectory=<INSERT OMNISCOPE INSTALL DIR HERE>
ExecStart=<INSERT OMNISCOPE INSTALL DIR HERE>/omniscope-evo-headless.sh
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Replace `<INSERT OMNISCOPE INSTALL DIR HERE>` with the full path where Omniscope is installed (e.g. `/home/ubuntu/visokio-omniscope`) and `<INSERT OMNISCOPE USER HERE>` with the username used to run Omniscope (e.g. `ubuntu`). Save the file and exit (Ctrl+X, Y, Enter).

To enable the service:

```bash
sudo systemctl enable omniscope-headless.service
```

You can check the state of the service using:

```bash
sudo systemctl status omniscope-headless.service
```

If using the service, you can later use the update script bundled by recent versions of Omniscope to smoothly update with minimal downtime:

```bash
./visokio-omniscope/update-omniscope-service-by-url.sh <url>
```

where `<url>` is the tgz file download link for the desired version, copied from the [download page](https://visokio.com/omniscope/).

### 9. Install MonetDB (required)

On Linux, Omniscope requires **MonetDB** to run correctly on your machine.

If you try to start Omniscope without MonetDB, you will find an error message in the log saying "MonetDb server not found on your Linux OS."

Omniscope relies on a particular version of the packages and it will show a warning in the app if the packages are not configured correctly.

At the time of writing the best version of the package is **11.49.15**, but any similar patch version such as **11.49.17** or **11.49.11** are equally suitable.

> _Note: both 11.43 and 11.49 are fully supported, although we currently bundle 11.43 on Windows and Mac._

#### Ubuntu

On **Ubuntu** these are the commands to execute in sequence:

a) Determine your OS release suite name, for example "noble":

```bash
lsb_release -cs
```

Create/edit a file `/etc/apt/sources.list.d/monetdb.list`:

```bash
sudo nano /etc/apt/sources.list.d/monetdb.list
```

With the following contents (replace `<SUITE>` with your suite name, e.g. `noble`):

```
deb https://dev.monetdb.org/downloads/deb/ <SUITE> monetdb
deb-src https://dev.monetdb.org/downloads/deb/ <SUITE> monetdb
```

b) Install the MonetDB GPG public key:

```bash
wget --output-document=- https://www.monetdb.org/downloads/MonetDB-GPG-KEY | sudo apt-key add -
```

c) Update apt:

```bash
sudo apt update
```

d) Install MonetDB/SQL:

```bash
sudo apt -y install monetdb5-sql=11.49.* monetdb5-server=11.49.* monetdb-client=11.49.*
```

If this fails because the given version is no longer available, install the latest available major version instead:

```bash
# Try this only if the above commands fail
sudo apt -y install monetdb5-sql monetdb5-server monetdb-client
```

> _If the installed MonetDB version is not officially supported, Omniscope may warn about this. It is likely to be compatible but should be tested against a typical report, and the warning can be disabled in Admin._

e) Add users who are allowed to run a database server to the monetdb group:

```bash
sudo usermod -a -G monetdb $USER
```

#### CentOS

On **CentOS** run the following commands:

```bash
sudo yum install https://dev.monetdb.org/downloads/epel/MonetDB-release-epel.noarch.rpm
sudo rpm --import https://dev.monetdb.org/downloads/MonetDB-GPG-KEY
sudo yum install MonetDB-SQL-server5-11.49.15 MonetDB5-server-11.49.15 MonetDB-client-11.49.15
sudo usermod -a -G monetdb $USER
```

You may need to tailor the version number to a later version if 11.49.15 is no longer available. More installation instructions can be found at [monetdb.org/Downloads](https://www.monetdb.org/Downloads).

### 10. Install other packages (recommended)

Update package lists:

```bash
sudo apt update
```

Install jblas:

```bash
sudo apt -y install jblas
```

On older distributions, instead install libfortran3:

```bash
sudo apt -y install libfortran3
```

#### Docker

You may also wish to install Docker. This will allow Omniscope to run custom blocks (installed automatically from our extensive [community blocks repository](https://github.com/visokio/omniscope-custom-blocks)) in a secure isolated environment, also removing the complexity of managing and installing further Python, R and system dependencies.

Follow the [Docker installation instructions](https://docs.docker.com/desktop/install/linux/), e.g. [for Ubuntu](https://docs.docker.com/desktop/install/linux/ubuntu/).

Later on, in the Omniscope web interface, visit Admin > R/Python & Blocks Library, and change the environment to Docker. Restart Omniscope after making this change.

### 11. (Optional) Running Omniscope on privileged ports

By default, you'll be running as an unprivileged user, with Omniscope defaulting to serving HTTP over port 8080. To allow Omniscope to serve on privileged ports (under 1024) such as 80 and 443, install and use authbind:

```bash
sudo apt-get install authbind
```

Create an empty file with the same name as the port Omniscope will be using:

```bash
sudo touch /etc/authbind/byport/80
sudo touch /etc/authbind/byport/443
```

Change the owner to the user running Omniscope:

```bash
sudo chown $USER /etc/authbind/byport/80
sudo chown $USER /etc/authbind/byport/443
```

Change the file permissions to 500:

```bash
sudo chmod 500 /etc/authbind/byport/80
sudo chmod 500 /etc/authbind/byport/443
```

Start Omniscope headless using authbind:

```bash
authbind --deep visokio-omniscope/omniscope-evo-headless.sh
```

To run Omniscope using authbind as a service, create a new script in the visokio-omniscope folder (e.g. `omniscope-headless-authbind.sh`):

```bash
#!/bin/bash
/usr/bin/authbind --deep ./omniscope-evo-headless.sh
```

Make the new file executable:

```bash
chmod +x visokio-omniscope/omniscope-headless-authbind.sh
```

Edit `/etc/systemd/system/omniscope-headless.service`, changing ExecStart to refer to the `omniscope-headless-authbind.sh` script.

You will now need to change the Omniscope configured port from the default of 8080 using the Setup Console:

```bash
~/visokio-omniscope/omniscope-evo.sh -setup
```

### 12. Configure and explore Omniscope in your browser

You should already have used the Omniscope Setup Console to activate, enable the external web server, and configure an admin username and password. Without this, you cannot configure Omniscope on a headless server.

When Omniscope has started (using `~/visokio-omniscope/omniscope-evo.sh`, or as a service), visit `http://your-server:port/` in your browser, where port is by default 8080 on Linux (unless you customised it).

> _If using a cloud VM, you may need to edit the cloud firewall rules at this point to allow the configured web server ports (e.g. 8080) from your IP address._

From there, log in as the user you created in the setup console (typically "admin") and the password you previously configured, in order to have full permissions server-wide to administer every aspect of the server. Follow the "Admin" section on the left to configure the server, or edit permissions in a folder using the 3 dots menu, top-right of the page.

> _If installing on Linux using a desktop environment, you can skip the section "Activate and set up Omniscope from the command line" since you will be able to use the desktop browser to perform initial configuration steps such as activation._

## Linux commands reference

### Omniscope Evo

```bash
~/visokio-omniscope/omniscope-evo.sh
```

Launches the Omniscope Evo application. If launched from an interactive desktop session, the app will run in background and be available in the OS system tray. Your default browser will open pointing to the Omniscope Evo welcome page. If launched from a headless terminal console (e.g. via SSH or Ubuntu Server), the app will run in headless mode.

```bash
~/visokio-omniscope/omniscope-evo-headless.sh
```

Same as `omniscope-evo.sh`, but explicitly launches in headless mode. This is necessary only on systems with desktop environments when you wish to run Omniscope as a headless process.

### Setup commands

```bash
~/visokio-omniscope/omniscope-evo.sh -setup
```

Launches the Omniscope Setup Console for terminal-based configuration.

```bash
~/visokio-omniscope/omniscope-evo.sh -autosetup LICENSE_KEY ADMIN_PASSWORD HTTP_PORT HTTPS_PORT
```

Launches the Setup Console in automated mode for silently configuring a headless server. The last 2 arguments are optional (defaults: 8080 and 8443).

Examples:

```bash
~/visokio-omniscope/omniscope.sh -autosetup XXXX-XXXX-XXXX-XXXX mypassword
~/visokio-omniscope/omniscope.sh -autosetup XXXX-XXXX-XXXX-XXXX mypassword 80 443
```

### Activation commands

```bash
~/visokio-omniscope/omniscope-evo-headless.sh -activate key
```

Activates the current user account with the key specified (format: `xxxx-xxxx-xxx-xxxx`). Requires an active internet connection.

```bash
~/visokio-omniscope/omniscope-evo-headless.sh -reactivate
```

Renews and refreshes the licence for the current user account.

```bash
~/visokio-omniscope/omniscope-evo-headless.sh -activationinfo
```

Displays the licensing state of the current user account.

```bash
~/visokio-omniscope/omniscope-evo-headless.sh -deactivate
```

Deactivates the current user account and makes the licence key available for use on another system.

### Service commands

```bash
sudo systemctl start omniscope-headless.service
sudo systemctl status omniscope-headless.service
sudo systemctl stop omniscope-headless.service
sudo systemctl restart omniscope-headless.service
```
