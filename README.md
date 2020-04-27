# STD OTAU Plugin
The deCONZ STD OTAU plugin implements a ZigBee Over-the-Air-Upgrade (OTAU) server in deCONZ.

## Upgrading Firmware
See the [Phoscon help topics](https://phoscon.de/en/support#ota-update-osram-devices) for instructions on how to upgrade the firmware of your ZigBee devices using the STD OTAU plugin.

Note that after the firmware has upgraded, the deCONZ GUI (and the REST API) might still show the old values (notably the _SW Build ID_ and _Date Code_).
To refresh these, read the _Basic_ cluster attributes, in the _Cluster info_ panel in the GUI.
Likewise, if the device exposes new or different endpoints and/or clusters (e.g. when upgrading from ZLL to ZigBee 3.0), refresh these by reading the _Node Descriptor_ and _Simple Descriptor(s)_ from the left drop-down menu on the node.

ZigBee firmware is identified by the _Manufacturer Code_, _Image Code_ and _Version_.
On startup, the STD OTAU plugin will read all `*.ota`, `*.ota.signed`, `*.sbl-ota`, and `*.zigbee` files in `~/otau`, and copy them to _mmmm_`_`_iiii_`_`_vvvvvvvv_`.zigbee` files, matching these attributes.

To find the _Manufacturer Code_ for your device, see the _Node info_ panel in the deCONZ GUI.  To find the _Image Code_ and (current) _Version_, see the _STD OTAU Plugin_ panel in the deCONZ GUI.
Select your device from the list (match the IEEE address) and press _Query_ to populate the fields.
Make sure to wake battery-powered devices before pressing _Query_.

Note that not all ZigBee devices support over-the-air firmware upgrading.
Devices that do support this, expose a client (grey) _OTAU_ cluster (0x0019).
Only these devices are listed in the _STD OTAU Plugin_ panel.
However, not all devices that advertise this cluster have actually implemented it.

You need to obtain the firmware files from the device manufacturer.
Some manufacturers have officially published their firmware.
For some others, we found the location from where their native gateway downloads the firmware.

Manufacturer | Code | Firmware Files
-- | -- | --
IKEA | 117C | Available on the [IKEA website](http://fw.ota.homesmart.ikea.net/feed/version_info.json).  Use the [ikea-ota-download.py](https://github.com/dresden-elektronik/deconz-rest-plugin/blob/master/ikea-ota-download.py) script to download the current IKEA firmware files directly to `~/otou`.
Ledvance<br>OSRAM | 1189<br>110C | Published to the [Ledvance website](https://update.ledvance.com/firmware-overview?submit=all).
Philips (Signify) | 100B | See [Hue Firmware](#hue-firmware) below.
ubisys | 10F2 | Published to the [ubisys website](http://www.ubisys.de/en/support/firmware/).

#### Hue Firmware

Signify don't provide their firmware for use with other ZigBee gateways than the Hue bridge.
The communication between the bridge and the server hosting the firmware files is encrypted, so we cannot get an overview of the files available.
To find the firmware files, you need to sniff the traffic from the Hue bridge to the Internet, as it downloads the files.
Unfortunately, the bridge will only download firmware files for connected devices with outdated firmware.
For details, see [issue #270](https://github.com/dresden-elektronik/deconz-rest-plugin/issues/270) in the REST API plugin repository.

Below is an overview of the images found so far:

Image | ModelId | Device(s) | Firmware | Works
-- | -- | -- | -- | --
0103 | | Gamut-A _Color light_ | 5.127.1.26581
0104 | LST002 | Gamut-B _Extended color light_ | [5.130.1.30000](http://fds.dc1.philips.com/firmware/ZGB_100B_0104/1107326256/ConnectedLamp-Atmel_0104_5.130.1.30000_0012.sbl-ota) | Y
0105 | LCT007 | _Dimmable light_ | [5.130.1.30000](http://fds.dc1.philips.com/firmware/ZGB_100B_0105/1107326256/WhiteLamp-Atmel-Target_0105_5.130.1.30000_0012.sbl-ota) | Y
0109 | RWL020 | Hue dimmer switch | [6.1.1.28573](http://fds.dc1.philips.com/firmware/ZGB_100B_0109/1107324829/Switch-ATmega_6.1.1.28573_0012.sbl-ota) |
010C | | Gamut-C _Extended color light_<br>_Color temperature light_ | [1.50.2_r30933](http://fds.dc1.philips.com/firmware/ZGB_100B_010C/16783874/100B-010C-01001A02-ConfLight-Lamps_0012.zigbee) | Y
010D | SML001 | Hue motion sensor | [6.1.1.27575](http://fds.dc1.philips.com/firmware/ZGB_100B_010D/1107323831/Sensor-ATmega_6.1.1.27575_0012.sbl-ota) |
010E | LTW013 | Aurelle Panels, Signes, Playbar, Being White Ambiance | [1.50.2_r30933](http://fds.dc1.philips.com/firmware/ZGB_100B_010E/16783620/100B-010E-01001904-ConfLight-ModuLum_0012.zigbee) | Y
0114 | | Bluetooth GU10 | [1.65.9_hB3217DF4](http://fds.dc1.philips.com/firmware/ZGB_100B_0114/16780032/100B-0114-01000B00-ConfLightBLE-Lamps-EFR32MG21.zigbee) |
0116 | | Hue button | 2.30.0_r30777 |

## Installation

Basically, the deCONZ STD OTAU plugin uses the same setup as the [deCONZ REST API plugin](https://github.com/dresden-elektronik/deconz-rest-plugin).
To compile and install the STD OTAU plugin, follow the instructions to compile and install the REST API plugin, substituting the repository in step 1 with this one.

## Troubleshooting
Start deCONZ with `--dbg-ota=1` to make the STD OTAU plugin issue debug messages.
