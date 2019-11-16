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
3. Close Arduino and open it again so the board configurations are loaded

#### Configure Arduino for this project

Connect your board using the USB cable.

* Make sure you are using the appropriate COM port under **Tools › Port**
* Make sure you have selected the appropriate board board under **Tools › Board:**

{% tabs %}
{% tab title="LOLIN D1 mini pro" %}
Use **LOLIN \(WEMOS\) D1 mini Pro**

![LOLIN \(WEMOS\) D1 mini Pro](../.gitbook/assets/image%20%2811%29.png)
{% endtab %}

{% tab title="WeMos D1 mini" %}
Use **WeMos D1 R1**

![WeMos D1 R1](../.gitbook/assets/image%20%2824%29.png)
{% endtab %}

{% tab title="Compatible D1 mini" %}
Use **WeMos D1 R1**

![WeMos D1 R1](../.gitbook/assets/image%20%2824%29.png)
{% endtab %}
{% endtabs %}

### Install libraries

#### AdaFruit Neopixel

We are going to use the Library Manager to install this library

![Arduino IDE Library Manager](../.gitbook/assets/image%20%2810%29.png)

1. Open the Library Manager by clicking on the menu **Sketch** › **Include Library** › 

   **Manage Libraries**

2. Search for neopixel
3. Select **Adafruit NeoPixel** by **Adafruit**
4. Click the **Install button**
5. Don't close the Library Manager dialog yet

#### ArduinoJson

We will use the Library Manager again to install this one

![](../.gitbook/assets/image%20%288%29.png)

1. Open the Library Manager by clicking on the menu **Sketch › Include Library › Manage Libraries**
2. Search for ArduinoJson
3. Select **ArduinoJson** by **Benoit Blanchon**
4. **Make sure you select the v5 version**
5. Click the **Install button**
6. Don't close the Library Manager dialog yet

#### Firebase ESP8266 Client

We will also install this library using the Library Manager

![](../.gitbook/assets/image%20%284%29.png)

1. Open the Library Manager by clicking on the menu **Sketch › Include Library › Manage Libraries**
2. Search for firebase
3. Select **FirebaseESP8266 Client** by **Mobitz**
4. Click the **Install button**
5. Close the Library Manager

#### Restart Arduino IDE

{% hint style="warning" %}


After updating or installing libraries, restarting Arduino IDE is required for changes to take effect
{% endhint %}

### Test program

Let's write a program that tests our RGB LED and its connections.  
First, open the Arduino  IDE



![](../.gitbook/assets/image%20%2817%29.png)

Notice that Arduino programs have at least two functions:

* **Setup** runs once when the device starts
* **Loop** runs again and again, indefinitely, while the device is powered on.

Let's copy the code below and make some magic happen

{% code title="neopixel-test.ino" %}
```c
#include <Adafruit_NeoPixel.h>

// This code asumes your data pin is 2. Change it if you need to
Adafruit_NeoPixel strip = Adafruit_NeoPixel(1, D4, NEO_GRB + NEO_KHZ800);

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
{% endcode %}

#### Flash your program

* Click the **upload** button on the top bar of the Arduino IDE
* Enjoy your blinking RGB led!

![The upload button](../.gitbook/assets/image%20%285%29.png)

### Debugging

#### The serial monitor

Let's take a look under the hood. Open the Serial Monitor on **Tools › Serial Monitor**

{% hint style="warning" %}
Make sure the baud rate on this window matches the one in the code
{% endhint %}

Watch as your code runs

![](../.gitbook/assets/image%20%286%29.png)

## References

* [https://github.com/esp8266/Arduino](https://github.com/esp8266/Arduino)
* [https://github.com/FirebaseExtended/firebase-arduino](https://github.com/FirebaseExtended/firebase-arduino)

