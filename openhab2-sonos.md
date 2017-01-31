# Controlling and scheduling Sonos

I don't care about interactively controlling Sonos because I can do that from any smartphone, tablet or desktop App.  
What matters to me is:  
* that my favorite radio (currently: Rock the Cradle) is playing in the kitchen when I get home at 16h  
* that sleeping music for my kids (currently: Janina) is playing at the same time every day  
* that they stop at a given time too  

## Considerations:  
* the things were created in Paper, so no need to set them up myself again  
* my select method to call an existing playlist/album/radio is to have it as a Sonos Favorite and play that  
  * each Sonos Favorite can be renamed in case it contains special characters  
* I am not using REPEAT properly, it only works for the current track or queue and I am using a Playlist  

## Requirements:  
* Sonos binding - http://docs.openhab.org/addons/bindings/sonos/readme.html
* Sonos devices discovered and added as things  

## sonos.items
```
Group gSonos
Number  sonos_EL_volume "Volume: [%.1f %%]" <soundvolume> (gSonos) {channel="sonos:PLAY1:RINCON_949F3E047AC601400:volume"}
Player  sonos_EL_control "Control: [%s]" (gSonos) {channel="sonos:PLAY1:RINCON_949F3E047AC601400:control"}
String  sonos_EL_currentalbum "Album: [%s]" <text> (gSonos) {channel="sonos:PLAY1:RINCON_949F3E047AC601400:currentalbum"}
String  sonos_EL_currentartist "Artist: [%s]" <text> (gSonos) {channel="sonos:PLAY1:RINCON_949F3E047AC601400:currentartist"}
String  sonos_EL_currenttitle "Title: [%s]" <text> (gSonos) {channel="sonos:PLAY1:RINCON_949F3E047AC601400:currenttitle"}
String  sonos_EL_favorite "Favorite: [%s]" <text> (gSonos) {channel="sonos:PLAY1:RINCON_949F3E047AC601400:favorite"}
String  sonos_EL_playlist "Playlist: [%s]" <text> (gSonos) {channel="sonos:PLAY1:RINCON_949F3E047AC601400:playlist"}
String  sonos_EL_state "State: [%s]" <text> (gSonos) {channel="sonos:PLAY1:RINCON_949F3E047AC601400:state"}
String  sonos_EL_repeat "Repeat: [%s]" <text> (gSonos) {channel="sonos:PLAY1:RINCON_949F3E047AC601400:repeat"}
```

## sonos.rules
```
rule "sonos_EL_control_ON"
when
    Time cron "0 15 19 1/1 * ? *"
then
    var Number startVolume = 8
    var String Title = "Janina" // a playlist under Sonos Favourite called "Janina"
    var String startVolumeString = startVolume.toString
    message = "Office playing: " + Title + " at " + startVolumeString + " volume"
    if (sonos_EL_control.state == PLAY) {
      var String currentlyplaying = "Currently playing: " + sonos_OF_currentartist + sonos_OF_currenttitle
      logInfo("Sonos", currentlyplaying)
    } else {
      logInfo("Sonos", message)
      sendCommand(sonos_EL_favorite, Title)
      sendCommand(sonos_EL_control, PLAY)
      sendCommand(sonos_EL_volume, startVolume)
      sendCommand(sonos_EL_repeat, ON)
end

rule "sonos_EL_control_OFF"
when
    Time cron "0 0 21 1/1 * ? *"
then
    logInfo("Sonos", "Turned Ellie SONOS OFF")
    sendCommand(sonos_EL_control, PAUSE)
end
```
