---
title: "Advanced Information"
permalink: /advanced
sort: 7
---
### Software Architecture

For the more adventurous of you, who want to tweak, extend and improve PiFire for your purposes, I've tried to architect PiFire in a way to make it friendly to use on different hardware.  

You may have noticed that we have a few modules that can be used interchangeably with a common API.

* **Grill Platform (grillplat_xxxx.py)**: Used for Input / Output with grill itself.  (i.e. Any Inputs (Buttons / Switche) and Relay Outputs for Fan, Auger, Igniter)
* **ADC (adc_xxxx.py)**: Used to get data for the different temperature probes (Grill, Probe1, Probe2).
* **Display (display_xxxx.py)**: Interface for the display.  Used to control and print information to the display.  
* **Distance / Hopper Level (dist_xxxx.py)**: Interface for getting hopper level input information - used by the pellet manager.  

Adding this layer for these components allows you to create your own modules to support different ADC devices, Display Devices, and Grill configurations.

#### Prototyping

Each of the above modules has a prototype version *(i.e. grillplat_prototype.py)* which is used to test PiFire on other platforms (i.e. Windows, Linux or MacOS).  Input and output is simulated, and can be used in conjunction with your own modules for testing purposes.  To enable prototype mode, simply set `prototype_mode = True` at the top of `control.py`.  After this has been changed, the software can be run on nearly any platform (Linux, Windows, Mac, Raspberry Pi, etc.) as long as python3 and flask are installed.  

When prototyping, functionality, it's helpful to run control.py in one process and the app.py in another process.  This way you can access the PiFire webui via your web browser of choice and monitor the console output of the control.py script in a separate window.  

More to come in this section.

#### API

PiFire offers a basic API to adjust settings or to control the system over the wire.  This API is used by the dashboard javascript to control the grill, which can be used as a reference for others build apps, dashboards or even other hardware that interfaces with the system.  

The API has changed since v1.5.0 to include more flexibility of probe definitions and types.  

- GET Settings (/api/settings): Dumps all of the current settings from settings.json in JSON format
- GET Control (/api/control): Dumps all of the current control from control.json in JSON format
- GET Current (/api/current): Dumps JSON of some key current activity, for ex:

```json 
{
  "current": {
    "F": {
      "Probe1": 0,
      "Probe2": 0
    },
    "NT": {
      "Grill": 0,
      "Probe1": 0,
      "Probe2": 0
    },
    "P": {
      "Grill": 0
    },
    "PSP": 0
  },
  "notify_data": [
    {
      "keep_warm": false,
      "label": "Grill",
      "name": "Grill",
      "req": false,
      "shutdown": false,
      "target": 0,
      "type": "probe"
    },
    {
      "keep_warm": false,
      "label": "Probe1",
      "name": "Probe-1",
      "req": false,
      "shutdown": false,
      "target": 0,
      "type": "probe"
    },
    {
      "keep_warm": false,
      "label": "Probe2",
      "name": "Probe-2",
      "req": false,
      "shutdown": false,
      "target": 0,
      "type": "probe"
    },
    {
      "keep_warm": false,
      "label": "Timer",
      "req": false,
      "shutdown": false,
      "type": "timer"
    },
    {
      "keep_warm": false,
      "label": "Hopper",
      "last_check": 0,
      "req": true,
      "shutdown": false,
      "type": "hopper"
    }
  ],
  "status": {
    "mode": "Stop",
    "name": "Production",
    "s_plus": true,
    "status": "",
    "ui_hash": -1786823920,
    "units": "F"
  }
}
```

- POST Settings (/api/settings): Change any settings data from settings.json.  Post format identical to the format of the settings.json file.  For ex: 

```json
{ "globals" : { "grill_name" : "Smokey the Bear" } }
```

- POST Control (/api/control): Change any control data from control.json.  Post format identical to the format of the control data structure in redis.  For ex: 

```json
{ "updated" : true, "mode" : "Startup" } 
```

This code will change the mode to 'Startup' and by setting the 'updated' key to 'true', will tell the control script to check for the mode change.