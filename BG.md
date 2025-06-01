![BANNER](/img/banner.png)
# 📣 ИЗВЕСТИЯ СЪС СМАРТ КОНТАКТ

[![Home Assistant](https://img.shields.io/badge/🏠_Home_Assistant-41BDF5?logo=homeassistant)](https://www.home-assistant.io/) [![Donate via PayPal](https://img.shields.io/badge/PayPal-Donate-blue?logo=paypal)](https://www.paypal.com/donate/?hosted_button_id=AAWFZVF2XCP5A)
![Script](https://img.shields.io/badge/logo-yaml-green?logo=yaml)
[![Български](https://img.shields.io/badge/BG_Български-език-green?logo=translate&labelColor=gray&style=flat-square&link=https://example.com/bg
)](BG.md)
[![Deutch](https://img.shields.io/badge/DE_Deutsche-sprache-green?logo=translate&labelColor=gray&style=flat-square&link=https://example.com/bg
)](DE.md)
[![English](https://img.shields.io/badge/EN_English-language-green?logo=translate&labelColor=gray&style=flat-square&link=https://example.com/bg)](README.md)

Тази автоматизация за Home Assistant ви уведомява, когато уред, включен в смарт контакт (например пералня, кафемашина и др.), приключи работа. Използва наблюдение на консумацията на енергия в реално време, за да засече кога уредът спира да работи, и изпраща визуално известие до вашия телевизор или Android устройство. Подходящо за пестене на енергия и удобство в ежедневието.

🔧 Създадено с YAML  
📡 Съвместимо с MQTT  
💡 Енергийно ефективно  
📺 Поддържа Android TV & Mi TV  

---

## 📦 СЪДЪРЖАНИЕ

- [📣 ИЗВЕСТИЯ СЪС СМАРТ КОНТАКТ](#-известия-със-смарт-контакт)
  - [📦 СЪДЪРЖАНИЕ](#-съдържание)
  - [🚀 Tuya Smart Plug Zigbee TS011F](#-tuya-smart-plug-zigbee-ts011f)
  - [МЕТОД 1: Автоматизация за известия](#метод-1-автоматизация-за-известия)
    - [🔌 Старт на автоматизацията](#-старт-на-автоматизацията)
    - [⏲️ Условие](#️-условие)
    - [📲 Действие](#-действие)
    - [📳 Край на автоматизацията](#-край-на-автоматизацията)
    - [🧾 Пълният код на автоматизацията](#-пълният-код-на-автоматизацията)
  - [МЕТОД 2: Използване на двоичен сензор](#метод-2-използване-на-двоичен-сензор)
    - [Създаване на двоичен сензор](#създаване-на-двоичен-сензор)
    - [Настройки на сензора](#настройки-на-сензора)
  - [💡 Съвети и допълнителна информация](#-съвети-и-допълнителна-информация)

---

## 🚀 Tuya Smart Plug Zigbee TS011F

| ![plug](/img/tuya_smart_plug.png) | [Tuya Smart Plug Zigbee TS011F][plug] е смарт контакт на сравнително добра цена и със задоволителна реакция при обновяване на данните от мониторинга. На прост език – обновяването става сравнително бързо (около 10 секунди). Това не е идеално, но е напълно достатъчно – получавате известието си около 10 секунди след приключване на прането. |
|----|----|

---

## МЕТОД 1: Автоматизация за известия

### 🔌 Старт на автоматизацията  
Когато пералнята е в покой и изключена, [Tuya Smart Plug Zigbee TS011F][plug] показва стойност "0 watt". Всичко над "1 watt" означава, че пералнята е включена. Това условие активира автоматизацията:

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

### ⏲️ Условие  
⚠️ В тази автоматизация **няма** да използваме отделно `conditions`, защото условието е вградено в действията.

### 📲 Действие  
След като консумацията на енергия падне под 1W за 5 секунди, се изпраща известие:

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

Известието се изпраща до Android TV с помощта на [Notifications for Android TV](https://www.home-assistant.io/integrations/nfandroidtv/).  

[![ADD Integrations](/img/button%20ADD%20INTEGRATION%20TO.svg)](https://my.home-assistant.io/redirect/config_flow_start?domain=nfandroidtv)  

![notifications](/img/notifications.png)  

### 📳 Край на автоматизацията  
Автоматизацията следи мощността на контакта. Ако надвиши 1W – пералнята е включена. Когато падне под 1W за 5 секунди – приключила е, и се изпраща уведомление.

### 🧾 Пълният код на автоматизацията  

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

## МЕТОД 2: Използване на двоичен сензор  

Създаването на двоичен сензор улеснява процеса и позволява визуализация на състоянието на уреда в картата на стаята.  

### Създаване на двоичен сензор  

1. Създайте нов шаблон, като щракнете на бутона:  
   [![Add Template](/img/button%20ADD%20INTEGRATION%20TO.svg)](https://my.home-assistant.io/redirect/config_flow_start?domain=template)  

2. Изберете `binary_sensor`:  
   ![img](/img/temp.png)  

3. В полето "State template*" въведете:  

```yaml
{% if states('sensor.steckdose_002_wohnwand_power') | float == 0 %} 
  off
{% else %}
  on
{% endif %}
```

> `float == 0` задава стойност по подразбиране, защото без нея стойността би била `null`, което е проблем за сензорите от тип `INT` (числа). Това е прост пример, при който ако стойността на сензора е по-голяма от 0, уредът е включен; в противен случай е изключен.  

### Настройки на сензора  
Задайте към кое устройство принадлежи сензорът и приключвате:  
![img](/img/binary_sensor.png)  

---

## 💡 Съвети и допълнителна информация  

- Ако този проект ви е харесал, [ТУК](https://github.com/Bacard1?tab=repositories) ще намерите още интересни проекти, направени от мен.  
- Ако срещате затруднения или имате въпроси, не се колебайте да се свържете с мен.  

[plug]: https://de.aliexpress.com/item/1005007060134011.html