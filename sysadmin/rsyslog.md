rsyslog
=======

For Debian (and possibly other distributions), you can drop configuration files in /etc/rsyslog.d/ and they will be loaded in alphanumeric order.

It looks like this directory only supports the [basic syntax](https://www.rsyslog.com/doc/v8-stable/configuration/conf_formats.html) (I couldn't get advanced to work, but maybe I just didn't try hard enough).

# systemd

For Debian (and possibly other distributions) systemd is already configured so that journald forwards messages to syslog and the default config appears to handle reading from the journald syslog socket for you.

Still new to the whole module thing, but it looks like imuxsock is the way to go for parsing journald syslog messages (which is already setup by default!)

In your systemd service definition:

```
[Service]
...
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=myprog
```

Substituting any string for `myprog`. The `SysLogIdentifier` can be set to any string, it doesn't have to be the name of your program, and you will later be able to select logs from your program using `:programname,isequal,myprog` in your rsyslog config file.

# Writing to disk

Writing to disk is pretty simple! You just tell rsyslog what to match against and where to write it, for example:

```
:programname, isequal, "myprog" /logs/myprog.log
```

For a list of all the properties you can match against: https://www.rsyslog.com/doc/master/configuration/properties.html
For a list of all the filters you can match with: https://www.rsyslog.com/doc/master/configuration/filters.html

# Using templates

You can use the same properties from above to create templates that transforms a log before writing it to disk or dynamically create file names!

For example, this snippet:
* strips off all the syslog metadata (timestamp, program name, etc.) and writes just the log from the process
* does log rotation by writing to timestamped files

```
$template Myprog,"%msg%\n"
$template LogRotate,"/logs/myprog/myprog-%$year%%$month%%$day%-%$hour%0000.log"
:programname, isequal, "myprog" ?LogRotate;Myprog
```
