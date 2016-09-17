## accessing a sitemap
### syntax
Classic UI: `http://<IP>:<port>/classicui/app?sitemap=<sitemap>`  
Basic UI: `http://<IP>:<port>/basicui/app?sitemap=<sitemap>`  

### examples
Classic UI: http://192.168.188.10:8080/classicui/app?sitemap=presence  
Basic UI: http://192.168.188.10:8080/basicui/app?sitemap=presence

## designer for openhab2
https://www.eclipse.org/smarthome/documentation/community/downloads.html

## Samba file share for designer
souce: http://docs.openhab.org/installation/linux.html#network-sharing
> sudo apt-get install samba samba-common-bin
> sudo vim /etc/samba/smb.conf
> sudo smbpasswd -a openhab
> sudo chown -hR openhab:openhab /etc/openhab2
> sudo service smbd restart

## checking logs
> tail -f /var/log/openhab2/openhab.log -f /var/log/openhab2/events.log

## set state of items to ON or OFF
`curl -X PUT --header "Content-Type: text/plain" --header "Accept: application/json" -d "<ON|OFF>" "http://192.168.188.10:8080/rest/items/<item>/state"`
