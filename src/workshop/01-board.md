---
description: Setting up the components for our IoT project
---

# Electronics

We are going to use a very cheap and useful esp8266-based board. I would recommend buying LOLIN/WeMos boards as they're pretty cheap, reliable and easy to buy.

These boards are compatible with Arduino, which makes them really use to work with, and perfect for our workshop.

{% tabs %}
{% tab title="D1 mini pro" %}


![LOLIN D1 mini pro](../.gitbook/assets/image%20%2820%29.png)

Official site: [https://www.wemos.cc/en/latest/d1/d1\_mini\_pro.html](https://www.wemos.cc/en/latest/d1/d1_mini_pro.html)
{% endtab %}

{% tab title="D1 mini" %}
![WeMos D1 mini](../.gitbook/assets/image%20%2818%29.png)

{% hint style="warning" %}
Retired/replaced by Lolin D1 mini
{% endhint %}

Official site: [https://www.wemos.cc/en/latest/d1/d1\_mini.html](https://www.wemos.cc/en/latest/d1/d1_mini.html)
{% endtab %}

{% tab title="Compatible D1 mini" %}
![D1 mini-compatible board](../.gitbook/assets/image%20%2819%29.png)

Dataset: [https://drive.google.com/open?id=1hyGhkGgUftwZsVEdMyOddPu8pWtqciLi](https://drive.google.com/open?id=1hyGhkGgUftwZsVEdMyOddPu8pWtqciLi)  
Available on Amazon EU: [https://www.amazon.es/gp/product/B0754N794H](https://www.amazon.es/gp/product/B0754N794H)
{% endtab %}
{% endtabs %}

## Drivers

We need to install USB drivers for the CH34X chipset so we can connect to our board and _flash_ \(install\) our programs

* [Driver for Windows](https://wiki.wemos.cc/_media/ch341ser_win_3.4.zip)
* [Driver for Mac OS X](https://wiki.wemos.cc/_media/ch341ser_mac_1.5.zip)

{% hint style="warning" %}
Reboot your computer after installing the drivers
{% endhint %}

### Setup

We're going to connect your RGB LED using to female-to-male jumper cables.

{% hint style="warning" %}
Make  sure your board is not connected to USB before doing this
{% endhint %}

First, let's connect the jumper cables to our RGB LED.

[AdaFruit's NeoPixel reference](https://www.adafruit.com/product/1938)

There's two main controllers for these RGB leds, APA106 and WS2812, and both use the same configuration

{% hint style="info" %}
If you're using an AdaFruit NeoPixel and you have difficulty locating the flat side, it's the one with the two longer pins
{% endhint %}

![](../.gitbook/assets/image%20%289%29.png)

When you're done, it should look like this

![](../.gitbook/assets/image%20%2814%29.png)

Now, let's connect the RGB LED to the board using the male end of the jumper cables, making sure you respect the pins

* RGB LED **ground** to board's **GND**
* RGB LED **+5v** to board's **5v pin**
* RGB LED **Data in** to boards' data pin \(**D4** in our example\)

When you're done, it'll look something like this,

![](../.gitbook/assets/image%20%2816%29.png)

## References

* [https://wemos.cc](https://wemos.cc) official site for WeMos/LOLIN boards

