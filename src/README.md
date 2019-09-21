---
description: How to follow this workshop
---

# Welcome

### What we are going to do

We are going to build an Arduino project that mimics a **Smart RGB Light Bulb**. The project will connect to the internet on its own and get its configuration from the **Firebase Realtime Database**. 

We will then connect our project to the **Actions on Google** platform, which will enable us to control our light by changing its color or intensity. To issue commands, we'll be able to use our voice with the **Google Assistant** or simply the **Google Home.**

### Objective

The main goal is to **have fun** writing **simple** **code** and **learn** new things **together**.

By the end you will have learned:

* How to wire up a simple Internet of Things project using an **esp8266** board and a

  **Adafruit's NeoPixels**-compatible RGB LED.

* How to install and configure your **Arduino IDE** to work with esp8266 boards
* How to connect to and get real-time updates from the **Firebase Realtime**

  **Database** from your Arduino project

* How to write a **JavaScript** API integration with the actions-on-google SDK
* How to set up your **Actions on Google** project to enable the **Smart Home API** integration.

### Expected result

You will be able to ask the Google Assistant to, for example:

* Turn your light on or off
* Set your light to any color \(even teal?, what kind of blue is that?!\)
* Set your light's intensity to a percentage
* Turn the light's intensity up or down

Using natural language, in any language supported by the Google Assistant, and run any other commands related to lights.

In other words, your project will behave exactly like any other off-the-shelf smart light bulb.

![This is what your smart light bulb will look like](.gitbook/assets/result.png)

### Reference code

The final result of this workshop is available on GitHub at [orestes/actions-on-google-smart-light](https://github.com/orestes/actions-on-google-smart-light)

This repo contains

* The Arduino code for the smart light project
* The Firebase Cloud Functions code to integrate with the Actions on Google Smart Home API 
* The front-end login screen to fake authenticating users when they link their accounts on the Google Home app

{% hint style="warning" %}
You need some hardware to follow along. Don't skip ahead, check out the [components list](requisites.md) first.
{% endhint %}

