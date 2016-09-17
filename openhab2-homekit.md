## Instructions
ref: https://community.openhab.org/t/setting-up-oh2-and-homekit/13709

1. add your devices/things with Paper UI, really easy!
2. create manually new items for homekit with a text file calling it xxx.items and placing it into /conf/items/ folder. Follow these instructions12
3: go to Habmin / Configuration / Items and just check that your manually created items are listed and have the correct TAB and GROUP
3. then go to Habmin / Configuration / Things and select the thing you already created with Paper UI. Then open its channels, select the one you want to link (normally the switch channel) and what do you need to do now is press the button link channel to an existing item and select the item you created manually
4. Done!

## Config
### /conf/services/HomeKit.cfg
```org.openhab.homekit:port=9124
org.openhab.homekit:pin=031-45-154
org.openhab.homekit:useFahrenheitTemperature=true
org.openhab.homekit:thermostatCoolMode=CoolOn
org.openhab.homekit:thermostatHeatMode=HeatOn
org.openhab.homekit:thermostatAutoMode=Auto
org.openhab.homekit:thermostatOffMode=Off
org.openhab.homekit:networkInterface=192.168.0.6
```

## sample items file
```
Switch CornerLight "corner Light" <light> (gLounge) [ "Lighting" ]
Switch LivingRoomLight "Living Room Light" <light> (gLivingRoom) [ "Lighting" ]
Dimmer LivingRoomLight "Living Room dimmer" <light> (gLivingRoom) [ "Lighting" ]
Number EllieTemperature "Ellie Bedroom Temperature" (gEllie) [ "CurrentTemperature" ]
Number LoungeTemperature "Lounge Current Temperature" (gLounge) [ "CurrentTemperature" ]
Number LoungeThermostat "LoungeThermostat Target Temperature" (gLounge) [ "TargetTemperature" ]
```

## config file
### items/homekit.items
```
Switch CornerLight "corner Light" <light> (gLounge) [ "Lighting" ]
Dimmer LivingRoomLight "Living Room dimmer" <light> (gLivingRoom) [ "Lighting" ]
Number BedroomTemperature "Bedroom Temperature" (gBedroom) [ "CurrentTemperature" ]
Number OfficeTemperature "Office Temperature" (gOffice) [ "CurrentTemperature" ]
Number LivingRoomTemperature "Living Room Temperature" (gLivingRoom) [ "CurrentTemperature" ]
Number EllieTemperature "Ellie Bedroom Temperature" (gEllie) [ "CurrentTemperature" ]
Number LoungeTemperature "Lounge Current Temperature" (gLounge) [ "CurrentTemperature" ]
Number BedroomThermostat "Bedroom Thermostat" (gLounge) [ "TargetTemperature" ]
Number OfficeThermostat "Office Thermostat" (gLounge) [ "TargetTemperature" ]
Number LivingRoomThermostat "Living Room Thermostat" (gLounge) [ "TargetTemperature" ]
Number EllieThermostat "Ellie Thermostat" (gLounge) [ "TargetTemperature" ]
Number LoungeThermostat "Lounge Thermostat" (gLounge) [ "TargetTemperature" ]
Number LivingRoomButtonBattery "Living Room Button Battery" <light> (gLivingRoom) [ "Lighting" ]
```
