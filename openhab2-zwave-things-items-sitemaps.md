## 5 Steps

This example is the simplest setup for a Z-Wave wall smart-plug setup in openhab2

### 1. Z-Wave Device

* include device into z-wave network
* we assume it's supported by openhab2 and is visible in PaperUI or HabPanel

### 2. openhab2 Things via Paper UI

* Paper UI via `http://<RPI-IP-ADDRESS>:8080/ui/index.html`
* *Configuration* > *Things* > pick an item, in this case a *Devolo MT02646 Home Control Metering Plug*
* top of page shows channel for node: `zwave:device:2f125f9f:node11`
* page shows individual channels, for example:
  * Switch, `zwave:device:2f125f9f:node11:switch_binary`
  * Electric meter (watts), `zwave:device:2f125f9f:node11:meter_watts`
  * etc.

### 3. openhab2 .items file with reference to thing

file path: `/etc/openhab2/items/switches.items`

```
Group gSwitch (All)
Group gLO (All)
Group gLights (All)

Switch Switch_LO_switch "Lounge Switch state" <switch> (gLights,gSwitch,gLO) {channel="zwave:device:2f125f9f:node11:switch_binary"}
Number Switch_LO_watts "Lounge Switch watts [%.2f W]" <power> (gSwitch,gLO) {channel="zwave:device:2f125f9f:node11:meter_watts"}
Number Switch_LO_kwh "Lounge Switch kWh [%.2f Kwh]" <power> (gSwitch,gLO) {channel="zwave:device:2f125f9f:node11:meter_kwh"}
```

### 4. openhab2 .sitemap file with reference to item

file path: `/etc/openhab2/sitemaps/home.sitemap`

```
Switch item=Switch_LO_switch label="Lounge"
Text item=Switch_LO_watts
Text item=Switch_LO_kwh

```

### 5. Access and Control

* via openhab iOS app
* BasicUI: `http://<RPI-IP-ADDRESS>:8080/basicui/app?sitemap=home`
* ClassicUI: `http://<RPI-IP-ADDRESS>:8080/classicui/app?sitemap=home`
