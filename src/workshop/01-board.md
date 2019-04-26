---
description: Setting up the components for our IoT project
---

# Electronics

## LOLIN D1 mini pro

We are going to use a very cheap and useful esp8266-based board

![LOLIN D1 mini pro](../.gitbook/assets/image%20%288%29.png)

## Drivers

We need to install USB drivers for the CH34X chipset so we can connecto to our board and flash our code 

* [Driver for Windows](https://wiki.wemos.cc/_media/ch341ser_win_3.4.zip)
* [Driver for Mac OS X](https://wiki.wemos.cc/_media/ch341ser_mac_1.5.zip)

{% hint style="warning" %}
Reboot your computer after installing the drivers
{% endhint %}

### Setup

Set up your board on top of your solder-less breadboard using the female to male pins. We don't want to solder our board, so make sure all the pins make contact

![](../.gitbook/assets/image.png)



Alternative, you can do it using the male-to-male pins

![](../.gitbook/assets/image%20%286%29.png)

Keep track of where the 5v, GND pins are

Select a pin to use as data-out \(for example, 2\) and keep track of that one too.

Connect your RGB LED using to female-to-male jumper cables.

{% hint style="warning" %}
Make  sure your board is not connected to USB before doing this
{% endhint %}

Connect the RGB LED to the board using the male end of the jumper cables, making sure you respect the pins

* RGB LED **ground** to board's **GND**
* RGB LED **+5v** to board's **5v pin**
* RGB LED **Data in** to boards' data pin \(**2** in our example\)

![](../.gitbook/assets/image%20%284%29.png)



## References

* [D1 mini pro board info](https://wiki.wemos.cc/products:d1:d1_mini_pro)
