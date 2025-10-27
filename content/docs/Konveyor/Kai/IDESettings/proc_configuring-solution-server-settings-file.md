---
title: "Configuring the Solution Server settings"
date: 2025-12-04T14:58:52-06:00
draft: false
---
You need a Keycloak realm and the Solution Server URL to connect
Konveyor extension with the Solution Server.

## Prerequisites

- The Solution Server URL is available.

- An administrator configured the Keycloak realm for the Solution
  Server.

## Procedure

1.  Type `Ctrl + Shift + P` or `Cmd + Shift + P` on the search bar and
    enter `Preferences:Open User Settings (JSON)`.

2.  In the `settings.json` file, enter `Ctrl + SPACE` to enable the
    auto-complete for the Solution Server configurable fields.

3.  Modify the following configuration as necessary:

     ```json
        {
            "konveyor.solutionServer": {

                "url": "https://konveyor.apps.konveyor-ai.example.com/hub/services/kai/api",

                "enabled": true,
                "auth": {

                   "enabled": true, 
                   "insecure": true,
                   "realm": "tackle"
                },

            }
        }
     ```
     
    **NOTE:** When you enable Solution Server authentication for the first time, you must enter the `username` and `password` in the VS >Code search bar.

    **TIP:** Enter `Konveyor: Restart Solution Server` in the Command Palette >to restart the Solution Server.
