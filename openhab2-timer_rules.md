# Simple timer in a rule

For example, when a motion detector changes from ON to OFF (motion timer reset), then time when a light will effectively go off.  
Resetting the timer is important to put in the rule where motion is detected again (from OFF to ON) to avoid having the timer from something it shouldn't anymore.  

In my case:  
1. detect motion => light set to 100%  
2. motion reset (set in the motion detector settings via Habmin) => light set to 40% and start timer  
3. timer finishes => light set to 0%  
4. if motion, then timer is reset and light set to 100%  

## Declare timer
var Timer <timer_name> = null

## To just run the timer
```
       <timer_name> = createTimer(now.plusSeconds(<timer_in_secs>)) [|
        sendCommand(<item>, OFF)
        message="doing something after the timer is done"
        logInfo("<Category>", message)
        <timer_name> = null
        ]
```

## can also be wrapped to make sure it's only set if already 0
```
    if(<timer_name> == null) {
        <timer_name> = createTimer(now.plusSeconds(<timer_in_secs>)) [|
        sendCommand(<item>, <command>)
        message="doing something after the timer is done"
        logInfo("<Category>", message)
        <timer_name> = null
        ]
    }
```

## resetting a timer
```
    if(<timer_name> != null) {
        <timer_name>
        <timer_name> = null
    }
```
