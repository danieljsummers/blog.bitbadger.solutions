---
layout: post
title: Mono / FastCGI Startup Script
author: Daniel
date: 2010-09-03 19:28:15
categories:
- Linux
- [ Programming, .NET, Mono ]
- Web Servers
tags:
- badgerports
- config
- fastcgi
- mono
- script
summary: A script that allows Mono web applications to be defined and started the way Apache and nginx enable and disable their sites
---

We've begun running Mono on some DJS Consulting servers to enable us to support the .NET environment, in addition to the PHP environment most of our other applications use. While Ubuntu has nice packages (and Badgerports even <del>brings</del> brought them up to the latest release), one thing that we were missing was a "conf.d"-type of configuration; my "/applications=" clause of the command was getting really, really long. We decided to see if we could create something similar to Apache / Nginx's sites-available/sites-enabled paradigm, and we have succeeded!

To begin, you'll need to create the directories `/etc/mono/fcgi/apps-available` and `/etc/mono/fcgi/apps-enabled`. These directories will hold files that will be used define applications. The intent of these directories is to put the actual files in `apps-available`, then symlink the ones that are enabled from `apps-enabled`. These files have no name restrictions, but do not put an extra newline character in them. The script will concatenate the contents of that file to create the [MONO_FCGI_APPLICATIONS environment variable][env], which tells the server what applications exist. (The syntax is the same as that for the "/applications=" clause - `[domain]:[URL path]:[filesystem path]`.) Here's how the site you're reading now is configured (from the file `djs-consulting.com.techblog.conf`)...

{% codeblock djs-consulting.com.techblog.conf lang:shell %}
techblog.djs-consulting.com:/:/path/to/install/base/for/this/site
{% endcodeblock %}

Finally, what brings it all together is a shell script. This should be named "monoserve" and placed in `/etc/init.d`. (This borrows heavily from <del>this script</del> a script we found online, which we used until we wrote this one.) Note the group of variables surrounded by the "make changes here" notes - these are the values that are used in starting the server. They are at the top so that you can easily modify this for your own needs.

{% codeblock monoserve lang:shell %}
#/bin/bash

### BEGIN INIT INFO
# Provides:          monoserve.sh
# Required-Start:    $local_fs $syslog $remote_fs
# Required-Stop:     $local_fs $syslog $remote_fs
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: Start FastCGI Mono server with hosts
### END INIT INFO

PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
DAEMON=/usr/bin/mono
NAME=monoserver
DESC=monoserver

## Begin -- MAKE CHANGES HERE --
PROGRAM=fastcgi-mono-server2 # The program which will be started
ADDRESS=127.0.0.1            # The address on which the server will listen
PORT=9001                    # The port on which the server will listen
USER=www-data                # The user under which the process will run
GROUP=$USER                  # The group under which the process will run
## End   -- MAKE CHANGES HERE --

# Determine the environment
MONOSERVER=$(which $PROGRAM)
MONOSERVER_PID=""
FCGI_CONFIG_DIR=/etc/mono/fcgi/apps-enabled

# Start up the Mono server
start_up() {
    get_pid
    if [ -z "$MONOSERVER_PID" ]; then
        echo "Configured Applications"
        echo "-----------------------"
        # Construct the application list if the configuration directory exists
        if [ -d $FCGI_CONFIG_DIR ]; then
            MONO_FCGI_APPLICATIONS=""
            for file in $( ls $FCGI_CONFIG_DIR ); do
                if [ "$MONO_FCGI_APPLICATIONS" != "" ]; then
                    MONO_FCGI_APPLICATIONS=$MONO_FCGI_APPLICATIONS,
                fi
                MONO_FCGI_APPLICATIONS=$MONO_FCGI_APPLICATIONS`cat $FCGI_CONFIG_DIR/$file`
            done
            export MONO_FCGI_APPLICATIONS
            echo -e ${MONO_FCGI_APPLICATIONS//,/"\n"}
        else
            echo "None (config directory $FCGI_CONFIG_DIR not found)"
        fi
        echo

        # Start the server
        start-stop-daemon -S -c $USER:$GROUP -x $MONOSERVER -- /socket=tcp:$ADDRESS:$PORT &
        echo "Mono FastCGI Server $PROGRAM started as $USER on $ADDRESS:$PORT"
    else
        echo "Mono FastCGI Server is already running - PID $MONOSERVER_PID"
    fi
}

# Shut down the Mono server
shut_down() {
    get_pid
    if [ -n "$MONOSERVER_PID" ]; then
        kill $MONOSERVER_PID
        echo "Mono FastCGI Server stopped"
    else
        echo "Mono FastCGI Server is not running"
    fi
}

# Refresh the PID
get_pid() {
    MONOSERVER_PID=$(ps auxf | grep $PROGRAM.exe | grep -v grep | awk '{print $2}')
}

case "$1" in
    start)
        start_up
    ;;
    stop)
        shut_down
    ;;
    restart|force-reload)
        shut_down
        start_up
    ;;
    status)
        get_pid
        if [ -z "$MONOSERVER_PID" ]; then
            echo "Mono FastCGI Server is not running"
        else
            echo "Mono FastCGI Server is running - PID $MONOSERVER_PID"
        fi
    ;;
    *)
        echo "Usage: monoserve (start|stop|restart|force-reload|status)"
    ;;
esac

exit 0
{% endcodeblock %}

This needs to be owned by root and be executable (`chmod +x monoserve`). You can use `update-rc.d monoserve defaults` to set this to start at boot.


[env]: http://www.mono-project.com/docs/web/fastcgi/ "FastCGI &bull; Mono Project"
