---
description: Cloud Computing platform
---

# Firebase

Firebase is a cloud platform from Google. It's built on top of Google Cloud Platform and it aims to make Cloud Computing easier to access for developers. It provides multiple services but we are going to be using only these:

* Hosting
* Cloud Functions \(Functions as a Service\)
* Realtime Database \(streaming/real-time NoSQL data storage\)

## Dependencies

You need **Node.js** \(which comes with **npm**\) installed on your computer

Go to [nodejs.org](https://nodejs.org) and follow the instructions for your Operating System

## Firebase project

First, we have to create a Firebase project

* Go to [console.firebase.google.com](https://console.firebase.google.com)
* Log in with your Google account
* Create a Firebase project \(it will use the default plan, _Spark_, which is free\)
* Make a note of your **Project ID**. We will need it later.

Let's set up the Firebase CLI so we can easily implement features on the Firebase Platform.

* Open a **Terminal** or **Command Prompt**
* Run`npm install --global firebase-tools`
* Run the `firebase login` command and follow the prompt to log in using your Google account

Now let's write de code for our Google Assistant integration.

* Create a directory for your code
* Inside the directory, run `firebase init` 
* Using the space key to select and the arrow keys to navigate, select only these features:
  * functions
  * hosting
* Make sure you select your newly created Firebase Project
* Select these settings

| Setting | Value |
| :--- | :--- |
| What language would you like to use to write Cloud Functions?  | JavaScript |
| Do you want to use ESLint to catch probable bugs and enforce style? | No |
| Do you want to install dependencies with npm now? | Yes |
| What do you want to use as your public directory? | public |
| Configure as a single-page app \(rewrite all urls to /index.html\)? | Yes |

The Firebase CLI will then scaffold and configure a project for you. 

Open the directory for your project with your favourite editor or IDE and you should have a directory structure similar to this

* firebase.json 
* public
  * index.html 
* functions
  * index.js
  * package.json
  * node\_modules

Open the `functions/package.json` file and add a configuration for the runtime environment

{% hint style="warning" %}
**Important!** Do not copy and paste the code below. Open the file and add the **engines** configuration for the Firebase Cloud Functions environment
{% endhint %}

```text
{
  "name": ...,
  "description": ...,
  "engines": { "node": "8" }
...
}

```

## Real-time Database

### Load the initial data

1. In the navigation menu on the left, click on **Develop** › **Database**
2. Choose Realtime Database. \(Firestore is not yet supported on Arduino\).

Initially, our database will be empty initially.

We are going to create a data structure that holds the state representation for our smart light bulb. We can create the properties one by one to reflect the JSON below, or just use the **import JSON** command on the options menu

Create a file named `database.json` and paste the following content. Save your file and use the **import JSON** option on the Firebase Console.

{% code title="database.json" %}
```javascript
{
  "devices" : {
    "light-1" : {
      "id" : "light-1",
      "state" : {
        "brightness" : 100,
        "color" : {
          "hex" : "#ff0000",
          "name" : "rouge",
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
{% endcode %}

Once you seed the data, your database should look like this.

![The Firebase Console](../.gitbook/assets/image%20%2823%29.png)

Using the Firebase Console on your browser, you can update your data manually whenever you want.

### Set up the database rules

We need to access this data without authenticating for now, let's open our dataset for read and write access

{% hint style="warning" %}
In real-life projects, leaving our database open for read and write access would be a terrible practice. Don't do this!
{% endhint %}

Go to the rules tab and update the contents with

{% code title="firebase-rules.json" %}
```javascript
{
  /* Visit https://firebase.google.com/docs/database/security to learn more about security rules. */
  "rules": {
    ".read": true,
    ".write": true
  }
}
```
{% endcode %}

Make sure you **publish** the rules

![](../.gitbook/assets/image%20%2815%29.png)

#### Database secret

In order to connect to our Firebase Realtime Database from our Arduino code, we'll need some basic credentials.

Go to your project settings on the Firebase Console

![](../.gitbook/assets/image%20%2822%29.png)

From here, go to **Service Accounts** › **Database secrets**. We need to generate a new database secret and save it for our next step.

![](../.gitbook/assets/image%20%2813%29.png)

## Connecting our prototype

Copy the following code, and make sure to **update the following settings** to match your Wi-Fi settings and Firebase Project.

* FIREBASE\_HOST
* FIREBASE\_AUTH
* WIFI\_SSID
* WIFI\_PASSWORD

{% code title="firebase-rg-led.ino" %}
```c
// Copyright 2019 Orestes Carracedo https://orestes.io
//
// This work is licensed under the Creative Commons Attribution 4.0 International License.
// To view a copy of this license, visit http://creativecommons.org/licenses/by/4.0/ or
// send a letter to Creative Commons, PO Box 1866, Mountain View, CA 94042, USA.

#include <ESP8266WiFi.h> // ESP8266 dependency
#include <FirebaseESP8266.h> // Firebase dependency
#include <Adafruit_NeoPixel.h> // NeoPixel dependency

// Firebase Project
#define FIREBASE_HOST "YOUR_PROJECT_ID_HERE.firebaseio.com"
#define FIREBASE_AUTH "YOUR_DB_SECRET_HERE"
#define WIFI_SSID "WIFI_SSID_HERE"
#define WIFI_PASSWORD "WIFI_PASSWORD_HERE"

#define DEVICE_ID "light-1"

// NeoPixel connection
const int LED_PIN = D4;
const int LED_COUNT = 1;

// State variables
bool on = true;
int i = 127;
int r = 255;
int g = 0;
int b = 0;

// Firebase Data API
FirebaseData firebaseData;
FirebaseJson json;

// NeoPixel API
Adafruit_NeoPixel strip = Adafruit_NeoPixel(LED_COUNT, LED_PIN, NEO_GRB + NEO_KHZ800);

void setup()
{
  Serial.begin(115200);
  Serial.println("");
  
  strip.begin();

  WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
  Serial.print("Connecting to Wi-Fi");
  while (WiFi.status() != WL_CONNECTED) {
    Serial.print(".");
    delay(1000);
  }
  Serial.println();
  Serial.print("Connected: ");
  Serial.println(WiFi.localIP());
  
  Serial.print("Connecting to Firebase: ");
  Serial.println(FIREBASE_HOST);

  Firebase.begin(FIREBASE_HOST, FIREBASE_AUTH);
  Firebase.reconnectWiFi(true);

  if (!Firebase.beginStream(firebaseData, "/devices/" + String(DEVICE_ID) + "/state"))
  {
      Serial.println("Could not begin stream");
      Serial.println("REASON: " + firebaseData.errorReason());
      Serial.println();
  }
}

void loop() {
  if (!Firebase.readStream(firebaseData)) {
    Serial.println("Can't read stream data");
    Serial.println("REASON: " + firebaseData.errorReason());
    delay(1000);
  }

  if (firebaseData.streamTimeout()) {
    Serial.println();
    Serial.println("Stream timeout, will resume streaming...");
  }

  updateDataFromFirebase();
  
  if (on) {
    strip.setBrightness(i);
    strip.setPixelColor(0, strip.Color(r, g, b));
  } else {
    strip.setBrightness(0);
    strip.setPixelColor(0, strip.Color(0, 0, 0));  
    strip.clear();
  }
  
  strip.show();
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

void updateDataFromFirebase() {
  if (!firebaseData.streamAvailable()) {
    return;
  }
    
  const String path = firebaseData.dataPath();

  if (path == "/") {
    FirebaseJson &json = firebaseData.jsonObject();
    FirebaseJsonData data;
    
    json.get(data, "on");
    if (data.success && data.type == "bool")
      setOn(data.boolValue); 

    json.get(data, "brightness");
    if (data.success && data.type == "int")
      setBrightness(data.intValue);

    int tmpR;
    int tmpG;
    int tmpB;
    
    json.get(data, "color/rgb/r");
    if (data.success && data.type == "int")
      tmpR = data.intValue;
    
    json.get(data, "color/rgb/g");
    if (data.success && data.type == "int")
      tmpG = data.intValue;
    
    json.get(data, "color/rgb/b");
    if (data.success && data.type == "int")
      tmpB = data.intValue;
    
    setColor(tmpR, tmpG, tmpB);
    return;
  }

  if (path == "/on") {
    setOn(firebaseData.boolData());
    return;
  }

  if (path == "/brightness") {
    setBrightness(firebaseData.intData());
    return;
  }

  if (path == "/color") {    
    FirebaseJson &json = firebaseData.jsonObject();
    FirebaseJsonData data;
    
    int tmpR;
    int tmpG;
    int tmpB;
    
    json.get(data, "rgb/r");
    if (data.success && data.type == "int")
      tmpR = data.intValue;
    
    json.get(data, "rgb/g");
    if (data.success && data.type == "int")
      tmpG = data.intValue;
    
    json.get(data, "rgb/b");
    if (data.success && data.type == "int")
      tmpB = data.intValue;
    
    setColor(tmpR, tmpG, tmpB);
    return;
  }
}
```
{% endcode %}

#### Test run

**Upload** your code and open **Tools &gt; Serial Monitor**

You should see your board connect to the internet and receive data from Firebase

Now change some color values in the Firebase Console and watch as your RGB led changes and your Serial Monitor shows you log traces. It's working!

![](../.gitbook/assets/image%20%287%29.png)

## Firebase Cloud Functions

We are going to use Firebase Cloud functions to create two endpoints for the Actions on Google Smart API to connect.

We will need

* A **token** endpoint where Google will perform OAuth 2 authentication
* A **request** endpoint to handle requests from the Smart Home API

Let's add some dependencies to build our functions

Go into the `functions` directory and run

```text
npm install jsonwebtoken
```

On your IDE or code editor, update the `functions/index.js` file with the following code that contains endpoint definitions

{% code title="functions/index.js" %}
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
{% endcode %}

And now let's deploy our functions. Go back to your terminal/console, and from the `functions` directory, run the following command:

```text
firebase deploy --only functions
```

Take a note of the endpoint for the two Firebase Cloud functions

## Firebase Hosting

We are going to create a simple login page that will take the parameters from Google and redirect an authenticated user.

Update the public/index.html file

{% code title="public/index.html" %}
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
{% endcode %}

Deploy this page with 

```text
firebase deploy --only hosting
```

Take a note of the generated public URL for your Firebase-hosted page

## References

* [https://github.com/FirebaseExtended/firebase-arduino](https://github.com/FirebaseExtended/firebase-arduino)
* [https://github.com/mobizt/Firebase-ESP8266](https://github.com/mobizt/Firebase-ESP8266)

