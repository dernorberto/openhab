## using Aeon Labs Z-Wave Stick Gen5

* The stick has to be unplugged and plugged in every time a thing has to be included or excluded of the z-wave network
* initially device is assigned `/dev/ttyACM0`, which is a string you need to specify in the openhab2 z-wave binding
* when unplugging and plugging back, there is a high chance that the device is assigned another name, `/dev/ttyACM1`
* with the tty name mismatch, the zwave stick will not be setup
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

`SUBSYSTEM=="tty", ATTRS{idVendor}=="0658", ATTRS{idProduct}=="0200", SYMLINK+="ttyUSB0"

the following one was not working for me, so it is better to not specify GROUP and MODE
`SUBSYSTEM=="tty", ATTRS{idVendor}=="0658", ATTRS{idProduct}=="0200", SYMLINK+="ttyUSB0", GROUP="dialout", MODE="0666"`

Note: Make sure the symlink name matches a standard Linux comm port prefix, like `ttyUSB0`, `ttyUSB1` or other... don't pick a simple name like `USBZWave`

Note: On Linux; the extra Java command-line property is not required if you choose a symlink name that matches a standard Linux comm port prefix and ends with a combination of numerics + non-letters. E.g. "ttyUSB-9999". (https://github.com/openhab/openhab1-addons/wiki/symlinks)

> sudo usermod -a -G dialout openhab
> sudo usermod -a -G tty openhab

> sudo udevadm trigger  

... or restart RPi

> ls /dev
```
crw-rw-rw-  1 root dialout 166,   1 Nov 17 21:18 ttyACM1
crw-rw----  1 root dialout 204,  64 Nov 17 21:10 ttyAMA0
crw-------  1 root root      5,   3 Nov 17 21:10 ttyprintk
lrwxrwxrwx  1 root root           7 Nov 17 21:12 ttyUSB0 -> ttyACM1
```

* Note: symlinks are root:root/777 so that anyone can access them, UDEV sets the group and permission of the real `/dev/ttyACM0` device, which is what matters

The USB device is now accessible as `/dev/ttyUSB0`

Note: When updating the Z-Wave network, where I need to unplug, operate and then plug the z-wave stick back, I always have to restart openhab2

> sudo systemctl restart openhab2
