## The premise

* WIFI Router regularly searches for specific MAC Addresses in his Wifi clients list
* if it finds a valid one, the router updates the status of an openhab2 item via a REST API PUT request

## openhab2 config

### presence.items

```
Group gPresence (All)

Switch MobileDevice1 "Mobile Device 1 Name" <switch>        (gPresence) 
Switch MobileDevice2 "Mobile Device 2 Name" <switch>        (gPresence) 
```

### presence.sitemap

```
sitemap presence label="presence" {
        Frame label="Mobile Devices" {
                Switch item=MobileDevice1
                Switch item=MobileDevice2
        }
}

```

## AsusWRT

Note: more information about JFFS: https://github.com/RMerl/asuswrt-merlin/wiki/JFFS

### crontab file to import

* /jffs/configs/**cron**

`*/15 * * * * /jffs/scripts/CheckUser/checkIfHome.sh >/dev/null 2>&1`

Note: example runs every 15 minutes

### script to import crontab on every boot

* /jffs/scripts/**services-start**

```
#!/bin/sh
sleep 10
/usr/bin/crontab /jffs/configs/cron
```

### show crontab

> cru l

or

> crontab -l

### edit crontab

> crontab -e


### script for Presence detection

Notes:
* This version of the script makes a request to openhab every time it checks each device, and another request if it changes the presence value
* to find out correct curl command, use REST API, search for items and update the state of an item
* the wifi detection command depends on the Asus Router model, in this case it's an RT-N66U running AsusWRT
* the logic is simple:
  * if MAC address is found, then set Switch Item to ON, only if not already ON
  * if MAC Address is NOT found, then set Switch Item to OFF, only it not already OFF
* it takes a relative minute to detect that a device is not associated with the Wifi router anymore, but seconds to detect it come on

### The script itself

* /jffs/scripts/CheckUser/**checkIfHome.sh**

```
#!/bin/sh

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
 *AA:AA:AA:AA:AA:AA*)
 Phone1=Home
 ;;
 *)
 Phone1=Away
esac

case "$macadresser" in
 *BB:BB:BB:BB:BB:BB*)
 Phone2=Home
 ;;
 *)
 Phone2=Away
esac

if [ "$Phone1" = Home ]
then
  query="$(curl -X GET "http://<openhab2 IP>:8080/rest/items/MobileDevice1/state")"
  if [ $query != "ON" ]
  then
    # set value to ON
    curl -X PUT --header "Content-Type: text/plain" --header "Accept: application/json" -d "ON" "http://<openhab2 IP>:8080/rest/items/MobileDevice1/state"
  fi
fi

if [ "$Phone1" = Away ]
then
  query="$(curl -X GET "http://<openhab2 IP>:8080/rest/items/MobileDevice1/state")"
  if [ $query != "OFF" ]
  then
    # set value to OFF
    curl -X PUT --header "Content-Type: text/plain" --header "Accept: application/json" -d "OFF" "http://<openhab2 IP>:8080/rest/items/MobileDevice1/state"
  fi
fi

if [ "$Phone2" = Home ]
then
  query="$(curl -X GET "http://<openhab2 IP>:8080/rest/items/MobileDevice2/state")"
  if [ $query != "ON" ]
  then
    # set value to ON
    curl -X PUT --header "Content-Type: text/plain" --header "Accept: application/json" -d "ON" "http://<openhab2 IP>:8080/rest/items/MobileDevice2/state"
  fi
fi

if [ "$Phone2" = Away ]
then
  query="$(curl -X GET "http://<openhab2 IP>:8080/rest/items/MobileDevice2/state")"
  if [ $query != "OFF" ]
  then
    # set value to OFF
    curl -X PUT --header "Content-Type: text/plain" --header "Accept: application/json" -d "OFF" "http://<openhab2 IP>:8080/rest/items/MobileDevice2/state"
  fi
fi

```
