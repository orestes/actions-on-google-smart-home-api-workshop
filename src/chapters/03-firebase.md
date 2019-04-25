---
description: Setting up our database
---

# Firebase

Firebase is a cloud platform from Google. It's built on top of Google Cloud
Platform, but it aims to make Cloud Computing easier to access for developers.
It provides multiple services, we're going to be using

- Hosting
- Cloud Functions (Functions as a Service)
- Realtime Database (streaming/real-time NoSQL data storage)

## Firebase project

Create a Firebase project

## Seed the initial data

0. In the navigation menu on the left, click on **Develop** › **Database**
1. Choose Realtime Database. (Firestore is not yet supported on Arduino).

Initially, our database will be empty initially.

We are going to create a data structure that holds the state representation for
our smart light bulb. We can create the properties one by one to reflect the
JSON below, or just use the **import JSON** command on the options menu


{% code-tabs %}
{% code-tabs-item title="database.json" %}

```json
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

![](../assets/firebase-data.png)

Database seed

## References

- https://firebase-arduino.readthedocs.io/en/latest
