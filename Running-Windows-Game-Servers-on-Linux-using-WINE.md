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
`yum install wine fluxbox vnc-server`

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

`sudo nano /etc/init.d/ogp_vnc

Copy and paste the following lines into the file:

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

Make sure to adjust the "agent_user=" line with your agent's username.

Save and exit.  Make the script executable by running the below command:

`sudo chmod +x /etc/init.d/ogp_vnc`

### Run the VNC Script when the System Boots

#### Ubuntu / Debian:

`sudo update-rc.d ogp_vnc defaults`

#### Fedora / CentOS/ Red-Hat:

`sudo chkconfig ogp_vnc on`

### Start the Service

`sudo service ogp_vnc start`

### Connecting via VNC:

Connect to your server by using your server's IP address and port 1 using any VNC client.  