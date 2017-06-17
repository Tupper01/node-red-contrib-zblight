# node-red-contrib-zblight

This project is an addon node for node-red, which allows you to control power and brightness
of various zigbee based smart-bulbs using a Digi XBEE series 2 module.

## Requirements

This requires the following npm packages to work properly:
* node-red-node-serialport
* xbee-api

Install these via npm, inside your node-red directory:
```
npm install node-red-node-serialport xbee-api node-red-contrib-zblight
```

## XBEE Setup

You will need to have an XBEE Series 2 module to communicate with the zigbee network, series 1/xbee 802.15.4 modules will not work.
The XBEE Radio will need to have the Zigbee Coordinator firmware flashed in to it.

In addition, you will need to make sure the following settings are correct:

* ATZS = 2
* ATEE = 1
* ATEO = 1
* ATAO = 1
* ATAP = 1

This ensures that the xbee can communicate with Zigbee ZDO/ZCL devices, which is required by the Zigbee Home Automation Profile.
The ATAP=1 also ensures that the xbee is placed into API mode.

For more information on configuring XBee series 2 radios as ZigBee coordinators see [Digi's knowledge base article](http://knowledge.digi.com/articles/Knowledge_Base_Article/Zigbee-Home-Automation).

## Usage
![flow](flow.png)
![mqtt-flow](mqtt-flow.png)
The node generates an API packet to be sent to the xbee module. You will need to configure the zblight node with the MAC address of the bulb you want to control, and the type of bulb you want to control.

Control of the bulbs is done fairly simply. Send a message to the node, with one of the following in the payload:
* on
    * _Turns the light on_
* off
    * _Turns the light off_
* toggle
    * _Toggles the light on or off_
* 0 - 255
    * _Dims the light_
* 2700 - 6500
    * _Sets the color temperature (if supported)_
* [0-255],[0,255]
    * _Sets the hue and saturation (if supported)_

You can also override the specified bulb type and topic (if any) by sending msg.bulbtype and/or msg.topic to the control. This can help eliminate the need for multiple ZigBee bulb controls.

If you dim a bulb, then turn it off, the next time you turn it on, it will be at the same brightness level.

![config](config.png)

You can also do more advanced grouping, by simply connecting a single output node to multiple zblight nodes.

![grouping](grouping.png)
![ui](ui.png)

In order to figure out the MAC address of the bulbs, as well as making sure the bulb joined your XBEE zigbee coordinator, you can use the Digi XCTU Software discovery mode.
![xctu](xctu.png)

This will show all of the zigbee devices connected to your network, and the routing table between them.
On my test network (above), I have A GE Link bulb (7E), A Cree Connected (E2), and a Philips Hue White (00)

If your bulbs do not connect to your network, or if they have been joined to a different network previously, they will need to be factory reset. For the Link and the Cree bulbs, this is done with a specific on/off pattern, for the hue bulbs, they have to be reset with either a touchlink remote, or the hue hub.

## Compatibility
Currently, the node has been tested with the following bulbs:
* GE Link
* Cree Connected
* Philips Hue (Tested with the White Ambiance, Tunable Ambiance, and the RGBW variations)
* Osram/Sylvania Lightify (White, Tunable White, and RGBW)

Those are the bulbs I currently have, and have been able to test. I will be looking at aquiring more bulbs to add more compatibility. I also have a Commercial Electric Smart LED Downlight I will be adding support for soon.
These bulbs all support turning power on and off, as well as dimming with this node. The Osram/Sylvania Lightify (Tunable White and RGBW) bulbs have been tested to work with color temperature, as well as hue/saturation changes.