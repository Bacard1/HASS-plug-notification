![BANNER](/img/banner.png)  
<<<<<<< HEAD
# 📣 SMART PLUG NOTIFICATIONS  

[![Home Assistant](https://img.shields.io/badge/🏠_Home_Assistant-41BDF5?logo=homeassistant)](https://www.home-assistant.io/) [![Donate via PayPal](https://img.shields.io/badge/PayPal-Donate-blue?logo=paypal)](https://www.paypal.com/donate/?hosted_button_id=AAWFZVF2XCP5A)
![Script](https://img.shields.io/badge/logo-yaml-green?logo=yaml)
[![Български](https://img.shields.io/badge/BG_Български-език-green?logo=translate&labelColor=gray&style=flat-square&link=https://example.com/bg
)](BG.md)
[![Deutch](https://img.shields.io/badge/DE_Deutsche-sprache-green?logo=translate&labelColor=gray&style=flat-square&link=https://example.com/bg
)](DE.md)
[![English](https://img.shields.io/badge/EN_English-language-green?logo=translate&labelColor=gray&style=flat-square&link=https://example.com/bg)](README.md)
=======

# 📣 SMART PLUG NOTIFICATIONS  


[![PayPal Donation](https://img.shields.io/badge/PayPal-Donate-blue?logo=paypal)](https://www.paypal.com/donate/?hosted_button_id=AAWFZVF2XCP5A)  
![Script](https://img.shields.io/badge/logo-yaml-green?logo=yaml)  
[![BULGARIAN](https://img.shields.io/badge/BULGARIAN-language-green?logo=translate&labelColor=gray&style=flat-square&link=https://example.com/en)](BG.md)  
>>>>>>> 943b4005188bd25aeb59f3fea52c7d2640fca2f5

This Home Assistant automation notifies you when a device connected to a smart plug (e.g., washing machine, coffee maker, etc.) finishes its cycle. It monitors real-time power consumption to detect when the device stops operating and sends a visual notification to your TV or Android device. Ideal for energy savings and daily convenience.  

🔧 Built with YAML  
📡 MQTT Compatible  
💡 Energy Efficient  
📺 Supports Android TV & Mi TV  

---

## 📦 CONTENTS  

- [📣 SMART PLUG NOTIFICATIONS](#-smart-plug-notifications)
- [](#)
- [📣 SMART PLUG NOTIFICATIONS](#-smart-plug-notifications-1)
  - [📦 CONTENTS](#-contents)
  - [🚀 Tuya Smart Plug Zigbee TS011F](#-tuya-smart-plug-zigbee-ts011f)
  - [METHOD 1: Notification Automation](#method-1-notification-automation)
    - [🔌 Automation Trigger](#-automation-trigger)
    - [⏲️ Condition](#️-condition)
    - [📲 Action](#-action)
    - [📳 Automation Outcome](#-automation-outcome)
    - [🧾 Full Automation Code](#-full-automation-code)
  - [METHOD 2: Using a Binary Sensor](#method-2-using-a-binary-sensor)
    - [Creating a Binary Sensor](#creating-a-binary-sensor)
    - [Sensor Configuration](#sensor-configuration)
  - [💡 Tips \& Additional Info](#-tips--additional-info)

---

## 🚀 Tuya Smart Plug Zigbee TS011F  

| ![plug](/img/tuya_smart_plug.png) | The [Tuya Smart Plug Zigbee TS011F][plug] is an affordable smart plug with decent real-time monitoring responsiveness. In simple terms, data updates are relatively fast (around 10 seconds). While not perfect, it's sufficient—you'll receive the notification about 10 seconds after your laundry finishes. |  
|----|----|  

---

## METHOD 1: Notification Automation  

### 🔌 Automation Trigger  
When the washing machine is idle and turned off, the [Tuya Smart Plug Zigbee TS011F][plug] shows a value of "0 watt." Any reading above "1 watt" means the washing machine is on. This condition triggers the automation:  

```yaml
alias: HASS PLUG NOTIFICATION
description: ""
triggers:
  - platform: numeric_state
    entity_id:
      - sensor.steckdose_002_wohnwand_power
    above: 1
    for:
      hours: 0
      minutes: 0
      seconds: 10
```

### ⏲️ Condition  
⚠️ This automation does **not** use a separate `conditions` block, as the condition is embedded in the actions.  

### 📲 Action  
Once power consumption drops below 1W for 5 seconds, a notification is sent:  

```yaml
actions:
  - wait_for_trigger:
      - platform: numeric_state
        entity_id:
          - sensor.steckdose_002_wohnwand_power
        below: 1
        for:
          hours: 0
          minutes: 0
          seconds: 5
        continue_on_timeout: false
  - service: notify.mitv
    metadata: {}
    data:
      message: Washing machine finished !!!
      title: "Home Assistant Service:"
      data:
        position: top-left
        transparency: 50%
        color: black
        interrupt: 0
        fontsize: medium
        duration: 10
```

The notification is sent to Android TV using [Notifications for Android TV](https://www.home-assistant.io/integrations/nfandroidtv/).  

[![ADD Integrations](/img/button%20ADD%20INTEGRATION%20TO.svg)](https://my.home-assistant.io/redirect/config_flow_start?domain=nfandroidtv)  

![notifications](/img/notifications.png)  

### 📳 Automation Outcome  
The automation monitors the plug's power usage. If it exceeds 1W, the washing machine is on. When it drops below 1W for 5 seconds, the cycle is complete, and a notification is sent.  

### 🧾 Full Automation Code  

```yaml
alias: HASS PLUG NOTIFICATION
description: ""
triggers:
  - platform: numeric_state
    entity_id:
      - sensor.steckdose_002_wohnwand_power
    above: 1
    for:
      hours: 0
      minutes: 0
      seconds: 10
conditions: []
actions:
  - wait_for_trigger:
      - platform: numeric_state
        entity_id:
          - sensor.steckdose_002_wohnwand_power
        below: 1
        for:
          hours: 0
          minutes: 0
          seconds: 5
        continue_on_timeout: false
  - service: notify.mitv
    metadata: {}
    data:
      message: Washing machine finished !!!
      title: "Home Assistant Service:"
      data:
        position: top-left
        transparency: 50%
        color: black
        interrupt: 0
        fontsize: medium
        duration: 10
mode: single
```

---

## METHOD 2: Using a Binary Sensor  

Creating a binary sensor simplifies the process and allows visualization of the device's state on your room map.  

### Creating a Binary Sensor  

1. Create a new template by clicking the button:  
   [![Add Template](/img/button%20ADD%20INTEGRATION%20TO.svg)](https://my.home-assistant.io/redirect/config_flow_start?domain=template)  

2. Select `binary_sensor`:  
   ![img](/img/temp.png)  

3. In the "State template*" field, enter:  

```yaml
{% if states('sensor.steckdose_002_wohnwand_power') | float == 0 %} 
  off
{% else %}
  on
{% endif %}
```

> `float == 0` sets a default value because without it, the sensor would return `null`, which is problematic for `INT` (integer) sensors. This is a simple example where a power reading above 0 means the device is on; otherwise, it's off.  

### Sensor Configuration  
Assign the sensor to the correct device, and you're done:  
![img](/img/binary_sensor.png)  

---

## 💡 Tips & Additional Info  

- If you liked this project, check out [HERE](https://github.com/Bacard1?tab=repositories) for more interesting repositories I've created.  
- If you encounter issues or have questions, feel free to reach out.  

[plug]: https://de.aliexpress.com/item/1005007060134011.html
