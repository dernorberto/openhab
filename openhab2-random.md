# Pick a random choice out of an array

Source: https://community.openhab.org/t/help-how-to-get-a-random-string-item-from-list/7844/5

## Current Situation

* When we put our kids down, specific Sonos Favorites are played on the Sonos speaker in their room
* Script runs every day at a specific time
* If I need to pick something else to play I need to either edit the script or pick something else on the Sonos App
* The Spotify Albums have been added to Sonos as Favorites

## Planned Situation

* Play an album picked at random from a pre-defined listm in other words "pick random item from a list"
* All 8 "Leo Lausemaus" Episodes 1-8 on Spotify are added to an Array and one is randomly picked

## sonos.items

* I won't be using all the Items

```
//sonos Kids
Number  sonos_KD_volume "Volume: [%.1f %%]" <soundvolume> (gSonosKids) {channel="sonos:PLAY1:RINCON_XXXXXXXXXXXXXXX:volume"}
Player  sonos_KD_control "Control: [%s]" (gSonosKids) {channel="sonos:PLAY1:RINCON_XXXXXXXXXXXXXXX:control"}
String  sonos_KD_currentalbum "Album: [%s]" <text> (gSonosKids) {channel="sonos:PLAY1:RINCON_XXXXXXXXXXXXXXX:currentalbum"}
String  sonos_KD_currentartist "Artist: [%s]" <text> (gSonosKids) {channel="sonos:PLAY1:RINCON_XXXXXXXXXXXXXXX:currentartist"}
String  sonos_KD_currenttitle "Title: [%s]" <text> (gSonosKids) {channel="sonos:PLAY1:RINCON_XXXXXXXXXXXXXXX:currenttitle"}
String  sonos_KD_favorite "Favorite: [%s]" <text> (gSonosKids) {channel="sonos:PLAY1:RINCON_XXXXXXXXXXXXXXX:favorite"}
String  sonos_KD_playlist "Playlist: [%s]" <text> (gSonosKids) {channel="sonos:PLAY1:RINCON_XXXXXXXXXXXXXXX:playlist"}
String  sonos_KD_state "State: [%s]" <text> (gSonosKids) {channel="sonos:PLAY1:RINCON_XXXXXXXXXXXXXXX:state"}
String  sonos_KD_repeat "Repeat: [%s]" <text> (gSonosKids) {channel="sonos:PLAY1:RINCON_XXXXXXXXXXXXXXX:repeat"}
```

## sonos.rules

### start playback

* Volume set to 8
* Repeat set to ALL
* Add to openhab2 log (/var/log/openhab2.log) what was picked
> 2017-09-15 21:23:02.948 [INFO ] [eclipse.smarthome.model.script.Sonos] - Kids playing: Leo Folge 2 at 8 volume

```
var String message = "sent by openhab2"
var Number startVolume = 12
val java.util.Random rand = new java.util.Random

rule "sonos_Kids_control_Stories"
when
    Time cron "0 10 20 1/1 * ? *"
then
    startVolume = 10
    var String startVolumeString = startVolume.toString
    var String Title
    var Playlists = newArrayList(
      "Leo Folge 1",
      "Leo Folge 2",
      "Leo Folge 3",
      "Leo Folge 4",
      "Leo Folge 5",
      "Leo Folge 6",
      "Leo Folge 7",
      "Leo Folge 8"
    )
    var i = rand.nextInt(8);
    Title = Playlists.get(i)

    message = "Kids playing: " + Title + " at " + startVolumeString + " volume"
    logInfo("Sonos", message)
    sendCommand(sonos_KI_favorite, Title)
    sendCommand(sonos_KI_control, PLAY)
    sendCommand(sonos_KI_volume, startVolume)
    sendCommand(sonos_KI_repeat, "ALL")
end
```

### stop playback

```
rule "sonos_Kids_control_OFF"
when
    Time cron "0 10 21 1/1 * ? *"
then
    logInfo("Sonos", "Turned Kids Sonos OFF")
    sendCommand(sonos_KD_control, PAUSE)
end

```


### logs

```
2017-09-15 22:02:03.732 [ItemStateChangedEvent     ] - sonos_KI_favorite changed from Leo Folge 7 to Leo Folge 3
2017-09-15 22:02:03.741 [ItemStateChangedEvent     ] - sonos_KI_repeat changed from OFF to ALL
2017-09-15 22:02:03.760 [ItemCommandEvent          ] - Item 'sonos_KI_control' received command PLAY
2017-09-15 22:02:03.776 [ItemStateChangedEvent     ] - sonos_KI_control changed from PAUSE to PLAY
2017-09-15 22:02:03.779 [ItemStateChangedEvent     ] - sonos_KI_currentartist changed from Foo Fighters to Leo Lausemaus
2017-09-15 22:02:03.781 [ItemStateChangedEvent     ] - sonos_KI_currentalbum changed from Concrete and Gold to Folge 3
2017-09-15 22:02:03.782 [ItemStateChangedEvent     ] - sonos_KI_currenttitle changed from The Sky Is A Neighborhood to Leo Lausemaus Titelsong
2017-09-15 22:02:03.784 [ItemStateChangedEvent     ] - sonos_KI_state changed from PAUSED_PLAYBACK to STOPPED
2017-09-15 22:02:03.787 [ItemStateChangedEvent     ] - sonos_KI_control changed from PLAY to PAUSE
2017-09-15 22:02:03.796 [ItemCommandEvent          ] - Item 'sonos_KI_volume' received command 8
2017-09-15 22:02:03.799 [ItemStateChangedEvent     ] - sonos_KI_volume changed from 0.16000000 to 8
2017-09-15 22:02:04.078 [ItemStateChangedEvent     ] - sonos_KI_volume changed from 8 to 0.08000000
2017-09-15 22:02:04.105 [ItemCommandEvent          ] - Item 'sonos_KI_repeat' received command ALL
2017-09-15 22:02:04.508 [ItemStateChangedEvent     ] - sonos_KI_state changed from STOPPED to PLAYING
2017-09-15 22:02:04.518 [ItemStateChangedEvent     ] - sonos_KI_control changed from PAUSE to PLAY
```
