# Openhab network devices

Ref: https://github.com/openhab/openhab2-addons/tree/master/addons/binding/org.openhab.binding.network  
How to setup an item that indicates if a network device like a PS3 is online. To be seen as online, the device has to respond to ping or have a TCP port open.  

I use it to detect if a PS3 is running and not let the motion detector timer reset turn off the lights.

# Requirements

* network binding

# things

```
network:device:<device_name> [ hostname="<IP_address>", port="0", retry="1", timeout="5000", refresh_interval="60000", use_system_ping="false", dhcplisten="false" ]
```

# items

```
Switch <item_name> (<group>) { channel="network:device:<device_name>:online" }
```

# use in rules

```
    if (<item_name>.state == ON) {
    sendCommand(<other_device>, <command>)
    message = "device is on, so I am doing something"
    logInfo("<Category>", message)
    } else {
    sendCommand(<other_device>, <command>)
    ...
    }
```
