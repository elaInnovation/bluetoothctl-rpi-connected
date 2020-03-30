# bluetoothctl-rpi-connected
This repository can be use as a sample to use a raspbery Pi and bluetoothctl to manage ELA Innovation BLE connected mode. Through this example, you will find a series of command to use our tag, the Nordic UARD and send command to our tags. All you need is a Raspbery Pi (use for this sample a Raspbery Pi 3B+) connected to internet and a Tag from ELA Innovation to test connection and send commands.

## Install bluez

## Use bluetoothctl for connection
First of all, you need to launch bluetoothctl in a terminal. Open a terminal and then start bluetoothctl.
```bash
  sudo bluetoothctl
```

Then you are ready to use all command available from bluetooth to handle connection with you tag. If you need more information, you can use the help command to get all the primary commands available. But now, we try to connect to a specific tag called "P T EN 800633" to send command via bluetooth connected mode (Nordic UART). At this point, it's important to see the tag and show it in the device list. You can try to use the command **devices** to see if the target tag is present or not.
```bash
[bluetooth]# devices
Device F8:B6:7C:03:76:13 Cherchez moi
Device F0:68:B4:96:8B:05 BE_BLUE_T
```

If not, you can start a scan by using the scanning on/off command. Please do it!
```bash
[bluetooth]# scan on
```
You can disable the scanner when the operation of scanning is complete by using the following command.
```bash
[bluetooth]# scan off
```

Now you're available to start a connection with the tag. The most important thing at this point is to know the target mac address of your tag. For this sample, the mac address used will be the tag used for the demo. So our mac address will be the following : D6:18:5A:07:A0:81. But please consider that you have to change it by using yours.
Now it's time to connect by using your mac address:
```bash
[bluetooth]# connect D6:18:5A:07:A0:81
```

Be patient, the operation of connecting can tag a few seconds. If the operation succeed, a message will print with all the information of services and characteristics available in the tag.
```bash
Attempting to connect to D6:18:5A:07:A0:81
[CHG] Device D6:18:5A:07:A0:81 Connected: yes
Connection successful
[NEW] Primary Service
        /org/bluez/hci0/dev_D6_18_5A_07_A0_81/service000a
        00001801-0000-1000-8000-00805f9b34fb
        Generic Attribute Profile
[NEW] Primary Service
        /org/bluez/hci0/dev_D6_18_5A_07_A0_81/service000b
        6e400001-b5a3-f393-e0a9-e50e24dcca9e
        Nordic UART Service
[NEW] Characteristic
        /org/bluez/hci0/dev_D6_18_5A_07_A0_81/service000b/char000c
        6e400002-b5a3-f393-e0a9-e50e24dcca9e
        Nordic UART TX
[NEW] Characteristic
        /org/bluez/hci0/dev_D6_18_5A_07_A0_81/service000b/char000e
        6e400003-b5a3-f393-e0a9-e50e24dcca9e
        Nordic UART RX
[NEW] Descriptor
        /org/bluez/hci0/dev_D6_18_5A_07_A0_81/service000b/char000e/desc0010
        00002902-0000-1000-8000-00805f9b34fb
        Client Characteristic Configuration
[CHG] Device D6:18:5A:07:A0:81 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
[CHG] Device D6:18:5A:07:A0:81 UUIDs: 00001801-0000-1000-8000-00805f9b34fb
[CHG] Device D6:18:5A:07:A0:81 UUIDs: 6e400001-b5a3-f393-e0a9-e50e24dcca9e
[CHG] Device D6:18:5A:07:A0:81 ServicesResolved: yes
```

For more information about service and characteristics, and for the rest of the descipton with GATT please report the [Bluetooth documentation][here_bluetooth_gatt]. Now you can enter in the gatt menu by using the associated command. Note that once you are connected, the target into bracet is not **bluetooth** anymore but your target **localname** tag.
```bash
[P T EN 800633]# menu gatt
```

If you need to see the different services and characteristics, you can use the command **list-attributes** with your target mac address. The attribute (address) will be useful to do the right operation of connections with your device.
```bash
[P T EN 800633]# list-attributes D6:18:5A:07:A0:81
Primary Service
        /org/bluez/hci0/dev_D6_18_5A_07_A0_81/service000a
        00001801-0000-1000-8000-00805f9b34fb
        Generic Attribute Profile
Primary Service
        /org/bluez/hci0/dev_D6_18_5A_07_A0_81/service000b
        6e400001-b5a3-f393-e0a9-e50e24dcca9e
        Nordic UART Service
Characteristic
        /org/bluez/hci0/dev_D6_18_5A_07_A0_81/service000b/char000c
        6e400002-b5a3-f393-e0a9-e50e24dcca9e
        Nordic UART TX
Characteristic
        /org/bluez/hci0/dev_D6_18_5A_07_A0_81/service000b/char000e
        6e400003-b5a3-f393-e0a9-e50e24dcca9e
        Nordic UART RX
Descriptor
        /org/bluez/hci0/dev_D6_18_5A_07_A0_81/service000b/char000e/desc0010
        00002902-0000-1000-8000-00805f9b34fb
        Client Characteristic Configuration
```

Now it's time to enable the right properties ofor the connection and use the appropriate commands to acquire notification and writing around your **Nordic UART characteristcs**. First of all, you need to acquire the notification on the **Nordic UART RX**. So find the **attribute** of **RX** and select it. And then acquire the notification signal.
```bash
[P T EN 800633]# select-attribute /org/bluez/hci0/dev_D6_18_5A_07_A0_81/service000b/char0000e
[P T EN 800633:/service000b/char000e]# acquire-notify
[CHG] Attribute /org/bluez/hci0/dev_D6_18_5A_07_A0_81/service000b/char000e NotifyAcquired: yes
AcquireNotify success: fd 7 MTU 63
```
Note that if the operation is successfull, the return value show you the MTU value with an FD handle. You can reuse it for writing and notification operation. 

Do the same operation to acquire writing on the **Nordic TX** by executing the following commands line.
```bash
[P T EN 800633]# select-attribute /org/bluez/hci0/dev_D6_18_5A_07_A0_81/service000b/char0000c
[P T EN 800633:/service000b/char000c]# acquire-write
[CHG] Attribute /org/bluez/hci0/dev_D6_18_5A_07_A0_81/service000b/char000c WriteAcquired: yes
AcquireWrite success: fd 8 MTU 63
```

Now it's time to write. You can do it by using direcctly the write function, but for this step we will describe how to do the work with to additionnal terminal and the file descriptor. Open two additionnal terminal and in he forst one, you can plug directly on the notificaiton signal (use the FD handle given before).
```bash
sudo cat /proc/$(pgrep bluetoothctl)/fd/7
```
In the second on, use the followind commands:
```bash
sudo -sE
cat > /proc/$(pgrep bluetoothctl)/fd/8
```

Now it's time to send command. Use the ELA Tags documentation to send the appropriate commands to the tag and tests the connection, and the function release by your tag.

Once, you can diconnect from the tag and aexit.
```bash
[P T EN 800633:/service000b/char000c]# back
[P T EN 800633:/service000b/char000c]# disconnect D6:18:5A:07:A0:81
Notify closed
Write closed
[CHG] Device D6:18:5A:07:A0:81 ServicesResolved: no
Successful disconnected
[CHG] Device D6:18:5A:07:A0:81 Connected: no
[bluetooth]# exit
```

[here_bluetooth_gatt]: https://www.bluetooth.com/specifications/gatt/
