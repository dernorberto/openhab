Creating an alias for USB Z-Wave device because it's never guaranteed it will be the same, /dev/ttyACM0 or ACM1...

### Is the device detected?

after plugging in device, check out /var/log/syslog or run dmesg|grep -i usb

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
