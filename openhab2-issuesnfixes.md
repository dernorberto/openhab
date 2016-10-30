## my.openhab not working, always offline
### Solution: update java, initially u65
> sudo su  
> systemctl stop openhab2.service  
> apt purge oracle-java8-jdk  

> vi /etc/apt/sources.list.d/webupd8team-java.list  
`deb http://ppa.launchpad.net/webupd8team/java/ubuntu trusty main`  
`deb-src http://ppa.launchpad.net/webupd8team/java/ubuntu trusty main`  
> apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys EEA14886  

> apt update  
> apt -y install oracle-java8-installer  
> apt -y install oracle-java8-set-default  
> systemctl start openhab2.service

## after an update, all the extensions (binding, ui, action, etc.) have been removed
### solution: update addons configuration file which will instruct openhab2 to install them

> vi /etc/openhab2/services/addons.cfg

```
...
# A comma-separated list of bindings to install (e.g. "sonos,knx,zwave")
binding = zwave,avmfritz,sonos,yahooweather
  
# A comma-separated list of UIs to install (e.g. "basic,paper")
ui = basic,paper,habmin,classic,habpanel

# A comma-separated list of persistence services to install (e.g. "rrd4j,jpa")
persistence = rrd4j

# A comma-separated list of actions to install (e.g. "mail,pushover")
action = mail

# A comma-separated list of transformation services to install (e.g. "map,jsonpath")
transformation =

# A comma-separated list of text-to-speech engines to install (e.g. "marytts,freetts")
tts =

# A comma-separated list of miscellaneous services to install (e.g. "myopenhab")
misc = myopenhab,homekit
...
```
