bluez-experimental
==================

* MOTIVATION

I am building an application which needs to manage Bluetooth LE devices such as
Heart Rate monitors, cadence monitors, etc...

My development is based in the new dbus API for GATT introduced in Bluez in version 5.x.This
 feature is still under the experimental flag.

The problem is that when running 'bluetoothd' with -E (experimental flat) it also starts other
experimenta services as: heartrate, cyclingspeed etc...

In order to avoid conflicts if a device is already used by a dbus service it won't show up in the other services.

In example:

My device exports a Cycling Speed and Cadence service.

`  
[RPM]# info
  Device C5:62:A1:0A:E6:ED
	Name: RPM
	Alias: RPM
	Appearance: 0x0485
	Paired: yes
	Trusted: yes
	Blocked: no
	Connected: yes
	LegacyPairing: no
	UUID: Generic Access Profile    (00001800-0000-1000-8000-00805f9b34fb)
	UUID: Generic Attribute Profile (00001801-0000-1000-8000-00805f9b34fb)
	UUID: Device Information        (0000180a-0000-1000-8000-00805f9b34fb)
	UUID: Battery Service           (0000180f-0000-1000-8000-00805f9b34fb)
	UUID: Cycling Speed and Cadence (00001816-0000-1000-8000-00805f9b34fb)
	UUID: Vendor specific           (a026ee01-0a7d-4ab3-97fa-f1500f9feb8b)
	UUID: Vendor specific           (a026ee03-0a7d-4ab3-97fa-f1500f9feb8b)
[RPM]#  

`

When list attributes to be used by GATT Service the service 0x1816 (RPM) doesn't show up:


`
[RPM]# list-attributes C5:62:A1:0A:E6:ED
Service /org/bluez/hci0/dev_C5_62_A1_0A_E6_ED/service001a Battery Service (Primary)
Characteristic /org/bluez/hci0/dev_C5_62_A1_0A_E6_ED/service001a/char001b Battery Level
Descriptor /org/bluez/hci0/dev_C5_62_A1_0A_E6_ED/service001a/char001b/desc001d Client Characteristic Configuration
Service /org/bluez/hci0/dev_C5_62_A1_0A_E6_ED/service0025 Vendor specific (Primary)
Characteristic /org/bluez/hci0/dev_C5_62_A1_0A_E6_ED/service0025/char0026 Vendor specific
Descriptor /org/bluez/hci0/dev_C5_62_A1_0A_E6_ED/service0025/char0026/desc0028 Client Characteristic Configuration
Characteristic /org/bluez/hci0/dev_C5_62_A1_0A_E6_ED/service0025/char0029 Vendor specific
Descriptor /org/bluez/hci0/dev_C5_62_A1_0A_E6_ED/service0025/char0029/desc002b Client Characteristic Configuration
Service /org/bluez/hci0/dev_C5_62_A1_0A_E6_ED/service002c Vendor specific (Primary)
Characteristic /org/bluez/hci0/dev_C5_62_A1_0A_E6_ED/service002c/char002d Vendor specific
Descriptor /org/bluez/hci0/dev_C5_62_A1_0A_E6_ED/service002c/char002d/desc002f Client Characteristic Configuration
`

That is because is taken by the experimental cyclingspeed bluez service.

* SOLUTION

Remove from Makefile.plugins the conflicting services and recompile again.

* REFERENCES

http://www.bluez.org/

linux-bluetooth mailing list issue: http://thread.gmane.org/gmane.linux.bluez.kernel/61649/focus=61652

* ABOUT THIS REPOSITORY

This repository has been duplicated from: https://git.kernel.org/pub/scm/bluetooth/bluez.git

`
git clone --bare https://git.kernel.org/pub/scm/bluetooth/bluez.git orig-repo

cd orig-repo

git push --mirror https://github.com/jomarmar/bluez-experimental.git

cd ..

rm -rf orig-repo

git clone https://github.com/jomarmar/bluez-experimental.git
`

Then I have change remotes to be able to update from the original repository and push to my own copy of it.

`
git remote set-url origin https://git.kernel.org/pub/scm/bluetooth/bluez.git
git remote set-url --push origin https://github.com/jomarmar/bluez-experimental.git
`

* COMPILE WITH EXPERIMENTAL FLAG *

See README.
Anyway:
`
  ./bootstrap
  ./configure --enable-experimental --enable-library
`
