# Open Game Panel (OGP) Wiki

***

## About

The Open Game Panel (OGP) consists of two software packages.  The agent manages, controls, and in some cases configures and installs game servers on the actual host server itself.  The web portion of OGP is powered by PHP and serves as a secure interface between one or many OGP agents.  The OGP web software is a centralized location for server admins, users, and sub-users to stop, start, manage, and edit their game servers across multiple host servers.

***

## Quick Navigation:

[OGP Agent Guide](#agent-installation)

[OGP Panel Web Guide](#web-installation)

[Help](#help)

***

## Agent Installation

### Prerequisites / Installing Dependent Software Packages

The OGP agent is written in PERL and depends on several software packages.  The below guide provides code to install the prerequisites.  If any of these software packages are missing, the agent will not work correctly.

**All Versions of Ubuntu:**

```
sudo apt-get install libxml-parser-perl libpath-class-perl perl-modules screen rsync sudo e2fsprogs unzip subversion libarchive-extract-perl pure-ftpd libarchive-zip-perl libc6 libgcc1 git
sudo apt-get install libc6-i386 libgcc1:i386
sudo apt-get install lib32gcc1
sudo apt-get install libhttp-daemon-perl
```

**All Versions of Debian:**

```
sudo apt-get install libxml-parser-perl libpath-class-perl perl-modules screen rsync sudo e2fsprogs unzip subversion pure-ftpd libarchive-zip-perl libc6 libgcc1 git
sudo apt-get install libc6-i386 lib32gcc1
sudo apt-get install libhttp-daemon-perl
```

**Debian 8 Extra Packages:**
```
sudo apt-get install libarchive-extract-perl
```

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

**Other OS:**

The OGP agent will work with the proper PERL prerequisites on any Linux operating system. This guide only contains instructions for the most post popular Linux flavors.  If you manage to setup OGP on a different operating system, please let us know so we can include it in this guide.

There are **OLD** instructions [here](https://sourceforge.net/p/hldstart/wiki/Installation%20Notes/) for other operating systems including Gentoo and OpenSUSE.

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

## Web Installation

### Web Server Required

A working LAMP (Linux Apache MySQL PHP) stack server is required before you can install the OGP web software.  If you don't have one already setup and configured, install the dependent packages below.  If you do already have a LAMP stack server setup, please verify that you have all of the prerequisite packages listed below.  Install any you might be missing.

### Prerequisites / Installing Dependent Software Packages

The below web server packages need to be installed before you can run the OGP web installer.

_Note: MySQL can and should be replaced with MariaDB (the package name is usually mariadb-server) if desired_

**For Ubuntu 14.04 or Lower:**
```
sudo apt-get install apache2 curl subversion php5 php5-gd php5-xmlrpc php5-curl php5-mysql php-pear phpmyadmin mysql-server libapache2-mod-php5 git
```

**For Ubuntu 16.04 or Higher:**
```
sudo apt-get install apache2 curl subversion php7.0 php7.0-gd php7.0-zip libapache2-mod-php7.0 php7.0-curl php7.0-mysql php7.0-xmlrpc php-pear phpmyadmin mysql-server php7.0-mbstring php-gettext git
```

**For Debian 8 or Lower:**
```
sudo apt-get install apache2 curl subversion php5 php5-gd php5-xmlrpc php5-curl php5-mysql php-pear phpmyadmin mysql-server libapache2-mod-php5 git
```

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

### Installing OGP Web Software
The latest OGP web files can be downloaded [here](https://github.com/OpenGamePanel/OGP-Website/archive/master.zip).

To install the OGP web software, first create a new MySQL database and assign a MySQL user secured with a password to it.

Then, extract the PHP files to a directory within your Apache2 httpdocs default home directory.  Access the installer by visiting http://yourip/yourogpfolderwithfiles in your browser.

Provide the MySQL credentials, create an admin account, and follow the installation steps to complete the install process.  

Once the installation is completed, under the "Administration" tab, click on "Servers".  Enter in the agent information here.  Once the agent has been configured, you can add game servers to it.  

***

## Help

For help installing OGP, please visit and post in our [forums](http://faq.opengamepanel.org/forum/index.php).

You can also request new features, help with new game server XML configuration files, and chat about anything OGP related!