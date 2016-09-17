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
