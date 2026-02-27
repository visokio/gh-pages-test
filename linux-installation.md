---
title: Linux Installation
breadcrumb: Installation & Deployment
---

## Installing using a cloud marketplace

If you're installing on a VM in the Amazon AWS EC2 or Microsoft Azure clouds, it's much easier - almost one-click - to use our marketplace listing

See:

- [Omniscope Evo on Amazon AWS Marketplace](https://aws.amazon.com/marketplace/pp/prodview-bs76pbnvfj4lk)
- [Omniscope Evo on Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/visokiouklimited1661943152060.visokio-omniscope?tab=PlansAndPrice)

You can install manually on a linux cloud VM if you prefer. If so, read on...

## Installing manually on a Linux server

Before starting, if updating an existing installation, be sure to check [this guide](https://help.visokio.com/support/solutions/articles/42000055078-upgrading-omniscope-to-a-latest-version).

Omniscope is available for common Linux distributions as both a desktop application and a headless server, whether in-cloud or on-premise.

It requires a recent version of any popular Linux distributions on intel/amd 64-bit hardware.  We've tested **Ubuntu** Desktop & Server since 12.04 (latest LTS is recommended), and the **Amazon** EC2 AMI.

*N.B. Omniscope Classic desktop interface is not supported on Linux.*

*These instructions are tailored to Ubuntu servers.*

## Installation on a Linux server via terminal

This describes how to install Omniscope on a 64-bit Linux server without a graphical desktop environment, such as Ubuntu Server via an SSH terminal session.

### 1. Read and approve the EULA

**Read and approve** the [EULA](http://kb.visokio.com/eula) before proceeding.

### 2. Log in via SSH

**Log in via SSH** as the user account you wish to use for Omniscope.

This account will be the activated account, and the account that runs the Omniscope Server service.  You should arrive in your home directory (~/, or /home/username).  These instructions assume you remain in this directory.

### 3. Stop and remove any previous installation

Skip this section in a new server.

To stop an existing service, execute:

```bash
sudo service omniscope-server stop
```

or

```bash
kill -15 -f com.visokio
```

Once all Omniscope processes have been stopped, remove the installation.

(But first, if you previously customised the `visokio-omniscope/_launch.sh` file, copy it and restore it after extracting the new version.)

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

Alternatively, to download a specific daily build, visit the Omniscope [download page](https://visokio.com/evo/).
Choose the version you want, and copy the Linux link URL (a TGZ file). Then enter: `wget <`*`url`*`>`

You should now have "VisokioOmniscope-Linux.tgz" in your current (home) directory as shown by `ls`.

### 5. Extract files

```bash
tar zxf VisokioOmniscope-Linux.tgz
ls
```

You should now have "visokio-omniscope" in your current (home) directory as shown by ls.

```bash
rm VisokioOmniscope-Linux.tgz
```

### 6. Move to chosen installation location

You can place the "visokio-omniscope" folder anywhere, but we recommend in your home directory. This does not require superuser privileges/sudo, and is only available to the user who installed it. **It should already be in this location.**

For example: `/users/ubuntu/visokio-omniscope`

Omniscope launch command: `~/visokio-omniscope/omniscope-evo.sh`

### 7. Activate and set up Omniscope from the command line

*Pre-requisite:*

On  some linux distributions, the Omniscope setup command below might report an error, because some of the fonts are not installed on the system. To prevent this problem, first install the package "**fontconfig**". On Ubuntu and Debian based distributions you can solve the issue by installing running the command:

```bash
sudo apt-get update && sudo apt -y install fontconfig
```

Or, if your system uses Yum (e.g. CentOS, it's:

```bash
sudo yum -y install fontconfig
```

You should use the Omniscope Setup Console to activate and configure server with a minimal configuration for web-based administration:

```bash
~/visokio-omniscope/omniscope-evo.sh -setup
```

This has an interactive console-based menu. Use this to activate with your licence key (contact support@visokio.com), then run again to configure an external web server and admin account.

**Or, for a fully automated/scripted setup:**

```bash
~/visokio-omniscope/omniscope-evo.sh -autosetup LICENSE_KEY ADMIN_PASSWORD
```

### 8. (Optional) Running Omniscope as a service (systemd)

It is possible to install Omniscope to run as a system service. This section will describe how to use install omniscope-headless.sh as a service. Similar instructions can be followed for other launching scripts or service managers.

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

replacing `<INSERT OMNISCOPE INSTALL DIR HERE>` with the full path where omniscope is installed (e.g. `/home/ubuntu/visokio-omniscope`) and `<INSERT OMNISCOPE USER HERE>` with the username used to run omniscope (e.g. `ubuntu`). Save the file and exit. (Ctrl+X, Y, Enter).

To enable the service use the command:

```bash
sudo systemctl enable omniscope-headless.service
```

You can check the state of the service, and retrieving the service logs using [systemctl](https://www.freedesktop.org/software/systemd/man/systemctl.html), e.g.:

```bash
sudo systemctl status omniscope-headless.service
```

If using the service, you can later use the update script bundled by recent versions of Omniscope to smoothly update Omniscope with minimal downtime:

```bash
./visokio-omniscope/update-omniscope-service-by-url.sh <url>
```

where `<url>` is the tgz file download link for the desired version, copied from the [download page](https://visokio.com/omniscope/).

### 9. Install MonetDB (required)

On Linux Omniscope requires "**[MonetDB](#monetdb)**" to be present to run correctly on your machine.

If you try to start Omniscope without MonetDB, you will find an error message in the log saying "MonetDb server not found on your Linux OS."

Omniscope relies on a particular version of the packages and it will show a warning in the app if the packages are not configured correctly.

For example, at the time of correct the best version of the package is **11.49.15**, but any similar patch version such as **11.49.17** or **11.49.11** are equally suitable.

*Note: both 11.43 and 11.49 are fully supported, although we currently bundle 11.43 on Windows and Mac.*

#### Ubuntu

On **Ubuntu** these are the commands to execute in sequence to install the MonetDB packages required by Omniscope:

a) Determine your OS release suite name, for example "noble". This command will display it:

```bash
lsb_release -cs
```

Create/edit a file `/etc/apt/sources.list.d/monetdb.list`:

```bash
sudo nano /etc/apt/sources.list.d/monetdb.list
```

... with the following contents (be sure to replace `<SUITE>` with, e.g. noble):

```
deb https://dev.monetdb.org/downloads/deb/ <SUITE> monetdb

deb-src https://dev.monetdb.org/downloads/deb/ <SUITE> monetdb
```

b) Then issue the following command to install the MonetDB GPG public key:

```bash
wget --output-document=- https://www.monetdb.org/downloads/MonetDB-GPG-KEY | sudo apt-key add -
```

c) After this, you can use apt to install the MonetDB packages. First run:

```bash
sudo apt update
```

d) To install MonetDB/SQL, use the command:

```bash
sudo apt -y install monetdb5-sql=11.49.* monetdb5-server=11.49.* monetdb-client=11.49.*
```

(If this fails, it's possible that the given version is no longer available. Instead install the latest available major version of MonetDb, which may not be verified for compatibility with Omniscope, but may be the only option on your current operating system version:

```bash
# Try this only if the above commands fail.
sudo apt -y install monetdb5-sql monetdb5-server monetdb-client
```

If the installed monetdb version is not officially supported, Omniscope may warn about this. It is likely to be compatible and but should be tested against a typical report, and the warning can be disabled in Admin.

On older Linux distributions, 11.43 is also an acceptable option if available.)

e) Add any users who are allowed to run a database server to the monetdb group, which was automatically created in the previous step:

```bash
sudo usermod -a -G monetdb $USER
```

#### CentOS

On **CentOS** (extracted from [this guide](https://www.monetdb.org/downloads/epel/)) these are the sequence of commands to run to install the packages with the right version:

```bash
sudo yum install https://dev.monetdb.org/downloads/epel/MonetDB-release-epel.noarch.rpm

sudo rpm --import https://dev.monetdb.org/downloads/MonetDB-GPG-KEY

sudo yum install MonetDB-SQL-server5-11.49.15 MonetDB5-server-11.49.15 MonetDB-client-11.49.15

sudo usermod -a -G monetdb $USER
```

You may need to tailor the version number to a later version if 11.49.15 is no longer available, or use 11.43.27 or similar. See comments for Ubuntu. More installation instructions can be found here [https://www.monetdb.org/Downloads](https://www.monetdb.org/Downloads)

### 10. Install other packages (recommended)

Instructions for Ubuntu/Debian. First, update package lists:

```bash
sudo apt update
```

Second, install jblas:

```bash
sudo apt -y install jblas
```

*On older distributions instead install libfortran3:*

```bash
sudo apt -y install libfortran3
```

#### Docker

You may also wish to install Docker. This will allow Omniscope to run custom blocks (installed automatically from our extensive [community blocks repository](https://github.com/visokio/omniscope-custom-blocks)) in a secure isolated environment, also removing the complexity of managing and installing further python, R and system dependencies.

Follow the [Docker installation instructions](https://docs.docker.com/desktop/install/linux/), e.g. [for ubuntu](https://docs.docker.com/desktop/install/linux/ubuntu/#install-docker-desktop).

Later on, in the Omniscope web interface, visit Admin > R/Python & Blocks Library, and change the environment to Docker. Restart Omniscope after making this change.

### 11. (Optional) Running Omniscope on privileged ports

By default, you'll be running as an unprivileged user, with Omniscope defaulting to serving http over port 8080. To allow Omniscope to serve on privileged ports (under 1024) such as 80 and 443, you need to install and use authbind:

```bash
sudo apt-get install authbind
```

next, create an empty file with the same name as the port Omniscope will be using in `/etc/authbind/byport/`. For example to use port 80 (http) and 443 (https):

```bash
sudo touch /etc/authbind/byport/80
sudo touch /etc/authbind/byport/443
```

Change the owner of the files to the user running omniscope (replace `$USER` with the name of the user that will run Omniscope, if different to your current user)

```bash
sudo chown $USER /etc/authbind/byport/80
sudo chown $USER /etc/authbind/byport/443
```

Change the files permissions to 500:

```bash
sudo chmod 500 /etc/authbind/byport/80
sudo chmod 500 /etc/authbind/byport/443
```

finally start omniscope-headless using authbind:

```bash
authbind --deep visokio-omniscope/omniscope-evo-headless.sh
```

*Alternatively, you could use [iptables](https://www.frozentux.net/iptables-tutorial/iptables-tutorial.html#REDIRECTTARGET) or [privbind](http://manpages.ubuntu.com/manpages/bionic/man1/privbind.1.html)*

To run omniscope using authbind as a service, the easiest solution is to create a new script in the visokio-omniscope folder with the content (called for example omniscope-headless-authbind.sh):

```bash
#!/bin/bash
/usr/bin/authbind --deep ./omniscope-evo-headless.sh
```

Depending on your distribution authbind might be installed on a different location. You can find where authbind binaries are located on you system with the command: `which authbind`

Make the new file executable via:

```bash
chmod +x visokio-omniscope/omniscope-headless-authbind.sh
```

Edit the file `/etc/systemd/system/omniscope-headless.service` (as previously edited/created), by changing the `ExecStart` to refer to the `omniscope-headless-authbind.sh`.

You will now need to change the Omniscope configured port from the default of 8080. Use the Omniscope Setup Console to do that (as done previously when activating):

```bash
~/visokio-omniscope/omniscope-evo.sh -setup
```

Finally, you'll need to start Omniscope!

If using a service, it's: `sudo systemctl start omniscope-headless.service`

Otherwise: `~/visokio-omniscope/omniscope-evo.sh`

### 12. Configure and explore Omniscope in your browser

**Configure and explore Omniscope in your browser**

Earlier, you should already have used the Omniscope Setup Console to activate, enable the external web server, and configure an admin username and password. Without this, you cannot configure Omniscope on a headless server (a server without a desktop environment).

As such, when Omniscope has started (using `~/visokio-omniscope/omniscope-evo.sh`, or as a service), you should be able to visit `http://your-server:port/` in your browser, where `port` is by default 8080 on Linux (unless you customised it in earlier section about privileged ports).

> *If using a cloud VM, you may need to edit the cloud firewall rules at this point to allow the configured web server ports (e.g. 8080) from your IP address so you can continue to configure Omniscope using the web interface.*

From there, you can log in as as the user you created in the setup console (typically "admin") and the password you previously configured, in order to have full permissions server-wide to administer every aspect of the server. Follow the "Admin" section on the left to configure the server, or edit permissions in a folder using the 3 dots menu, top-right of the page.

> *If installing on Linux using a desktop environment, you can skip the section "Activate and set up Omniscope from the command line" since you will be able to use the desktop browser to perform initial configuration steps such as activation.*

In some corporate environments, you may now need to [import trusted certificates into Omniscope's cacerts file](https://help.visokio.com/support/solutions/articles/42000101684-import-a-trusted-root-certificate-into-omniscope-s-cacerts-file).

## Linux commands

These terminal commands assume you've installed into your home directory.

### Omniscope Evo

```bash
~/visokio-omniscope/omniscope-evo.sh
```

> Launches the new Omniscope Evo application.  If launched from an interactive desktop session, the app will be running in background and available in the OS system tray. Your system default browser will open up pointing to the Omniscope Evo welcome page. Otherwise (if launched from a headless terminal console, such as via SSH or from Ubuntu Server), the app will run in headless mode.

```bash
~/visokio-omniscope/omniscope-evo-headless.sh
```

> Same as omniscope-evo.sh, but explicitly launches Omniscope Evo application in headless mode. This is necessary only on systems with desktop environments, when you wish to execute Omniscope as a headless process. It is implicit on servers without a graphical interface. You will need to access Omniscope from a browser on a different machine, and will need to have set up Omniscope already via the terminal using the Omniscope Setup Console.

### Setup commands

```bash
~/visokio-omniscope/omniscope-evo.sh -setup
```

> Launches the Omniscope Setup Console, for terminal-based configuration of the bare minimum needed to begin with an Omniscope headless server. See above. An interactive console-based menu will be shown, where you can activate, refresh, deactivate and setup, and see summary information about the installation.

```bash
~/visokio-omniscope/omniscope-evo.sh -autosetup LICENSE_KEY ADMIN_PASSWORD HTTP_PORT HTTPS_PORT
```

> Launches the Omniscope Setup Console in automated mode for silently configuring the minimum needed to begin with an Omniscope headless server, e.g. for use from a cloud VM init script. It activates using the license key given, enables the external web server, and creates an 'admin' user account with the password given. The last 2 arguments are optional. If not specified, the current values are left unchanged (defaults being 8080 and 8443).

Examples:

```bash
~/visokio-omniscope/omniscope.sh -autosetup XXXX-XXXX-XXXX-XXXX mypassword
~/visokio-omniscope/omniscope.sh -autosetup XXXX-XXXX-XXXX-XXXX mypassword 80 443
```

### Activation commands

Use appropriate start script (see above). Alternatively, use the Omniscope Setup Console (above). Please note even if you have a Omniscope Classic licence, activation commands will still work with your licence.

```bash
~/visokio-omniscope/omniscope-evo-headless.sh -activate key
```

> Use when unlicensed.  Activates the current user account with the key specified. The key must be in the format xxxx-xxxx-xxx-xxxx. Requires an active internet connection.  Non-interactive; check the terminal console for status/error messages.

```bash
~/visokio-omniscope/omniscope-evo-headless.sh -reactivate
```

> Use when activated.  Renews and refreshes the license for current user account.  If Visokio have extended or upgraded your license, you will need to do this.  Requires an active internet connection.  Non-interactive; check the terminal console for status/error messages.

```bash
~/visokio-omniscope/omniscope-evo-headless.sh -activationinfo
```

> Displays the licensing state of the current user account. Non-interactive; check the terminal console for status/error messages.

```bash
~/visokio-omniscope/omniscope-evo-headless.sh -deactivate
```

> Use when activated.  Deactivates the current user account and makes the license key available to activate on another system (up to 3 transfers, depending on license - not available for trial keys). Requires an active internet connection.  Non-interactive; check the terminal console for status/error messages.

### Other commands

```bash
kill -15 PID
```

> Where PID is the process ID of the Omniscope Java process. (You can obtain the PID by executing "ps aux | grep java" and identifying the Omniscope java process) Use this command to gracefully stop the Omniscope app if you have started it through one of the above scripts. But if installed as a service, see the systemctl commands below.

```bash
kill -15 -f com.visokio
```

> Use this command to gracefully stop any Omniscope app running on your machine. But if installed as a service, see the systemctl commands below.

### Service commands

```bash
sudo systemctl start omniscope-headless.service
```

> Starts the Omniscope Server service.  Note: the service starts automatically on system startup. Requires the Omniscope Server service to be installed - see the Server section above.  Requires Omniscope to be activated on the user account the service is configured to run as.

```bash
sudo systemctl status omniscope-headless.service
```

> Displays the running/stopped status of the Omniscope Server service. Requires the Omniscope Server service to be installed - see the Server section above.

```bash
sudo systemctl stop omniscope-headless.service
```

> Stops the Omniscope Server service.  Note: the service starts automatically on system startup. Requires the Omniscope Server service to be installed - see the Server section above.

```bash
sudo systemctl restart omniscope-headless.service
```

> Restarts the Omniscope Server service.  Note: the service starts automatically on system startup. Requires the Omniscope Server service to be installed - see the Server section above.
