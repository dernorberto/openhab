# setting up a scene switch and assigning actions to button presses

## Refs

* https://community.openhab.org/t/nodon-octan-remote-in-oh2/18888
* https://github.com/openhab/org.openhab.ui.habmin/wiki/ZWave-Device-Installation

## device

nodon - The Soft Remote http://nodon.fr/en/

# inclusion and identification

* As per https://github.com/openhab/org.openhab.ui.habmin/wiki/ZWave-Device-Installation, inclusion of battery operated devices requires a manual NIF in order to speed things up
  * after including device, you'll need to trigger sending the Node Information Frame (NIF) by: 1) holding 1+2 for 3 seconds, then hit button 3, the LED will blink
* Configuration Parameters and Association Groups must be changed for it to function
  * as of OH2.4M6, the following discusses the issue of configuring things with .things files and not being able to change device properties - https://github.com/eclipse/smarthome/pull/3245
  * my workaround: setup thing with both .thing and via PaperUI, the node ID will be different anyway
* in Habmin: Configuration Parameters
  1. Button 1 and 2 profile = Scene
  2. Button 3 and 4 profile = Scene
  3. Scene Type = Central Scene
* in Habmin: Association Groups
  * 1-7 set to *openHAB Controller*

# Behavior

* with the item set to Number, updates will be "b.c":  
  * b = button: 1-4  
  * c = command: 0-3  
* state is then:  
  * 1.0 = button 1 pressed 1x
  * 3.1 = button 3 pressed 1x
  * 3.2 = button 3 pressed long
  * 3.3 = button 3 pressed 2x
  
# Item

```
Number Nodon_Remote "Remote"    {channel="zwave:device:a923924a:node30:scene_number"}
```

# Rule

```
val String mailTo = "email@domain.com"
val String message = "sent by openhab2"

rule "let me know when scene is enabled"
when Item Nodon_Remote received update
then
    message = "Soft Remote Button Pressed"+" "+ Nodon_Remote.state.toString
    logInfo("Switch", message)
        if (Nodon_Remote.state == 1.0 || Nodon_Remote.state == 1.1) {
                if (Hallway_Lamp.state == OFF) {
                        message = "button 1"
                        logInfo("Scenes",message)
                        sendMail(mailTo, "Scene Switch", message)
                } else {
                        message = "button other"
                        logInfo("Scenes",message)
                        sendMail(mailTo, "Scene Switch", message)
                }
        }
end
```

## Log output
```
2018-11-25 13:55:07.204 [INFO ] [clipse.smarthome.model.script.Switch] - Soft Remote Button Pressed 2.0
2017-03-08 20:03:33.860 [ItemStateChangedEvent     ] - Nodon_Remote changed from 1.0 to 2.0
2017-03-08 20:03:53.232 [ItemStateChangedEvent     ] - Nodon_Remote changed from 3.0 to 1.2
```
