# Running Windows Game Servers on Linux using WINE

It is possible to run Windows game servers on Linux using the Open Game Panel (OGP).  To get this to work, Windows game servers **MUST run via [WINE](https://www.winehq.org/)** from a Linux server **with a desktop environment** such fluxbox, MATE, GNOME, Unity, or any of the other hundreds of different desktop environments available.

Wine (originally an acronym for "Wine Is Not an Emulator") is a compatibility layer capable of running Windows applications on several POSIX-compliant operating systems, such as Linux, macOS, & BSD.  It's various libraries can help make running _SOME_ Windows game servers on Linux a reality.  

In order to run Windows applications on Linux, you will need to install WINE.  And, you must run a desktop environment such as fluxbox, MATE, GNOME, or Unity. 

This guide will walk you through installing and configuring fluxbox (a light weight desktop environment).  It will also walk you through installing and configuring VNC server to access the desktop through a graphical user interface (GUI) remotely.

### Install Software
Run the below commands based on your operating system to obtain everything you need to get started using WINE on Linux:

#### Ubuntu:
`sudo apt-get install wine fluxbox vnc4server`

#### Fedora / CentOS / Red-Hat:
```
sudo yum install epel-release
sudo yum install wine fluxbox
sudo yum install vnc-server
sudo yum install tigervnc-server
sudo yum install nano
```

### Set VNC Password and Configure Fluxbox

After installing the needed packages, it is recommended that you set a password for your VNC server. To set a VNC password for the OGP agent user, first login as the OGP user.  Adjust the below code as necessary using the correct ogp user.

`sudo su - ogp_agent`

Set a VNC password now by running this command:

`vncpasswd`

Configure VNC to use Fluxbox:

```
vncserver -geometry 800x600 -depth 24
vncserver -kill :1
```

Configure your VNC server to interface with the fluxbox desktop environment.

`nano ~/.vnc/xstartup`

Add the line below to the end of the file.

`fluxbox &`

Save the file (CTRL + O) and exit nano (CTRL + X).

Switch back to the root account.

`exit`

### Create a Startup Script for the VNC Server

Use one of the two methods below depending on your operating system.  If your operating system uses the new systemd boot process, use the first guide.  If your operating system uses the old init.d boot process, scroll down to the older init.d section.

***
#### Systemd Service (Ubuntu 16.04+ / CentOS 7+ / Newer Operating Systems)

`sudo nano /etc/systemd/system/ogp_vnc.service`

Copy and paste the following lines into the file:

```
# ogp_vnc systemd Service Script
# By Open Game Panel
# OGP <own3mall@gmail.com>
[Unit]
Description=OGP VNC Daemon
After=network.target

[Service]
Type=oneshot

ExecStart=/bin/nohup /bin/sh -c "/usr/bin/ogp_vnc.sh start" &>/dev/null &
ExecStop=/bin/sh -c "/usr/bin/ogp_vnc.sh stop"

RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

Save the file.  Reload the systemd daemon:

`sudo systemctl daemon-reload`

Create the script called by the service file:

`sudo nano /usr/bin/ogp_vnc.sh`

Copy and paste the following lines into the file (adjust the "agent_user=" line with your agent username):

```
#!/bin/sh
export PATH="/usr/local/sbin:/sbin:/bin:/usr/sbin:/usr/bin"
agent_user=ogp_agent
#service=ogp_vnc.service

start() {
    # start daemon
    echo -n "Starting OGP Agent WINE VNC: "
    su -c "vncserver :1 -geometry 800x600 -depth 24" $agent_user
    RETVAL=$?
    return $RETVAL
}

stop() {
    # stop daemon
    echo -n "Stopping OGP Agent WINE VNC: "
    su -c "vncserver -kill :1 >/dev/null 2>&1" $agent_user
    RETVAL=$?
    return $RETVAL
}

case "$1" in
    start)
    start
    RETVAL=$?
    ;;
    stop)
    stop
    RETVAL=$?
    ;;
    restart)
    stop
    sleep 1
    start
    RETVAL=$?
    ;;
    condrestart)
    stop
    sleep 1
    start
    ;;
    status)
    status $service
    RETVAL=0
    ;;
    *)
    echo "Usage: $service {start|stop|restart|condrestart|status}"
    RETVAL=1
    ;;
esac

