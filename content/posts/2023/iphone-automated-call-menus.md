---
title: "iPhone Call Menu Automation"
tags: ["iphone", "ios", "phone", "automate", "call", "menu"]
date: 2023-07-07T13:18:21-04:00
draft: false
---

With rise in Automated Call Menus, it has become tedious to get past complex menu options. Fortunately iPhone has a built in option to automate this process.

Below, I am using Fidelity as an example, as it requires you to login using an **numeric** representation of your username and password. As my password is very complex, this guide streamlines the process and saves me 1-5 minutes each time I call Fidelity.

> **Note:** Total setup time should take roughly **5-30 minutes** depending on complexity of call menu.

## Fidelity Automated Call Timestamps

Below are the rough timestamps for Fidelity support call menu options.

| Duration (seconds) | Summary                                      |
|--------------------|----------------------------------------------|
| 00:00 - 00:15      | Welcome screen and numerical username prompt |
| 00:15 - 00:20      | Numerical password prompt                    |

## Automation Shortcuts

Below are the options you see on iOS if you select the `+*#` option on the keypad when adding a **New Contact**.

| Key   | Description              |
|-------|--------------------------|
| `,`   | 2 second pause (roughly) |
| `;`   | Wait until tap           |
| `0-9` | Automate button press    |
| `#`   | Automate `#` press       |

![iPhone New Contact Image](/images/iphone_new_contact_phone.png)

## Example

| Description                | Phone Number                                         |
|----------------------------|------------------------------------------------------|
| Fidelity Support Number    | `(800) 343-3548`                                     |
| Automated Call Menu Number | `(800) 343-3548,,,,,,,,[username]#,,,,,[password]#,` |

> **Note:** Replace `[username]` and `[password]` (brackets `[]` included) with your **numerical** username and password.

This will do the following:

1. Call `(800) 343-3548`
2. Wait `16` seconds
3. Type in `numerical username` and press `#`
4. Type in `numerical password` and press `#`
5. Wait `2` seconds

Adding a new phone number with the new `phone number` automates the cell menu entirely using your iPhone.

No longer do you have to type in the required information manually to get to the support you need!
