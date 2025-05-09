# 📣 ИЗВЕСТИЯ СЪС СМАРТ КОНТАКТ
[![PayPal дарение](https://img.shields.io/badge/PayPal-Дари-синьо?logo=paypal)](https://www.paypal.com/donate/?hosted_button_id=AAWFZVF2XCP5A)  ![Скрипт](https://img.shields.io/badge/logo-yaml-green?logo=yaml)  [![ENGLISH](https://img.shields.io/badge/ENGLISH-language-green?logo=translate&labelColor=gray&style=flat-square&link=https://example.com/bg)](README.md)  

Смарт контактите с функция за мониторинг дават възможност за следене на консумацията. Данните от мониторинга, използвани в автоматизация, могат да бъдат мощен инструмент за уведомяване при приключване на процес от ваш електроуред. В този случай ще ви покажа как смарт контакт ме уведомява за приключването на пералнята.

## 📦 СЪДЪРЖАНИЕ

- [📣 ИЗВЕСТИЯ СЪС СМАРТ КОНТАКТ](#-известия-със-смарт-контакт)
	- [📦 СЪДЪРЖАНИЕ](#-съдържание)
	- [🚀 Tuya Smart Plug Zigbee TS011F](#-tuya-smart-plug-zigbee-ts011f)
	- [🛠️ АВТОМАТИЗАЦИЯ](#️-автоматизация)
		- [🔌 СТАРТ](#-старт)
		- [⏲️ УСЛОВИЕ](#️-условие)
		- [📲 ДЕЙСТВИЕ](#-действие)
		- [📳 КРАЙ](#-край)
	- [🧾 ЦЯЛАТА АВТОМАТИЗАЦИЯ](#-цялата-автоматизация)

## 🚀 Tuya Smart Plug Zigbee TS011F

| ![plug](/img/tuya_smart_plug.png) | [Tuya Smart Plug Zigbee TS011F][plug] е смарт контакт на сравнително добра цена и със задоволителна реакция при обновяване на данните от мониторинга. На прост език – обновяването става сравнително бързо (около 10 сек.). Това не е идеално, но е напълно достатъчно – получавате известието си около 10 секунди след приключване на прането. |
|----|----|

## 🛠️ АВТОМАТИЗАЦИЯ  
*Цел: В автоматизацията ще "запечатаме" времето от началото до края на процеса, за да избегнем фалшиви известия.*

### 🔌 СТАРТ  
Когато пералнята е в покой и изключена, [Tuya Smart Plug Zigbee TS011F][plug] показва стойност "0 watt", което означава, че всичко над "1 watt" ще показва, че пералнята е включена. Именно това условие ще активира нашата автоматизация:

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

### ⏲️ УСЛОВИЕ

⚠️ В тази автоматизация **няма** да използваме отделно `conditions`, защото ще поставим условие директно в действията.

### 📲 ДЕЙСТВИЕ  

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
	  message: Пералнята приключи !!!
	  title: "Home Assistant Service:"
	  data:
		position: top-left
		transparency: 50%
		color: black
		interrupt: 0
		fontsize: medium
		duration: 10
```

Съобщението се изпраща до Android TV с помощта на [Notifications for Android TV](https://www.home-assistant.io/integrations/nfandroidtv/).  

[![ADD Integrations](/img/button%20ADD%20INTEGRATION%20TO.svg)](https://my.home-assistant.io/redirect/config_flow_start?domain=nfandroidtv)

![notifications](/img/notifications.png)

### 📳 КРАЙ  

Автоматизацията следи мощността на контакта, който захранва пералнята. Ако надвиши 1W – пералнята е включена. Когато падне под 1W за поне 5 секунди – приключила е. В този момент се изпраща уведомление.

## 🧾 ЦЯЛАТА АВТОМАТИЗАЦИЯ

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
	  message: Пералнята приключи !!!
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
> Ако този проект ви е харесъл, [ТУК](https://github.com/Bacard1?tab=repositories) ще намерите още интересни гранилища направени от мен.<br>
> Ако срещате затруднения или имате въпроси не се колебайте да се свържете с мен.

[plug]: https://de.aliexpress.com/item/1005007060134011.html