---
description: Setting up our database
---

# Firebase

Firebase is a cloud platform from Google. It's built on top of Google Cloud Platform, but it aims to make Cloud Computing easier to access for developers. It provides multiple services, we're going to be using

* Hosting
* Cloud Functions \(Functions as a Service\)
* Realtime Database \(streaming/real-time NoSQL data storage\)

## Dependencies

You need **Node.js** and npm installed on your computer

Go to [nodejs.org](https://nodejs.org) and follow the instructions for your Operating System

## Firebase project

Let's create a Firebase project

* Go to [console.firebase.google.com](https://console.firebase.google.com)
* Log in with your Google account
* Create a Firebase project
* Open a Terminal
* Run`npm install --global firebase-tools`
* Run the `firebase login` command
* Create a directory for your code
* Inside the directory, run `firebase init` 
* Using the space key to select, and the arrow keys to navigate, select functions and hosting
* Use all the default options

Open the `package.json` file and add a configuration for the runtime environment

`"engines": { "node": "8" }`

## Seed the initial data

1. In the navigation menu on the left, click on **Develop** › **Database**
2. Choose Realtime Database. \(Firestore is not yet supported on Arduino\).

Initially, our database will be empty initially.

We are going to create a data structure that holds the state representation for our smart light bulb. We can create the properties one by one to reflect the JSON below, or just use the **import JSON** command on the options menu

{% code-tabs %}
{% code-tabs-item title="database.json" %}
```javascript
{
  "devices" : {
    "light-1" : {
      "id" : "light-1",
      "state" : {
        "brightness" : 100,
        "color" : {
          "hex" : "#ff0000",
          "name" : "rojo",
          "rgb" : {
            "b" : 0,
            "g" : 0,
            "r" : 255
          },
          "spectrum" : 16711680
        },
        "on" : true
      },
      "type" : "rgb-led"
    }
  }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Once you seed the data, your database should look like this.

![](../.gitbook/assets/image%20%2814%29.png)

Using the Firebase Console, you can update your data manually whenever you want.

### Set up the database rules

We need to access this data without authenticating for now, let's open our dataset for read and write access

Go to the rules tab and update the contents with

{% code-tabs %}
{% code-tabs-item title="firebase-rules.json" %}
```javascript
{
  /* Visit https://firebase.google.com/docs/database/security to learn more about security rules. */
  "rules": {
    ".read": true,
    ".write": true
  }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Make sure you **publish** the rules

![](../.gitbook/assets/image%20%2810%29.png)

## Connecting our prototype

Copy the following code, adjusting the following settings as you need them.

* PROJECT\_URL
* WIFI\_SSID
* WIFI\_PASSWORD

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

// Firebase project
#define PROJECT_URL "toy-home.firebaseio.com"


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

  Firebase.begin(PROJECT_URL);
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

#### Test run

**Upload** your code and open **Tools &gt; Serial Monitor**

You should see your board connect to the internet and receive data from Firebase

Update values in the Firebase Console and watch as your RGB led changes and your Serial Monitor shows you log traces. It's working!

![](../.gitbook/assets/image%20%285%29.png)

## Firebase Cloud Functions

We are going to use Firebase Cloud functions to create two endpoints for the Actions on Google Smart API to connect.

We will need

* A **token** endpoint where Google will perform OAuth 2 authentication
* A **request** endpoint to handle requests from the Smart Home API

Let's add some dependencies to build our functions

Go into the functions directory and run

```text
npm install jsonwebtoken
```

Update the functions/index.js file with the endpoint definitions

{% code-tabs %}
{% code-tabs-item title="functions/index.js" %}
```javascript
const functions = require("firebase-functions");
const admin = require("firebase-admin");
const jwt = require("jsonwebtoken");

// TODO: Maybe only do this when you need access to the data
admin.initializeApp();

function getUserIdForAuthCode(code) {
  // TODO: Get corresponding user for the given auth code
  return "fake-user-id";
}

function getToken(payload) {
  // TODO: Use secret from config/env
  return jwt.sign(payload, "secret", {
    expiresIn: "1h" // TODO: Get expiration time from config/env
  });
}

const tokenHandler = (request, response) => {
  const authCode = request.body.code;
  const userId = getUserIdForAuthCode(authCode);

  console.log("Auth code matches user", { authCode, userId });

  // TODO: Add any metadata for the user
  const data = {
    authCode,
    userId
  };

  const access_token = getToken(data);
  const refresh_token = getToken(data);

  console.log("Tokens generated", { access_token, refresh_token });

  response.send({
    access_token,
    refresh_token
  });
};

function dec2hex(dec) {
  return (dec + Math.pow(16, 6)).toString(16).substr(-6);
}

function hex2dec(input) {
  return parseInt(input, 16);
}

function parseAsRGB(input) {
  const hex = dec2hex(input);
  const r = hex2dec(hex.substr(0, 2));
  const g = hex2dec(hex.substr(2, 2));
  const b = hex2dec(hex.substr(4, 2));

  return { r, g, b };
}

// TODO: Get this state from Firebase when the request handler loads
const devicesState = {
  "light-1": {
    on: true,
    online: true,
    brightness: 80,
    color: {
      name: "cerulean",
      spectrumRGB: 31655
    }
  }
};

const devices = [
  {
    id: "light-1",
    type: "action.devices.types.LIGHT",
    traits: [
      "action.devices.traits.OnOff",
      "action.devices.traits.Brightness",
      "action.devices.traits.ColorSetting"
    ],
    name: {
      name: "my smart light"
    },
    willReportState: false,
    attributes: {
      colorModel: "rgb"
    }
  }
];

const commands = [
  {
    ids: ["light-1"],
    status: "SUCCESS",
    states: {
      on: true,
      online: true
    }
  }
];

const intentMap = {
  "action.devices.SYNC": async (input, result) => {
    result.payload.devices = devices;
    return result;
  },
  "action.devices.EXECUTE": async (input, result) => {
    const command = input.payload.commands[0].execution[0];

    if (command.command === "action.devices.commands.ColorAbsolute") {
      await admin
        .database()
        .ref("devices/light-1/state/color")
        .set({
          spectrum: command.params.color.spectrumRGB,
          rgb: parseAsRGB(command.params.color.spectrumRGB),
          hex: "#" + dec2hex(command.params.color.spectrumRGB),
          name: command.params.color.name
        });

      result.payload.devices = devicesState; // TODO: Report online status and color
    }

    if (command.command === "action.devices.commands.OnOff") {
      // TODO: Communicate with the device and get a result
      await admin
        .database()
        .ref("devices/light-1/state/on")
        .set(command.params.on);

      result.payload.devices = devicesState; // TODO: Report online status and color
    }

    if (command.command === "action.devices.commands.BrightnessAbsolute") {
      // TODO: Communicate with the device and get a result
      await admin
        .database()
        .ref("devices/light-1/state/brightness")
        .set(command.params.brightness);

      result.payload.devices = devicesState; // TODO: Report online status and color
    }

    result.payload.commands = commands;

    return result;
  },
  "action.devices.QUERY": (input, result) => {
    // TODO: Read from Firebase
    result.payload.devices = devicesState;
    return result;
  }
};

const requestHandler = async (request, response) => {
  console.log("incoming request", JSON.stringify(request.body));

  const body = request.body;
  const requestId = body.requestId;
  const intent = body.inputs[0].intent;
  const agentUserId = "fake-user-id"; // TODO: Get from JWT in Authorization HTTP header

  const tmp = {
    requestId,
    payload: {
      agentUserId
    }
  };

  const result = await intentMap[intent](body.inputs[0], tmp);

  console.log("outgoing response", JSON.stringify(result));
  response.send(result);
};

exports.token = functions.https.onRequest(tokenHandler);
exports.request = functions.https.onRequest(requestHandler);

```
{% endcode-tabs-item %}
{% endcode-tabs %}

And now let's deploy our functions

```text
firebase deploy --only functions
```

Take a note of the endpoint for the two Firebase Cloud functions

## Firebase Hosting

We are going to create a simple login page that will take the parameters from Google and redirect an authenticated user.

Update the public/index.html file

{% code-tabs %}
{% code-tabs-item title="public/index.html" %}
```markup
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>Smart Home Provider :: Login</title>
    <style>
      textarea {
        width: 100%;
        min-height: 10em;
        overflow-x: scroll;
        margin: 2em;
      }
    </style>
  </head>
  <body>
    <button id="login">Login</button>
    <textarea id="debug"></textarea>
    <script>
      // Get references to DOM elements
      const loginButton = document.querySelector("#login");
      const debug = document.querySelector("#debug");

      // Read query params
      const url = new URL(window.location);

      const redirect_uri = url.searchParams.get("redirect_uri");
      const client_id = url.searchParams.get("client_id");
      const response_type = url.searchParams.get("response_type");
      const scope = url.searchParams.get("scope");
      const state = url.searchParams.get("state");

      // Show variables for debugging
      debug.innerText = JSON.stringify(
        {
          redirect_uri,
          client_id,
          response_type,
          scope,
          state
        },
        null,
        2
      );

      // Declare function
      function getAuthCodeAndRedirect() {
        // TODO: Authenticate the user and get a single-use auth code from our API
        const auth_code = "fake-auth-code";

        // Redirect the user back to Google, providing the new auth code for the original state
        window.location = `${redirect_uri}?&state=${state}&code=${auth_code}`;
      }

      // Add event listeners
      loginButton.addEventListener("click", getAuthCodeAndRedirect);
    </script>
  </body>
</html>

```
{% endcode-tabs-item %}
{% endcode-tabs %}

Deploy this page with 

```text
firebase deploy --only hosting
```

Take a note of the generated public URL for your Firebase-hosted page

## References

* [https://firebase-arduino.readthedocs.io/en/latest](https://firebase-arduino.readthedocs.io/en/latest)

