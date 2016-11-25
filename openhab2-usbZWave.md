## using Aeon Labs Z-Wave Stick Gen5

* The stick has to be unplugged and plugged in every time a thing has to be included or excluded of the z-wave network
* initially device is assigned `/dev/ttyACM0`, which is a string you need to specify in the openhab2 z-wave binding
* when unplugging and plugging back, there is a high chance that the device is assigned another name, `/dev/ttyACM1`
* openhab2 won't be able to use it anymore
* the solution is to create an alias for all devices matching the Vendor and hardware attributes

ref: https://github.com/openhab/openhab/wiki/symlinks

### Is the device detected?

after plugging in device, check out `/var/log/syslog` or run 
> dmesg|grep -i usb

```
[80587.895966] usb 1-1.2: new full-speed USB device number 4 using dwc_otg
[80587.999160] usb 1-1.2: New USB device found, idVendor=0658, idProduct=0200
[80587.999182] usb 1-1.2: New USB device strings: Mfr=0, Product=0, SerialNumber=0
[80588.071155] cdc_acm 1-1.2:1.0: ttyACM0: USB ACM device
[80588.071930] usbcore: registered new interface driver cdc_acm
[80588.071942] cdc_acm: USB Abstract Control Model driver for USB modems and ISDN adapters
```

### obtain USB stick serial

> sudo dmesg|grep -i usb

```
[ 4570.137384] usb 1-1.4: New USB device found, idVendor=0658, idProduct=0200
[ 4570.138854] cdc_acm 1-1.4:1.0: ttyACM0: USB ACM device
```

> sudo udevadm info --query=property --name=/dev/ttyACM0 | grep SERIAL

`ID_SERIAL=0658_0200`

### create rule

> sudo vi  /etc/udev/rules.d/99-usbserial.rules

`SUBSYSTEM=="tty", ATTRS{idVendor}=="0658", ATTRS{idProduct}=="0200", SYMLINK+="USBzwave", GROUP="dialout", MODE="0666"`

> sudo udevadm trigger  

... or restart RPi

the USB device is now accessible as `/dev/USBzwave`

### allow openhab to use that device as well

If not told so, openhab2 will not be able to connect to new `/dev/USBzwave` device and limit itself to the ttyACMx-named devices.
As it's the apt repository installation, we need to customize the `EXTRA_JAVA_OPTS` options.

> vi /etc/default/openhab  

* add `-Dgnu.io.rxtx.SerialPorts=/dev/USBzwave` to `EXTRA_JAVA_OPTS=""`  
* result = `EXTRA_JAVA_OPTS="-Dgnu.io.rxtx.SerialPorts=/dev/USBzwave"`  
* note: The individual serial controller separated by ":" will be the only ones openhab2 will be able to use, so if you add only `/dev/USBzwave`, openhab will not be able to use `/dev/ttyACM0`

> sudo service openhab2 restart

### define in openhab2 where to connect to the USB serial controller

* habmin
  * Configuration
  * Things
    * in this case, my *USB Serial Controller* (Aeon Labs Z-Stick Gen5)
    * Port Configuration
    * Serial Port
    * `/dev/USBzwave' then Save and keep an eye on the logs
