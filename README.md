# Ajax Security via Jeedom Home Assistant Integration

First HA integration that works!

Technically it uses the Ajax API via Jeedom cloud. Jeedom works like a proxy. All commands and events are handled exactly as described in the Ajax API documentation. This integration calls the API and parses events independently of the Jeedom Ajax Plugin. It provides more features and works much better.

# REQUIREMENTS
- Jeedom with external access by URL (Jeedom cloud calls this URL on each Ajax event)
- Jeedom Ajax System plugin. It's one time 8 euro. It opens access to Ajax API via Jeedom Cloud
- A simple code path to Jeedom installation.
- I do not add ajax@jeedom.com user to my hub (it is the Jeedom recommendation). Everything works without this step.
  
!!! It's mandatory to have external access to Jeedom installation !!!

# What works
- Arm, Force ARM, Disarm for HUB and Groups
- Built-in HA action (service) to arm / disarm multiple groups with one click
- Optional HA User name instead of Ajax User in notifications
- Panic and mute fire detectors buttons
- Night Mode, Force Night Mode
- Correct malfunctions messages
- Detailed state for arming and disarming (good for HA UI)
- Correct text messages like in the original app
- Realtime events
- ALARM events
- Sensors with real-time updates for door sensors, temperature, power failure, battery charge, online status, GSM info, masking, relay voltage, and more
- Relays, Sockets
- Realtime events
- Testing it limited to my equipment. Feedback is welcome!
  
# Installation
1. Install Jeedom + MQTT Manager + Ajax System Plugin.
2. Configure Ajax in Jeedom. Configure MQTT in Jeedom to access the MQTT server that HA uses.
3. It is not required to check "Transmit all equipment" in MQTT Manager settings. The integration does not use this information.
4. [Configure Jeedom by this Manual](jeedom/readme.md)
5. Install this integration using HACS
6. Configure access with information from step 4.

   
## HACS Installation

1. Go to http://homeassistant.local:8123/hacs/integrations
1. Add `https://github.com/CommanderTux/ha_ajax_security` custom integration repository
1. Go to http://homeassistant.local:8123/config/integrations and add new integration
1. Choose "Ajax Security via Jeedom" from the list and follow the config flow steps
3. Check the checkbox to enable Panic Button calls. Without it, calls will raise an internal exception. It is better to disable it while testing.

# Extending Manual
[How to add unsupported devices](extending.md)


