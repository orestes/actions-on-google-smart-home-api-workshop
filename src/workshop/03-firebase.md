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
#include <FirebaseArduino.h>
#include <ESP8266WiFi.h>

// Set these to run example.
#define WIFI_SSID "WIFI_GOES_HERE"
#define WIFI_PASSWORD "PASSWORD_GOES_HERE"

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
  Firebase.stream("/devices/light-1/state");  
}

void loop() {
  if (Firebase.failed()) {
    Serial.println("streaming error");
    Serial.println(Firebase.error());

    Serial.println(Firebase.error());
  }

  if (Firebase.available()) {
     FirebaseObject event = Firebase.readEvent();
     String eventType = event.getString("type");
     eventType.toLowerCase();

     Serial.print("event: ");
     Serial.println(eventType);

     Serial.println("path: " + event.getString("path"));

     Serial.print("data: ");
     JsonVariant variant = event.getJsonVariant("data");
     variant.prettyPrintTo(Serial);

     // if (eventType == "put") {}
     if (eventType == "") {
      Serial.println("discarded");
     }


  }  
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## References

* [https://firebase-arduino.readthedocs.io/en/latest](https://firebase-arduino.readthedocs.io/en/latest)

