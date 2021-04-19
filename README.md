It can be handy to have two or more instances of Domoticz running :

 - During development you disrupt Domoticz by restarts to activate changes.

 - You may want to have a single plugin running more times.

A solution is to have more computers with one Domoticz each.

Another solution is to have more instances of Domoticz on one computer.

Here are the steps to create a second instance named Second_domoticz on www port 8888 en sslwww port 1234

Login with your user account

 - sudo systemctl stop domoticz

 - sudo cp -ra domoticz/ Second_domoticz

The next is just to prove things work and will be reverted later.

 - mv domoticz/ no_domoticz/

Be root

 - sudo -i

 - cd /etc/init.d

 - cp /etc/init.d/domoticz.sh /etc/init.d/Second_domoticz.sh


Use your favorite editor to make some changes :

 - geany /etc/init.d/Second_domoticz.sh

Changes 4 lines and enable 1 line :

### - Changed next line
NAME=Second_domoticz

### - Changed next line
DAEMON=/home/pi/Second_domoticz/domoticz

### - Enabled next line
DAEMON_ARGS="$DAEMON_ARGS -daemonname $NAME -pidfile $PIDFILE"

### - Changed next line
DAEMON_ARGS="$DAEMON_ARGS -www 8888"

### - Changed next line
DAEMON_ARGS="$DAEMON_ARGS -sslwww 1234"

Save the file and close your editor.

The next part may be done in a different way but this is what I did....

 - find /etc/rc* -name '*domo*'

Now you have an overview of all S01 (start) and K01 (kill) scripts for all runlevels.
These are not real scripts but links to /etc/init.d/domoticz.sh.
The new service needs these too to run after reboot and stop during shutdown.

Copy and execute the next 14 lines to your terminal window to make the links :

cd /etc/rc0.d

ln -s ../init.d/Second_domoticz.sh K01Second_domoticz.sh

cd /etc/rc1.d

ln -s ../init.d/Second_domoticz.sh K01Second_domoticz.sh

cd /etc/rc2.d

ln -s ../init.d/Second_domoticz.sh S01Second_domoticz.sh

cd /etc/rc3.d

ln -s ../init.d/Second_domoticz.sh S01Second_domoticz.sh

cd /etc/rc4.d

ln -s ../init.d/Second_domoticz.sh S01Second_domoticz.sh

cd /etc/rc5.d

ln -s ../init.d/Second_domoticz.sh S01Second_domoticz.sh

cd /etc/rc6.d

ln -s ../init.d/Second_domoticz.sh K01Second_domoticz.sh

 - find /etc/rc* -name '*domo*'

Now you should have a list with all S01 and K01 scripts for all runlevels for both domoticz services.

 - /etc/init.d/Second_domoticz.sh start

And your service starts.

 - ps -ef | grep sslwww
 
root      2273     1  4 15:46 ?        00:00:01 /home/pi/Second_domoticz/domoticz -daemon -daemonname Second_domoticz -pidfile /var/run/Second_domoticz.pid -www 8888 -sslwww 1234

 - exit

And you are back in your user account

Start your web browser to your Domoticz host on port 8888 and you should have a copy of your Domoticz.

Now you see that the copy works move the original one you put aside for this test back.

 - mv no_domoticz/ domoticz/

 - sudo systemctl start domoticz

 - ps -ef | grep sslwww

root      2273     1  0 15:46 ?        00:00:03 /home/pi/Second_domoticz/domoticz -daemon -daemonname Second_domoticz -pidfile /var/run/Second_domoticz.pid -www 8888 -sslwww 1234

root      2453     1  7 15:51 ?        00:00:00 /home/pi/domoticz/domoticz -daemon -www 8080 -sslwww 443

And you have 2 seperate instances.

After a reboot both will run.

Enjoy and take care not to change plugins in the wrong instance ;-)
