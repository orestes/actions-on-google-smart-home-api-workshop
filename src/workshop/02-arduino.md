---
description: Electronics platform
---

# Arduino

## Arduino

We'll be using the Arduino platform and IDE to write our code

### Install Arduino IDE

Download and install the Arduino IDE.

{% tabs %}
{% tab title="Windows" %}
Download **Arduino IDE** for 

* [Windows](https://www.arduino.cc/download_handler.php?f=/arduino-1.8.9-windows.exe)
{% endtab %}

{% tab title="Linux" %}
Download **Arduino IDE** for

* [Linux 32 bits](https://www.arduino.cc/download_handler.php?f=/arduino-1.8.9-linux32.tar.xz)
* [Linux 64 bits](https://www.arduino.cc/download_handler.php?f=/arduino-1.8.9-linux64.tar.xz)
* [Linux ARM 32 bits](https://www.arduino.cc/download_handler.php?f=/arduino-1.8.9-linuxarm.tar.xz)
* [Linux 64 bits](https://www.arduino.cc/download_handler.php?f=/arduino-1.8.9-linuxaarch64.tar.xz)
{% endtab %}

{% tab title="Mac OS X" %}
Download **Arduino IDE** for 

* [Mac OS X](https://www.arduino.cc/download_handler.php?f=/arduino-1.8.9-macosx.zip)
{% endtab %}
{% endtabs %}

### Install board definitions

Copy this URL

```text
http://arduino.esp8266.com/stable/package_esp8266com_index.json
```

1. Open the Arduino IDE preferences
2. In the `Additional Boards Manager URLs`

![](../.gitbook/assets/arduino-boards.png)

#### Install the esp8266 boards definitions

Open the Board Manager in **Tools › Board › Boards Manager**

![](../.gitbook/assets/image%20%283%29.png)

1. Search for **esp8266**
2. Install the boards definition

### Install libraries

#### AdaFruit Neopixel

We are going to use the Library Manager to install this library

![Arduino IDE Library Manager](../.gitbook/assets/image%20%288%29.png)

1. Open the Library Manager by clicking on the menu **Sketch** › **Include Library** › 

   **Manage Libraries**

2. Search for neopixel
3. Select **Adafruit NeoPixel** by **Adafruit**
4. Click the **Install button**
5. Don't close the Library Manager dialog yet

#### ArduinoJson

We will use the Library Manager again to install this one

![](../.gitbook/assets/image%20%286%29.png)

1. Open the Library Manager by clicking on the menu **Sketch › Include Library › Manage Libraries**
2. Search for ArduinoJson
3. Select **ArduinoJson** by **Benoit Blanchon**
4. **Make sure you select the v5 version**
5. Click the **Install button**
6. Don't close the Library Manager dialog yet

#### FirebaseExtended

Installing this libary is a bit different. We are going to download a zip with a stable release and add it to the Arduino IDE.

1. Download the v0.3 release for **firebase-arduino** [here](https://github.com/FirebaseExtended/firebase-arduino/archive/v0.3.zip)
2. Click on **Sketch › Include Library › Add .ZIP library**
3. Select the zip you just downloaded for firebase-arduino

### Test program

Let's write a program that tests our RGB LED and its connections.  
Open the Arduino  IDE



![](../.gitbook/assets/image%20%2811%29.png)

Arduino programs have to functions:

**Setup** runs once when the device starts

**Loop** runs again and again while the device is connected

{% tabs %}
{% tab title="neopixel-test.ino" %}
```c
#include <Adafruit_NeoPixel.h>

Adafruit_NeoPixel strip = Adafruit_NeoPixel(1, 2, NEO_GRB + NEO_KHZ800);

void setup() {
  strip.begin();
  strip.setBrightness(255);
  Serial.begin(9600);
}

void loop() {
    strip.setPixelColor(0, strip.Color(255, 0, 0));
    Serial.println("red");
    strip.show();
    delay(500);
    
    strip.setPixelColor(0, strip.Color(0, 255, 0));
    Serial.println("green");
    strip.show();
    delay(500);
    
    strip.setPixelColor(0, strip.Color(00, 0, 255));
    Serial.println("blue");
    strip.show();
    delay(500);
}
```
{% endtab %}
{% endtabs %}

### Flash your program

Connect your board using the USB cable.

* Make sure you have selected the **LOLIN d1 mini pro** board under **Tools › Board** 
* Make sure you are using the appropriate COM port under **Tools › Port** 
* Click the **upload** button 
* Enjoy your blinking RGB led!

Let's take a look under the hood

### The serial monitor

Open the Serial Monitor on **Tools › Serial Monitor**

{% hint style="warning" %}
Make sure the baud rate matches the one in the code
{% endhint %}

Watch as your code runs

![](../.gitbook/assets/image%20%284%29.png)

## References

* [https://github.com/esp8266/Arduino](https://github.com/esp8266/Arduino)
* [https://github.com/FirebaseExtended/firebase-arduino](https://github.com/FirebaseExtended/firebase-arduino)

