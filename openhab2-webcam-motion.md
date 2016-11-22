## Using a webcam for motion detection

Reference: https://klenzel.de/3691 (in German)

* I am not recording video, just capturing a frame every 2 seconds

### Confirming that the webcam was detected and has a device

> ls -ltrh /dev/video*

`crw-rw---- 1 root video 81, 0 Nov 14 19:11 /dev/video0`

### Installation of motion package

> sudo apt-get -V install motion

Note:
* for video capture, see reference website and ffmpeg installation instructions

### .items file

```
Group gSensor (All)
Group gMotion (gSensor)

Switch Webcam_LI_Motion "Motion Living Room" <switch> (gMotion, gLI)
```

### set to autostart when booting Pi

file: */etc/default/motion*

`start_motion_daemon=yes`

### motion.conf config file

Just follow the instructions, it's not too complicated.
Remember the `stream_port 8090` setting so that you can check the feed yourself `https://<PI_IP_ADDRESS>:8090`

For my old Creative Webcam, I had to use: 

```
...
# V4L2_PIX_FMT_JPEG    : 9  'JPEG'
...
v4l2_palette 9
...
```

### trigger action when motion is detected

File: `/etc/motion/motion.conf`

* Set item to ON

```
# Command to be executed when an event starts. (default: none)
# An event starts at first motion detected after a period of no motion defined by event_gap
# on_event_start value
on_event_start curl -X PUT --header "Content-Type: text/plain" --header "Accept: application/json" -d "ON" "http://<PI_IP_ADDRESS>:8080/rest/items/Webcam_LI_Motion/state"
```

* Set item to OFF

```
# Command to be executed when an event ends after a period of no motion
# (default: none). The period of no motion is defined by option event_gap.
# on_event_end value
on_event_end curl -X PUT --header "Content-Type: text/plain" --header "Accept: application/json" -d "OFF" "http://<PI_IP_ADDRESS>:8080/rest/items/Webcam_LI_Motion/state"
```

### rules

I can now have rules like:

```
rule "when_motion_in_LivingRoom_then_message"
when
    Item Webcam_LI_Motion changed from OFF to ON
then
...
end
```
