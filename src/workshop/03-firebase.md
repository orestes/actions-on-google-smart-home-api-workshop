---
description: Setting up our database
---

# Firebase

Firebase is a cloud platform from Google. It's built on top of Google Cloud Platform, but it aims to make Cloud Computing easier to access for developers. It provides multiple services, we're going to be using

* Hosting
* Cloud Functions \(Functions as a Service\)
* Realtime Database \(streaming/real-time NoSQL data storage\)

## Firebase project

Create a Firebase project

* Create a Firebase project
* Install cli
* firebase login
* firbase init \(functions + hosting\)

"engines": { "node": "8" } in functions package.json

## Seed the initial data

1. In the navigation menu on the left, click on **Develop** › **Database**
2. Choose Realtime Database. \(Firestore is not yet supported on Arduino\).

Initially, our database will be empty initially.

We are going to create a data structure that holds the state representation for our smart light bulb. We can create the properties one by one to reflect the JSON below, or just use the **import JSON** command on the options menu

{% code-tabs %}
{% code-tabs-item title="database.json" %}
```javascript
{
  "devices": {
    "light-1": {
      "id": "light-1",
      "state": {
        "brightness": 16,
        "hex": "#009055",
        "on": true,
        "rgb": {
          "b": 80,
          "g": 144,
          "r": 0
        }
      },
      "type": "rgb-led-dimmable"
    }
  }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Once you seed the data, your database should look like this.

![](../.gitbook/assets/firebase-data%20%281%29.png)

Database seed

## Connecting our prototype

{% code-tabs %}
{% code-tabs-item title="firebase-rg-led.ino" %}
```c
#include <FirebaseError.h>
#include <FirebaseObject.h>

#include <Adafruit_NeoPixel.h>

#include <FirebaseArduino.h>
#include <ESP8266WiFi.h>

// Wi-fi access
#define WIFI_SSID "orestes_LTE"
#define WIFI_PASSWORD "hell0wifi"

// NeoPixel connection
const int LED_STRIP_PIN = 2;
const int LED_STRIP_COUNT = 1;

// State variables
bool on = true;
int i = 127;
int r = 255;
int g = 0;
int b = 0;

Adafruit_NeoPixel strip = Adafruit_NeoPixel(LED_STRIP_COUNT, LED_STRIP_PIN, NEO_GRB + NEO_KHZ800);

void setup() {
  Serial.begin(9600);

  WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
  Serial.print("connecting");
  while (WiFi.status() != WL_CONNECTED) {
    Serial.print(".");
    delay(1000);
  }
  Serial.println();
  Serial.print("connected: ");
  Serial.println(WiFi.localIP());

  Firebase.begin("toy-home.firebaseio.com");
  Firebase.stream("/devices/light-1/state"); // TODO: Use your device ID if you change it
}

void loop() {
  if (Firebase.failed()) {
    Serial.println("streaming error");
    Serial.println(Firebase.error());

    Serial.println(Firebase.error());
  }

  updateDataFromFirebase();
  
  if (on) {
    strip.setBrightness(i);
    strip.setPixelColor(0, strip.Color(r, g, b));
  } else {
    strip.setBrightness(0);
    strip.setPixelColor(0, strip.Color(0, 0, 0));  
  }
  
  strip.show();
}

void updateDataFromFirebase() {
  if (!Firebase.available()) {
    return;
  }
  
  FirebaseObject event = Firebase.readEvent();
  String eventType = event.getString("type");
  String path = event.getString("path");

  if (eventType == "") {
   Serial.println("discarded");
   return;
  }

  // Let's output some debugging data
  Serial.println("event: " + eventType);
  Serial.println("path: " + path);
  
  Serial.print("data: ");
  JsonVariant json = event.getJsonVariant("data");
  json.prettyPrintTo(Serial);
  Serial.println("");

  // We only act on  put events
  if (eventType != "put") {
    return;
  }

  if (path == "/") {
    setOn(event.getBool("data/on"));
    setBrightness(event.getInt("data/brightness"));
    setColor(event.getInt("data/color/rgb/r"), event.getInt("data/color/rgb/g"), event.getInt("data/color/rgb/b"));
  }

  if (path == "/on") {
    setOn(event.getBool("data"));
  }

  if (path == "/brightness") {
    setBrightness(event.getInt("data"));
  }

  if (path == "/color") {    
    setColor(event.getInt("data/rgb/r"), event.getInt("data/rgb/g"), event.getInt("data/rgb/b"));
  }
}

void setOn(bool value) {
  on = value;
  if (on) {
    Serial.println("Turn light ON");
  } else {
    Serial.println("Turn light OFF");
  }
}
void setBrightness(int value) {
  i = map(value, 0, 100, 0, 255);
  Serial.print("Set brightness to: ");
  Serial.println(i);
}
void setColor(int red, int green, int blue) {
  r = constrain(red, 0, 255);
  g = constrain(green, 0, 255);
  b = constrain(blue, 0, 255);

  Serial.print("Set R to: ");
  Serial.println(r);
  Serial.print("Set G to: ");
  Serial.println(g);
  Serial.print("Set B to: ");
  Serial.println(b);
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## References

* [https://firebase-arduino.readthedocs.io/en/latest](https://firebase-arduino.readthedocs.io/en/latest)

