# Trigger a Z-Wave switch based on the value of a Hue dimmer

## Components

* Philips Hue dimmer, has no Thing nor Item
* Philips Hue Light Bulb, item name `Light_02`
* Z-Wave Switch, item name `Switch_2_switch` (used to turn cheap LED strip ON & OFF)

## Expected behavior

* when dim value of the hue light controlled by the dimmer (or the Hue app) changes below or above a certain value
** trigger the switch ON or OFF

## Rule

```
val String message
val Number lightThreshold = 5

rule "Light_command_choice"
when
   Item Light_02 changed
then
    val dimmerValue = Light_02.state as DecimalType
    if(dimmerValue > lightThreshold) {
        message = "Turned Other Lights on together with LivingRoom Light"
        sendCommand(Switch_2_switch, ON)
        logInfo("Switches", message)
    }
    else if(dimmerValue <= lightThreshold ) {
      message="Turned Other Lights OFF together with LivingRoom Light"
      sendCommand(Switch_2_switch, OFF)
      logInfo("Switches", message)
    }
end
```
