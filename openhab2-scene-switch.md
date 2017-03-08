# setting up a scene switch and assigning actions to button presses

## Refs

* https://community.openhab.org/t/nodon-octan-remote-in-oh2/18888
* https://github.com/openhab/org.openhab.ui.habmin/wiki/ZWave-Device-Installation

## device
nodon - The Soft Remote http://nodon.fr/en/

# inclusion and identification

* As per https://github.com/openhab/org.openhab.ui.habmin/wiki/ZWave-Device-Installation, inclusion of battery operated devices requires a manual NIF 
  * after including device, you'll need to trigger sending the Node Information Frame (NIF) by triple-clicking the button 1  
* in Habmin: Configuration Parameters
  * Scene Type = Central Scene
* in Habmin: Association Gtroups
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
2017-03-08 20:03:33.860 [ItemStateChangedEvent     ] - Nodon_Remote changed from 1.0 to 2.0
2017-03-08 20:03:53.232 [ItemStateChangedEvent     ] - Nodon_Remote changed from 3.0 to 1.2
```
