### related community posts

* https://community.openhab.org/t/zwave-binding-updates/51080/231?u=lenorb
* https://community.openhab.org/t/zwave-manual-thing-configuration/26542

## Setting up z-wave devices in .things files

* it would be nice not to rely on (PaperUI or habmin) discovery to setup z-wave things

### Caveats

* after setting up a thing in a .thing file, it's not possible to change any of the properties via Habmin
* if a change is attempted, the following shows in the logs:

```
2018-11-25 12:03:57.127 [INFO ] [st.core.internal.thing.ThingResource] - Received HTTP PUT request for update configuration at 'things/zwave:nodon_softremote_00_000:zStickController:node56/config' for an unmanaged thing 'zwave:nodon_softremote_00_000:zStickController:node56'.
```

* on the other hand, if the device settings are managed via discovery, then changing settings in habmin work

```
2018-11-25 12:26:48.654 [vent.ConfigStatusInfoEvent] - ConfigStatusInfo [configStatusMessages=[ConfigStatusMessage [parameterName=group_4, type=PENDING, messageKey=null, arguments=null, message=null, statusCode=null], ConfigStatusMessage [parameterName=group_1, type=PENDING, messageKey=null, arguments=null, message=null, statusCode=null], ConfigStatusMessage [parameterName=group_3, type=PENDING, messageKey=null, arguments=null, message=null, statusCode=null], ConfigStatusMessage [parameterName=group_2, type=PENDING, messageKey=null, arguments=null, message=null, statusCode=null]]]
```

### Good things though

* the fact that the manual and the discovery setup of Things uses  different thingids, it means that both Things can be setup at the same time for the same device, one for manual config if required and one for discovery, which is used to change device parameters and settings, but it's probably just better to have only the Thing via discovery anyway