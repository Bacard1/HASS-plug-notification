![BANNER](/img/banner.png)

# 📣 HASS PLUG NOTIFICATION
[![PayPal Donate](https://img.shields.io/badge/PayPal-Donate-blue?logo=paypal)](https://www.paypal.com/donate/?hosted_button_id=AAWFZVF2XCP5A)  ![Script](https://img.shields.io/badge/logo-yaml-green?logo=yaml)  [![БългарскиЕзик](https://img.shields.io/badge/Български-Език-green?logo=translate&labelColor=gray&style=flat-square&link=https://example.com/bg)](BG.md)  

Smart plugs with monitoring capabilities allow you to track energy consumption. The monitoring data used in an automation can be a powerful tool for notifying you when a process from your appliance has finished. In this case, I’ll show how a smart plug notifies me when the washing machine has completed its cycle.

## 📦 CONTENTS

- [📣 HASS PLUG NOTIFICATION](#-hass-plug-notification)
	- [📦 CONTENTS](#-contents)
	- [🚀 Tuya Smart Plug Zigbee TS011F](#-tuya-smart-plug-zigbee-ts011f)
	- [🛠️ AUTOMATION](#️-automation)
		- [🔌 START](#-start)
		- [⏲️ CONDITION](#️-condition)
		- [📲 ACTION](#-action)
		- [📳 END](#-end)
	- [🧾 FULL AUTOMATION](#-full-automation)

## 🚀 Tuya Smart Plug Zigbee TS011F

| ![plug](/img/tuya_smart_plug.png) | [Tuya Smart Plug Zigbee TS011F][plug] is a smart plug at a relatively good price with decent update speed for power monitoring data. Simply put – the updates occur fairly quickly (around 10 seconds). That’s not ideal, but it’s more than acceptable for our purpose: receiving a notification roughly 10 seconds after the washing machine finishes. |
|----|----|

## 🛠️ AUTOMATION  
*Goal: Capture the time from the start to the end of the washing process to avoid false notifications.*

### 🔌 START  
When the washing machine is idle and turned off, [Tuya Smart Plug Zigbee TS011F][plug] shows "0 watt". Anything above "1 watt" means the washing machine is running. That’s the trigger we’ll use for the automation:

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

### ⏲️ CONDITION

⚠️ This automation does **not** use a separate `conditions` block, because we apply conditions directly within the actions.

### 📲 ACTION  

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

The message is sent to Android TV using [Notifications for Android TV](https://www.home-assistant.io/integrations/nfandroidtv/).  

[![ADD Integrations](/img/button%20ADD%20INTEGRATION%20TO.svg)](https://my.home-assistant.io/redirect/config_flow_start?domain=nfandroidtv)

![notifications](/img/notifications.png)

### 📳 END  

The automation monitors the power of the socket powering the washing machine. If power goes above 1W – it means the machine is running. If it drops below 1W for 5 seconds – it’s finished. A notification is then sent.

## 🧾 FULL AUTOMATION

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
---
> [!TIP]
> If you like this project, check out [more of my repositories here](https://github.com/Bacard1?tab=repositories) .<br>
> If you need help or have questions, feel free to contact me.


[plug]: https://de.aliexpress.com/item/1005007060134011.html