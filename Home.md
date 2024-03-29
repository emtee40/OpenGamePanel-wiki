# Open Game Panel (OGP) Wiki

***

## About

The Open Game Panel (OGP) consists of two software packages.  The agent manages, controls, and in some cases configures and installs game servers on the actual host server itself.  The web portion of OGP is powered by PHP and serves as a secure interface between one or many OGP agents.  The OGP web software is a centralized location for server admins, users, and sub-users to stop, start, manage, and edit their game servers across multiple host servers.

***

## Quick Navigation:
[OGP Easy Installers - Windows and Linux](#easy-installation)

[OGP Agent Linux Manual Installation Guide](#manual-agent-linux-installation)

[OGP Panel Linux Manual Web Installation Guide](#manual-web-linux-installation)

[Help](#help)

***

## Easy Installation

The Open Game Panel agent and web packages can be easily installed on Ubuntu 14.04 and up, Debian, CentOS 6 & 7, and Windows 7 through Windows 10 (including Windows Server versions) using our pre-built easy install packages.  You can download and install the easy installers from the [OGP Downloads page](https://opengamepanel.org/infusions/pro_download_panel/download.php?catid=9).  There will also be specific instructions on the download page you must follow to use the easy installers.  This is the quickest and easiest way to install and use OGP.  These installers will configure everything for you required to run both OGP packages (agent and panel) with minimal setup.

If you want to install OGP on Windows, you MUST USE the Windows easy installers (which are pre-configured cygwin packages).  If you do NOT use the easy installers for Windows, good luck.

***

## Manual Agent Linux Installation

### Prerequisites / Installing Dependent Software Packages

The OGP agent is written in PERL and depends on several software packages.  The below guide provides code to install the prerequisites.  If any of these software packages are missing, the agent will not work correctly.

***

### Ubuntu

**All Versions of Ubuntu:**

```
sudo apt-get install libxml-parser-perl libpath-class-perl perl-modules screen rsync sudo e2fsprogs unzip subversion libarchive-extract-perl pure-ftpd libarchive-zip-perl libc6 libgcc1 git curl
sudo apt-get install libc6-i386 
sudo apt-get install libgcc1:i386
sudo apt-get install lib32gcc1
sudo apt-get install libhttp-daemon-perl
```

***

### Debian

If sudo is not installed automatically on your installation of Debian, switch to the root user using the below command:

```
su -
```
Install sudo now before proceeding:
```
apt-get install sudo
```
Add your user to the sudo group if you're not a member of it already (first line for Ubuntu, second line for CentOS / Fedora):
```
usermod -aG sudo "{REPLACE_WITH_YOUR_LINUX_USERNAME}"
usermod -aG wheel "{REPLACE_WITH_YOUR_LINUX_USERNAME}"
```
Restart the machine to apply the sudo group change:
```
shutdown -r now
```

Now install the prerequisites:

```
sudo apt-get install libxml-parser-perl libpath-class-perl perl-modules screen rsync sudo e2fsprogs unzip subversion pure-ftpd libarchive-zip-perl libc6 libgcc1 git curl
sudo apt-get install libc6-i386 lib32gcc1
sudo apt-get install libhttp-daemon-perl
```

**Debian 8, 9, and 10 Extra Packages:**
```
sudo apt-get install libarchive-extract-perl
```

***

### CentOS


**CentOS 6:**

```
sudo yum -y update
sudo yum -y install epel-release wget subversion git
sudo yum install -y perl-libwww-perl proftpd proftpd-utils perl-ExtUtils-MakeMaker glibc.i686 glibc libgcc_s.so.1 perl-IO-Compress-Bzip2 perl-Archive-Extract perl-Archive-Zip perl-Archive-Tar perl-Path-Class
```

After installation has been completed, you'll need to run the following to get proftpd to work properly:

```
sudo sed -i "s/^LoadModule\( \)*mod_auth_file.c/#LoadModule mod_auth_file.c/g" "/etc/proftpd.conf"
sudo service proftpd restart
```

**CentOS 7:**

```
sudo yum -y update
sudo yum -y install epel-release wget subversion git
sudo yum install -y perl-HTTP-Daemon perl-LWP-Protocol-http10 proftpd proftpd-utils perl-ExtUtils-MakeMaker glibc.i686 glibc libgcc_s.so.1 perl-IO-Compress-Bzip2 perl-Archive-Extract perl-Archive-Zip perl-Archive-Tar perl-Path-Class
```

***

### Other

The OGP agent will work with the proper PERL prerequisites on any Linux operating system. This guide only contains instructions for the most post popular Linux flavors.  If you manage to setup OGP on a different operating system, please let us know so we can include it in this guide.

There are **OLD** instructions [here](https://sourceforge.net/p/hldstart/wiki/Installation%20Notes/) for other operating systems including Gentoo and OpenSUSE.

***

### Obtaining Latest Version and Running Installer

The latest OGP agent files can be downloaded [here](https://github.com/OpenGamePanel/OGP-Agent-Linux/archive/master.zip).

It is recommended that the OGP agent run under its own sudo user account.  To create a new user and add him to the sudo group, run this command:

```
sudo adduser ogpbot
sudo usermod -a -G sudo ogpbot
```

Extract the agent files to any directory.  Then run install.sh with sudo:

```
sudo bash ./install.sh
```

Follow the installation prompts, and you're done.  The installer should add the appropriate rc links so that the agent will automatically start on boot.  Be sure to record and save your agent encryption password somewhere.  This is a key that the panel uses to communicate securely.  The panel web software will ask for it when configuring servers, so don't lose this information.  The encryption can always be retrieved or changed in the Config.pm file located in the /Cfg directory where the OGP agent files were installed.

***

## Manual Web Linux Installation

### Web Server Required

A working LAMP (Linux Apache MySQL PHP) stack server is required before you can install the OGP web software.  If you don't have one already setup and configured, install the dependent packages below.  If you do already have a LAMP stack server setup, please verify that you have all of the prerequisite packages listed below.  Install any you might be missing.

### Prerequisites / Installing Dependent Software Packages

The below web server packages need to be installed before you can run the OGP web installer.

_Note: MySQL can and should be replaced with MariaDB (the package name is usually mariadb-server) if desired_

***

### Ubuntu

**For Ubuntu 14.04 or Lower:**
```
sudo apt-get install apache2 curl subversion php5 php5-gd php5-xmlrpc php5-curl php5-mysql php-pear phpmyadmin mysql-server libapache2-mod-php5 git
```

**For Ubuntu 16.04:**
```
sudo apt-get install apache2 curl subversion php7.0 php7.0-gd php7.0-zip libapache2-mod-php7.0 php7.0-curl php7.0-mysql php7.0-xmlrpc php-pear phpmyadmin mysql-server php7.0-mbstring php-gettext git php-bcmath
```

**For Ubuntu 18.04:**
```
sudo apt-get install apache2 curl subversion php7.2 php7.2-gd php7.2-zip libapache2-mod-php7.2 php7.2-curl php7.2-mysql php7.2-xmlrpc php-pear phpmyadmin mysql-server php7.2-mbstring php-gettext git php-bcmath
```

***

### Debian

If you are running Debian 10 or lower, first make sure you have sudo installed and your user has sudo access.  

You can install and configure sudo by switching to the root user account using the command below:

```
su -
````

Now install the sudo package:

```
apt-get install sudo
```

Add your user to the sudo group if you're not a member of it already:

```
usermod -aG sudo "{REPLACE_WITH_YOUR_LINUX_USERNAME}"
```

Restart the machine to apply the sudo group change:

```
shutdown -r now
```

Then, run the following commands in a terminal:

**For Debian 10 Only:**

```
sudo apt-get install apache2 curl subversion php7.3 php7.3-gd php7.3-zip libapache2-mod-php7.3 php7.3-curl php7.3-mysql php7.3-xmlrpc php-pear mariadb-server php7.3-mbstring php-gettext git php-bcmath
sudo apt-get install phpmyadmin
```

The second line above used to install phpmyadmin may fail.  It's just there as a placeholder for when Debian 10 adds phpmyadmin to its repository of available software to install.  phpmyadmin isn't required to run the OGP web panel software.  phpmyadmin is just useful and easy to use software.

**For Debian 9 Only:**

```
sudo apt-get install apache2 curl subversion php7.0 php7.0-gd php7.0-zip libapache2-mod-php7.0 php7.0-curl php7.0-mysql php7.0-xmlrpc php-pear phpmyadmin mysql-server php7.0-mbstring php-gettext git php-bcmath
```

**For Debian 8 or Lower:**
```
sudo apt-get install apache2 curl subversion php5 php5-gd php5-xmlrpc php5-curl php5-mysql php-pear phpmyadmin mysql-server libapache2-mod-php5 git
```

**Debian 7 Change Default Apache Root Directory:**

If you're running Debian 7, it is recommended you change your DocumentRoot to /var/www/html by running these commands (OPTIONAL **do NOT do this if you host other websites or have pre-configured your webserver for a custom setup**):

```
sudo sed -i 's#<Directory /var/www.*#<Directory /var/www/html/>#g' /etc/apache2/sites-available/default
sudo sed -i 's#DocumentRoot /var/www.*#DocumentRoot /var/www/html#g' /etc/apache2/sites-available/default
sudo sed -i 's#<Directory /var/www.*#<Directory /var/www/html/>#g' /etc/apache2/sites-available/default-ssl
sudo sed -i 's#DocumentRoot /var/www.*#DocumentRoot /var/www/html#g' /etc/apache2/sites-enabled/000-default
sudo sed -i 's#<Directory /var/www.*#<Directory /var/www/html/>#g' /etc/apache2/sites-enabled/000-default
sudo sed -i 's#DocumentRoot /var/www.*#DocumentRoot /var/www/html#g' /etc/apache2/sites-enabled/000-default
sudo service apache2 restart
```

This would mean you should place the OGP files in this directory or in a subdirectory within the /var/www/html path.

***

### CentOS

**CentOS 6:**
```
sudo yum -y install epel-release wget subversion git
sudo yum -y install mysql-server
sudo service mysqld restart
sudo mysql_secure_installation
sudo chkconfig mysqld on
sudo yum -y install httpd php php-xmlrpc php-xml php-mysql php-gd php-pear php-mbstring curl
```

**CentOS 7:**
```
sudo yum -y install epel-release wget subversion git
sudo yum -y install mariadb-server
sudo service mariadb restart
sudo mysql_secure_installation
sudo yum -y install httpd php php-xmlrpc php-xml php-mysql php-gd php-pear php-mbstring curl
```

For other operating systems, please check the **OLD** instructions [here](https://sourceforge.net/p/hldstart/wiki/Installation%20Notes/#prerequisites-for-webui).

***

### Installing OGP Web Software
The latest OGP web files can be downloaded [here](https://github.com/OpenGamePanel/OGP-Website/archive/master.zip).

To install the OGP web software, first create a new MySQL database and assign a MySQL user secured with a password to it.

Then, extract the PHP files to a directory within your Apache2 httpdocs default home directory.  Access the installer by visiting http://yourip/yourogpfolderwithfiles in your browser.

Provide the MySQL credentials, create an admin account, and follow the installation steps to complete the install process.  

Once the installation is completed, under the "Administration" tab, click on "Servers".  Enter in the agent information here.  Once the agent has been configured, you can add game servers to it.  

***

## Additional Documentation and Info

Please read the posts in [the OGP Important Information forum](https://opengamepanel.org/forum/viewforum.php?forum_id=18) for other tidbits of information, guides, and documentation.

## Help

For help installing OGP, please visit and post in our [forums](http://faq.opengamepanel.org/forum/index.php).

You can also request new features, help with new game server XML configuration files, and chat about anything OGP related!