exit $RETVAL
```
Make sure **to adjust the "agent_user=" line** with your OGP agent username.

Enable the ogp_vnc agent on boot:

`sudo systemctl enable ogp_vnc`

Make this file executable:

`sudo chmod 775 /usr/bin/ogp_vnc.sh`


Reboot your machine:

`sudo shutdown -r now`

***
#### Normal Init.d Script (Older Operating Systems - Ubuntu 14.04 & Older / CentOS 6 & Older)

`sudo nano /etc/init.d/ogp_vnc`

Copy and paste the following lines into the file (adjust the "agent_user=" line with your OGP agent username):

```
#!/bin/sh
#
#   Startup/shutdown script for the VNC server.
#
#   Linux chkconfig stuff:
#
#   chkconfig: 2345 88 10
#   description: Startup/shutdown script for the OGP Agent
#
### BEGIN INIT INFO
# Provides:          ogp_vnc
# Required-Start:    $network $local_fs
# Required-Stop:     $local_fs $network
# Should-Start:      $local_fs $network 
# Should-Stop:       $local_fs $network 
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: Start and stop the VNC server for OGP Agent
# Description:       Start and stop the VNC server for OGP Agent
### END INIT INFO
#
set -e
set -u
${DEBIAN_SCRIPT_DEBUG:+ set -v -x}

agent_user=ogp_agent
service=ogp_vnc.service

# Source function library.
if [ -f /etc/rc.d/init.d/functions ] ; then
    . /etc/rc.d/init.d/functions
elif [ -f /etc/init.d/functions ] ; then
    . /etc/init.d/functions
elif [ -f /lib/lsb/init-functions ] ; then
    . /lib/lsb/init-functions
fi

start() {
    # start daemon
    echo -n "Starting OGP Agent WINE VNC: "
    su -c "vncserver :1 -geometry 800x600 -depth 24 >/dev/null 2>&1" $agent_user
    RETVAL=$?
    if [ $RETVAL -eq 0 ]; then
       touch /var/lock/$service
       echo success
    else
       echo fail
    fi
    return $RETVAL
}

stop() {
    # stop daemon
    echo -n "Stopping OGP Agent WINE VNC: "
    su -c "vncserver -kill :1 >/dev/null 2>&1" $agent_user
    RETVAL=$?
    if [ $RETVAL -eq 0 ]; then 
       rm -f /var/lock/$service
       echo success
    else
       echo fail
    fi
    return $RETVAL
}

case "$1" in
    start)
    start
    RETVAL=$?
    ;;
    stop)
    stop
    RETVAL=$?
    ;;
    restart)
    stop
    sleep 1
    start
    RETVAL=$?
    ;;
    condrestart)
    if [ -f /var/lock/$service ]; then
       stop
       RETVAL=$?
       if [ $RETVAL -eq 0 ]; then
          start
          RETVAL=$?
       fi
    fi
    ;;
    status)
    status $service
    RETVAL=0
    ;;
    *)
    echo "Usage: $service {start|stop|restart|condrestart|status}"
    RETVAL=1
    ;;
esac

exit $RETVAL
```

Make sure **to adjust the "agent_user=" line** with your OGP agent username.

Save and exit.  Make the script executable by running the below command:

`sudo chmod +x /etc/init.d/ogp_vnc`

***

### Run the VNC Script when the System Boots

#### Ubuntu / Debian:

```
sudo update-rc.d ogp_vnc defaults
sudo systemctl enable ogp_vnc
```

#### Fedora / CentOS / Red-Hat:

```
sudo chkconfig ogp_vnc on
sudo systemctl enable ogp_vnc
```

### Start the Service

`sudo service ogp_vnc start`

### Connecting via VNC:

Connect to your server by using your server's IP address and port 1 using any VNC client.  

Run the following commands to make VNC accessible from remote connections:

#### Fedora / CentOS / Red-Hat:

```
sudo yum -y install iptables-services
sudo echo "SELINUX=disabled" > "/etc/sysconfig/selinux"
sudo systemctl disable firewalld
sudo iptables -F # Flush all the rules
sudo service iptables save
```

Note that this is absolutely not required to have WINE working in OGP. It can be useful for specific rare cases where you would need to access the desktop created for running WINE game servers, for example to click the WINE popup asking to install a specific component. Most of the time you wouldn't want to have this desktop accessible.