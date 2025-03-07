Updated Jan 02 2024

# Welcome to MolProbity!!

##### Table of Contents
- [Introduction](#introduction)
- [1. System Preparation](#prepare)
    - [a. Preliminaries](#preliminaries)
    - [b. Installing PHP 5.6](#php5)
    - [c. PHP Version Check](#phpcheck)
    - [d. Configure Apache](#configureapache)
- [2. MolProbity Installation](#install)
    - [a. Acquiring MolProbity](#acquiremolprobity)
    - [b. Run install_via_bootstrap.sh](#runbootstrap)
    - [c. Run setup.sh to configure the webserver](#runsetup)
- [3. Providing System or Local Webservice](#service)
    - [a. Webservices](#webservice)
    - [b. Webserver/PHP configuration](#phpconfig)


<a name="introduction"></a>
## Introduction

MolProbity website source and installation instructions are provided with the
cautionary restriction and explicit understanding that user deployment occur
only on individual machines and restricted access private networks.
This code is not intended for deployment on world accessible public sites.

We recommend that users consider an installation in
user space as opposed to a systemwide installation and invoke molprobity
as a local app using a lightweight user based web server (such as
php -S localhost:8000 provided with the php-cli package) as opposed to the
systemwide web server (eg apache.)

Installation is fairly straightforward but will probably require some system
administration background.  The most notable wrinkle is the need to install
an older version of PHP (PHP5.6) as the default PHP interpreter.

MolProbity installation is accomplished with a downloadable script which
fetches required sources and data into a specifically created empty directory
as opposed to cloning this repository. 

We describe our installation recipe in three parts:
1. System preparation (mostly installing php 5.6)
2. Molprobity installation using our standalone script placed at the top of
an empty directory created specifically for the installation
3. Providing system and local webservices. These notes are mostly specific
to Linux Ubuntu/Debian systems but can be adapted to other platforms.

<a name="prepare"></a>
## 1. System Preparation:
<a name="preliminaries"></a>
### a. Preliminaries:

Linux users should ensure that gawk is on a path that the
server can read (gawk may need to be installed). Also, python-dev is a
one of MolProbity's cctbx dependencies, and may need to be installed.
The build script will warn you about this if it fails for this reason.
32 BIT Linux is not supported.

Mac users should know that the Xcode app and xcode commandline tools may be
needed to install MolProbity.
<a name="php5"></a>
### b. Installing PHP 5.6

MolProbity requires the older 5.6 version of PHP to work.

```
apt-get install -y software-properties-common
sudo add-apt-repository ppa:ondrej/php
sudo apt update
sudo apt upgrade
sudo apt install -y php5.6
```

```
apt-get install php5.6-gd php5.6-mysql php5.6-imap php5.6-curl
php5.6-intl php5.6-pspell php5.6-recode php5.6-sqlite3 php5.6-tidy
php5.6-xmlrpc php5.6-xsl php5.6-zip php5.6-mbstring php5.6-soap
php5.6-opcache libicu65 php5.6-common php5.6-json php5.6-readline
php5.6-xml libapache2-mod-php5.6 php5.6-cli 
```
<a name="phpcheck"></a>
### c. PHP Version Check:

```
php --version
```
<a name="configureapache"></a>
### d. Configure Apache
```
sudo update-alternatives --config php
```
or
```
sudo a2enmod php5.6
sudo service apache2 restart
```
See

https://vitux.com/how-to-install-php5-php8-on-ubuntu/

<a name="install"></a>
## 2. MolProbity Installation:

The MolProbity installation process is currently in flux. This is our
recommended protocol which starts with downloading our standalone
installation script into an empty MolProbity directory specifically
created for the installation.

<a name="acquiremolprobity"></a>
### a. Acquiring MolProbity 

First create the empty MolProbity directory (the directory that this
README file will be in) to a location where you want it to reside.
Note that Apache will have to see molprobity/public_html if you are
interested in setting up a web service.

Second, open a terminal and change into the empty MolProbity directory which you
have just created. Download our installation script into your MolProbity
directory with:
```
wget -O install_via_bootstrap.sh https://github.com/rlabduke/MolProbity/raw/master/install_via_bootstrap.sh
```
Make sure that this script is at the top level of the directory where
MolProbity is to be installed.

<a name="runbootstrap"></a>
### b. Run install_via_bootstrap.sh. 
```
./install_via_bootstrap.sh
```
The script can accept a single integer argument to set the number of processors
to use.  We recommend 4 if possible.
```
./install_via_bootstrap.sh 4
```
This will install cctbx_project and the following needed buildlist components
in MolProbity/modules:

> annlib
> 
> annlib_adaptbx
> 
> cbflib
> 
> ccp4io
> 
> ccp4io_adaptbx
> 
> cctbx_project
> 
> chem_data
> 
> probe
> 
> reduce
> 
> tntbx

and then will compile and configure in MolProbity/build.

<a name="runsetup"></a>
### c. Run setup.sh to configure the webserver.
```
cd/molprobity
./setup.sh
```

<a name="service"></a>
## 3. Providing System or Local Webservice

<a name="webservice"></a>
### a. Webservices

If setting up a webserver, make sure that the machine's Apache
configuration can point to the MolProbity/public_html directory.

Note that it is not necessary to setup a webserver if you are only
interested in running the command-line tools.

Note that it is not necessary to set up an externally-accessible
webserver like Apache to get MolProbity served as a website available
only on your computer (via localhost). You will need to install
php-cli or a similar package, then open a terminal in the MolProbity directory
and run:
```
php -S localhost:8000
```
This starts up a local userspace webserver which will continue to run until
its process is killed.

In a browser, navigate to:

http://localhost:8000/public_html/index.php 

you will have a functioning local MolProbity site. After finishing your
MolProbity work and closing the browser, you can shut down the local userspace
webservice by using a Ctl-C command to kill the process in the terminal
where the command was invoked. 

<a name="phpconfig"></a>

### b. Webserver/PHP configuration

Finally it is very important to tweak webserver and PHP settings
for proper operation. 

We have provided two files in public_html/, .user.ini and .htaccess,
which attempt to preclude the need for users to do system-wide server
reconfigurations.  In particular, .htaccess should override the global
php.ini for an Apache MolProbity server, and .user.ini should do the
same for a local PHP-CLI server.

If that doesn't work, you may need to edit Apache settings for proper
MolProbity performance.  The settings file is often called php.ini,
e.g. /etc/php5/???/php.ini, but it will vary on different computers.
We recommend setting at least:

`upload_max_filesize = 50M`

`post_max_size = 50M`

Values higher than 50M may be needed for unusually large files.

An external explanation:
http://stackoverflow.com/questions/24377403/maximum-upload-size-in-php-and-apache
http://stackoverflow.com/questions/2184513/php-change-the-maximum-upload-file-size

It may also be appropriate to set the script memory limit, thus:
`memory_limit = 1280M`

We invite feedback on your experience with these instructions (and notes
for different platforms) so they may be improved. Thank you for using
MolProbity!
