---
description: Voice User Interface platform for the Google Assistant
---

# Actions on Google

### Setup your Actions on Google Project

Navigate to [https://actions.google.com](https://actions.google.com)

Log in with your Google Account

Create a new Actions on Google Project. Pick any project name you want. You can select any support language.

![](https://lh3.googleusercontent.com/-T_DbO5BcJuQT9JAoteWbL4dnPPRzpyGfAiaYDX1DRfCIBCIyyqEDPs3U0pvco03uCl4RoN0-JFk9vs9QR_DJfLOv2xmbcnCWgXuJII-j9gMKev_c-BMLoNKGQBQ92eqkQme2UWhwMk)

If you get the following screen, click on **SKIP** on the top right

![](https://lh6.googleusercontent.com/ayBLJEd9MLmoccmwj6blKMs7JpAYZnqwuKOVDONVFIEOrxl7-ZrXfw3gCNpL2tkKAPlnr0lLaRlR8XIkjaqSCIpnadqvIWaaY5Mhcs-c2wCYDSSg8K14LlIEXI4KIPLbDe2FQfMn5Lc)

Make sure you pick the **Smart home** template

![](https://lh6.googleusercontent.com/0ckJekk-tYUKCHtiDfgvR_6KNPn4o82pbT3PMkGuaj2VK3G4UQmiI-iHsu-H_KTXvJpoLjGXrYmozqHEHOTXxx_KvjX6VhaWagP6KKtQJWRxR0LUBqy7xtxzrvrUQG5EpZrjvh_6TDc)

Pick a display name. This will be displayed in your list of Smart Home providers in the Google Home app.

![](https://lh5.googleusercontent.com/m6zNPhhiFjSG3OGV53n3uBegd_d8GNQsaxz-nCWIPw4EKV83zsqTB0x-YTNNI-Inir9LPkRtxpqLIX6TLpPCtW8Wq3_8K4mxf1EgQw5JwL-SHvsIavol46hzzpZUfC9bQiI1C5GjXMo)

Go to the list of actions and click on the only action that shows on the list

![](https://lh5.googleusercontent.com/nWzfZfqX6HX3LSbefPr0KkUk50P8tv-nmqyINEhJpRFj4nQKva2YkAfoBYnrm3gD8Myw2vgsGqaSlwEZN8iZgLqBeqruxEnE8mTYJ-cvCWLr9r0f1EuZTtsNfkS-u4MwiVjdisjG-AQ)

In the settings for the action, enter the **endpoint URI** for your **request** Firebase Cloud Function

![](https://lh3.googleusercontent.com/TMrdGEOelw8qGFufAY9QcxcaFuLzqcUimDQ3agnB6SOHwUcheHyZ_XnxThYCD2ftFJNaHNAakaZbkKzHXwCjGyc0G0SIVWzwOh9R5hy3yBiOIccqY5_bnesOk4ZzURMlZEAhdVH9N50)

Go to the **Advanced Options** &gt; **Account Linking** page and select the following options

You can enter any **client ID** and client **secret** you want.

For the **Authorization URL,** use the URL that the **Firebase Hosting** provided you with

Fore the Token

![](https://lh3.googleusercontent.com/nRt4ZsBI9Mmir47n14D7zWgjOtAO7dmv8bkKdraqNIyK7iIMf-FDGA1WdIdmXxSfAxlyi4EgYCSItXiXtWe_iTLZ3JWQ5OG7_PeJQGz_IDWuKDGiJKzzzPNCkAvU-rdokuam6N7Lvwo)

You're done!

### All together

Now you can ask the Google Assistant to 

* Turn on  the light
* Turn off the light
* Set the light to green
* Set the light to 50%

You should see the logs in the Serial Monitor and your RGB light following your commands

