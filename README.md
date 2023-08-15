# My account will be closed soon.
Microsoft requires 2FA. I will never use that. So this accout will be closed soon. Sorry about that.

# Simple bash scripts to read temperature data from SwitchBot devices

This is a collection of linux bash shell scripts for reading
SwitchBot's temperature sensors. The scripts use `bluetoothctl` to
receive the data from the devices. Therefore the package `bluez` of
your linux-distribution must be installed.

The scripts must either have execution rights or be started with `bash`.

## `scan_sensors`

This is a simple script to search for the sensors and get their
address (MAC). It runs endless and must be aborted with `Ctrl-C`
when all sensors have been found.

The sensors should be close to the receiver to ensure proper reception.

To speed up the process, you should press and hold the bluetooth
button of the devices until the LED flashes or the bluetooth symbol
appears on the display. This causes the devices to send data even
though no value has changed.

Example:
```
$ bash scan_sensors
Scanning for sensors. Use Ctrl-C to exit ...
Found WoSensorTH: XX:XX:XX:XX:XX:XX
Found WoIOSensorTH: YY:YY:YY:YY:YY:YY
Found WoSensorTHP: ZZ:ZZ:ZZ:ZZ:ZZ:ZZ
Test AA:AA:AA:AA:AA:AA ...
```

The result shows the type of device and the MAC address after a while.

You need these MAC values to configure the `sensor_config` file.

| Type         | Description                                |
| ------------ | ------------------------------------------ |
| WoSensorTH   | SwitchBot Meter                            |
| WoSensorTHP  | SwitchBot Meter Plus                       |
| WoIOSensorTH | SwitchBot Indoor/Outdoor Thermo-Hygrometer |

## Configure your sensors

The file `sensor_config` contains the sensors configurations. Edit the
file to insert the MAC addresses of your devices. Make sure you have
either modified or removed the samples in this file. Otherwise, the
tools will prompt you to edit the file.

## `log_sensors`

This tool scans for bluetooth messages and filter out the SwitchBot
temperature messages.

```
$ bash log_sensors
2023-07-08 12:01:42 EA:D8:03:13:C0:33: outdoor sensor        30.0°C / 30%
2023-07-08 12:02:38 E4:16:56:54:8F:5E: room sensor           22.8°C / 47%
2023-07-08 12:05:33 EA:D8:03:13:C0:33: outdoor sensor        30.1°C / 30%
2023-07-08 12:11:50 E4:16:56:54:8F:5E: room sensor           22.8°C / 47%
2023-07-08 12:14:46 E4:16:56:54:8F:5E: room sensor           22.8°C / 48%
2023-07-08 12:21:28 EA:D8:03:13:C0:33: outdoor sensor        30.3°C / 31%
```

A new row will appear each time one of your SwitchBot sensors sends
new data. It may take a while before the first values are
displayed. The tool does not actively poll the device, but waits for
the sensors to send data.

You may want to redirect the output into a log file. Then use
```
bash log_sensors > sensors.log
```
or
```
bash log_sensors | tee sensors.log
```

## `show_sensors`

This tool shows a table of all configured sensors with the data
received from the sensors.  Shortly after the start, the output looks
like this:

```
Name                     Temp Humidity Last Update         Signal Quality
==================== ======== ======== =================== ==============
room sensor           no data  no data -                   [        ] -
outdoor sensor        no data  no data -                   [        ] -
```

After a while the tool has retrieved some data and shows the last
received values from the sensors:

```
Name                     Temp Humidity Last Update         Signal Quality
==================== ======== ======== =================== ==============
room sensor            23.2°C      51% 2023-07-09 08:43:29 [******  ] -50
outdoor sensor         25.0°C      51% 2023-07-09 08:40:33 [***     ] -86
```

The column "Last Update" shows when the last data was received. For
sensors that are further away, it can take me more than an hour to
receive data. The last column shows the signal quality with the RSSI
value.

You have to use `Ctrl-C` to exit the program.
