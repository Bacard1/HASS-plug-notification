
![BANNER](/img/banner.png)  
# 📣 BENACHRICHTIGUNGEN MIT SMART STECKDOSE  

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?color=ff00d8)](https://opensource.org/licenses/MIT)
![GitHub last commit](https://img.shields.io/github/last-commit/Bacard1/HASS-plug-notification.svg?color=ff00d8)
[![hacs_badge](https://img.shields.io/badge/HACS-2025.5.3-orange.svg?color=ff00d8)](https://github.com/hacs/integration)

[![Home Assistant](https://img.shields.io/badge/.-HOME_ASSISTANT-blue?logo=homeassistant)](https://www.home-assistant.io/) 
[![Donate via PayPal](https://img.shields.io/badge/PayPal-DONATE-blue?logo=paypal)](https://www.paypal.com/donate/?hosted_button_id=AAWFZVF2XCP5A)
![Script](https://img.shields.io/badge/Script-YAML-blue?logo=yaml)

[![Български](https://img.shields.io/badge/BG-ЕЗИК-gree?logo=translate&labelColor=gray&style=flat-square&link=https://example.com/bg
)](BG.md)
[![Deutch](https://img.shields.io/badge/DE-SPRACHE-gree?logo=translate&labelColor=gray&style=flat-square&link=https://example.com/bg
)](DE.md)
[![English](https://img.shields.io/badge/EN-LANGUAGE-gree?logo=translate&labelColor=gray&style=flat-square&link=https://example.com/bg)](README.md)

Diese Automatisierung für Home Assistant benachrichtigt Sie, wenn ein Gerät, das an eine smarte Steckdose angeschlossen ist (z. B. Waschmaschine, Kaffeemaschine usw.), seine Arbeit beendet hat. Sie überwacht den Energieverbrauch in Echtzeit, um festzustellen, wann das Gerät stoppt, und sendet eine visuelle Benachrichtigung an Ihren Fernseher oder Ihr Android-Gerät. Ideal für Energieeinsparungen und mehr Komfort im Alltag.  

🔧 Erstellt mit YAML  
📡 Kompatibel mit MQTT  
💡 Energieeffizient  
📺 Unterstützt Android TV & Mi TV  

---  

## 📦 INHALT  

- [📣 BENACHRICHTIGUNGEN MIT SMART STECKDOSE](#-benachrichtigungen-mit-smart-steckdose)
  - [📦 INHALT](#-inhalt)
  - [🚀 Tuya Smart Plug Zigbee TS011F](#-tuya-smart-plug-zigbee-ts011f)
  - [METHODE 1: Automatisierung für Benachrichtigungen](#methode-1-automatisierung-für-benachrichtigungen)
    - [🔌 Start der Automatisierung](#-start-der-automatisierung)
    - [⏲️ Bedingung](#️-bedingung)
    - [📲 Aktion](#-aktion)
    - [📳 Ende der Automatisierung](#-ende-der-automatisierung)
    - [🧾 Vollständiger Automatisierungscode](#-vollständiger-automatisierungscode)
  - [METHODE 2: Verwendung eines binären Sensors](#methode-2-verwendung-eines-binären-sensors)
    - [Erstellen eines binären Sensors](#erstellen-eines-binären-sensors)
    - [Sensor-Einstellungen](#sensor-einstellungen)
  - [💡 Tipps und zusätzliche Informationen](#-tipps-und-zusätzliche-informationen)

---  

## 🚀 Tuya Smart Plug Zigbee TS011F  

| ![plug](/img/tuya_smart_plug.png) | Der [Tuya Smart Plug Zigbee TS011F][plug] ist eine preisgünstige smarte Steckdose mit zufriedenstellender Reaktionszeit bei Datenaktualisierungen. Einfach ausgedrückt – die Aktualisierung erfolgt relativ schnell (etwa 10 Sekunden). Das ist nicht perfekt, aber völlig ausreichend – Sie erhalten Ihre Benachrichtigung etwa 10 Sekunden nach Ende des Waschvorgangs. |  
|----|----|  

---  

## METHODE 1: Automatisierung für Benachrichtigungen  

### 🔌 Start der Automatisierung  
Wenn die Waschmaschine im Leerlauf und ausgeschaltet ist, zeigt der [Tuya Smart Plug Zigbee TS011F][plug] einen Wert von "0 Watt" an. Alles über "1 Watt" bedeutet, dass die Waschmaschine eingeschaltet ist. Diese Bedingung löst die Automatisierung aus:  

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

### ⏲️ Bedingung  
⚠️ In dieser Automatisierung wird **keine** separate `conditions` verwendet, da die Bedingung in die Aktionen integriert ist.  

### 📲 Aktion  
Sobald der Energieverbrauch für 5 Sekunden unter 1W fällt, wird eine Benachrichtigung gesendet:  

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
      message: Waschmaschine fertig !!!  
      title: "Home Assistant Service:"  
      data:  
        position: top-left  
        transparency: 50%  
        color: black  
        interrupt: 0  
        fontsize: medium  
        duration: 10  
```  

Die Benachrichtigung wird an Android TV über [Notifications for Android TV](https://www.home-assistant.io/integrations/nfandroidtv/) gesendet.  

[![ADD Integrations](/img/button%20ADD%20INTEGRATION%20TO.svg)](https://my.home-assistant.io/redirect/config_flow_start?domain=nfandroidtv)  

![notifications](/img/notifications.png)  

### 📳 Ende der Automatisierung  
Die Automatisierung überwacht die Leistung der Steckdose. Wenn sie über 1W steigt – ist die Waschmaschine eingeschaltet. Wenn sie für 5 Sekunden unter 1W fällt – ist der Vorgang beendet, und eine Benachrichtigung wird gesendet.  

### 🧾 Vollständiger Automatisierungscode  

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
      message: Waschmaschine fertig !!!  
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

## METHODE 2: Verwendung eines binären Sensors  

Das Erstellen eines binären Sensors vereinfacht den Prozess und ermöglicht die Visualisierung des Gerätestatus auf der Raumkarte.  

### Erstellen eines binären Sensors  

1. Erstellen Sie eine neue Vorlage, indem Sie auf die Schaltfläche klicken:  
   [![Add Template](/img/button%20ADD%20INTEGRATION%20TO.svg)](https://my.home-assistant.io/redirect/config_flow_start?domain=template)  

2. Wählen Sie `binary_sensor`:  
   ![img](/img/temp.png)  

3. Geben Sie im Feld "State template*" Folgendes ein:  

```yaml  
{% if states('sensor.steckdose_002_wohnwand_power') | float == 0 %}  
  off  
{% else %}  
  on  
{% endif %}  
```  

> `float == 0` setzt einen Standardwert, da ohne ihn der Wert `null` wäre, was für Sensoren vom Typ `INT` (Zahlen) problematisch ist. Dies ist ein einfaches Beispiel, bei dem der Sensorwert größer als 0 bedeutet, dass das Gerät eingeschaltet ist; andernfalls ist es ausgeschaltet.  

### Sensor-Einstellungen  
Weisen Sie den Sensor dem entsprechenden Gerät zu und schließen Sie den Vorgang ab:  
![img](/img/binary_sensor.png)  

---  

## 💡 Tipps und zusätzliche Informationen  

- Wenn Ihnen dieses Projekt gefallen hat, finden Sie [HIER](https://github.com/Bacard1?tab=repositories) weitere interessante Projekte von mir.  
- Wenn Sie Schwierigkeiten haben oder Fragen haben, zögern Sie nicht, mich zu kontaktieren.  

[plug]: https://de.aliexpress.com/item/1005007060134011.html  