##openhab2 config
### items
```Switch PresencePhone1
Switch PresencePhone2
Switch PresencePhone3```

### sitemap

```
sitemap Presence label="Home sweet home" {
Frame label="Who's home?" {
                Switch item=PresencePhone1 visibility=[PresencePhone1==ON]
                Switch item=PresencePhone2 visibility=[PresencePhone2==ON]
                Switch item=PresencePhone3 visibility=[PresencePhone3==ON]
        }
}```

## AsusWRT
### cronjob

> crontab -e
```
* * * * * /jffs/scripts/CheckUser/checkIfHome.sh
* * * * * sleep 10; /jffs/scripts/CheckUser/checkIfHome.sh
* * * * * sleep 20; /jffs/scripts/CheckUser/checkIfHome.sh
* * * * * sleep 30; /jffs/scripts/CheckUser/checkIfHome.sh
* * * * * sleep 40; /jffs/scripts/CheckUser/checkIfHome.sh
* * * * * sleep 50; /jffs/scripts/CheckUser/checkIfHome.sh
```

### script: /jffs/scripts/CheckUser/checkIfHome.sh

Notes:
* to find out correct curl command, use REST API, search for items and update the state of an item
* the wifi detection command depends on the Asus Router model, in this case it's an RT-N66U wunning AsusWRT

```#!/bin/sh

Phone1=Away
Phone2=Away
Phone3=Away

macadresser="`wl -i eth1 assoclist` `wl -i eth2 assoclist`"
# set to 0 to use only wl for wifi detection
#antal=0
#antal=`qcsapi_sockrpc get_count_assoc wifi0`

#while [ $antal -gt 0 ]
#do
#  antal=`expr $antal - 1`
#  macadresser="`qcsapi_sockrpc get_station_mac_addr wifi0 $antal`;$macadresser"
#done

case "$macadresser" in
 *xx:xx:x:xx:xx:xx*)
 Phone1=Home
 ;;
esac

case "$macadresser" in
 *yy:yy:yy:yy:yy:yy*)
 Phone2=Home
 ;;
esac

case "$macadresser" in
 *zz:zz:zz:zz:zz:zz*)
 Phone3=Home
 ;;
esac


if [ "$Phone1" = Home ]
then
 if [ ! -f /jffs/scripts/CheckUser/Phone1Home ]
 then
  touch /jffs/scripts/CheckUser/Phone1Home
  curl -X PUT --header "Content-Type: text/plain" --header "Accept: application/json" -d "ON" "http://192.168.188.10:8080/rest/items/PresencePhone1/state"
 fi
 else
 if [ -f /jffs/scripts/CheckUser/Phone1Home ]
 then
  rm -f /jffs/scripts/CheckUser/Phone1Home
  curl -X PUT --header "Content-Type: text/plain" --header "Accept: application/json" -d "OFF" "http://192.168.188.10:8080/rest/items/PresencePhone1/state"
 fi
fi
               
if [ "$Phone2" = Home ]
 then
 if [ ! -f /jffs/scripts/CheckUser/Phone2Home ]
 then
  touch /jffs/scripts/CheckUser/Phone2Home
  curl -X PUT --header "Content-Type: text/plain" --header "Accept: application/json" -d "ON" "http://192.168.188.10:8080/rest/items/PresencePhone2/state"
 fi
 else
 if [ -f /jffs/scripts/CheckUser/Phone2Home ]
 then
  rm -f /jffs/scripts/CheckUser/Phone2Home
  curl -X PUT --header "Content-Type: text/plain" --header "Accept: application/json" -d "OFF" "http://192.168.188.10:8080/rest/items/PresencePhone2/state"
 fi
fi
                             
if [ "$Phone3" = Home ]
 then
 if [ ! -f /jffs/scripts/CheckUser/Phone3Home ]
 then
  touch /jffs/scripts/CheckUser/Phone3Home
  curl -X PUT --header "Content-Type: text/plain" --header "Accept: application/json" -d "ON" "http://192.168.188.10:8080/rest/items/PresencePhone3/state"
 fi
 else
 if [ -f /jffs/scripts/CheckUser/Phone3Home ]
 then
  rm -f /jffs/scripts/CheckUser/Phone3Home
  curl -X PUT --header "Content-Type: text/plain" --header "Accept: application/json" -d "OFF" "http://192.168.188.10:8080/rest/items/PresencePhone3/state"
 fi
fi ```
