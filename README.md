# SB Adjustable Base BLE+MQTT Controller

This project provides a Python-based controller that uses Bluetooth Low Energy (BLE) to control a [Southern Bay International Adjustable Bed Base](https://southbayinternational.com/collections/adjustable-bases) (model number MMKD-TL). This model was sold as a [Sam's Club Member's Mark Premier Adjustable Base with Pillow Tilt and Massage](https://www.samsclub.com/p/members-mark-adjustable-bed-base-massage-wireless-remote-app-usb/prod22421683). The controller integrates with Home Assistant via MQTT, enabling remote control of the bed.

**Note:** This is a personal project that I am sharing in case others find it helpful. It is complete and working, but I am not actively maintaining it or providing support.

---

## About

Adjustable bed bases like the MMKD-TL can be controlled via a radio remote or via Bluetooth. Although an official Android app is available, it poses privacy concerns by requesting invasive permissions (such as precise location and camera access) and transmitting data to servers in China.

This project was created to provide a **privacy-friendly** and **automated** way to control the adjustable bed from a computer or phone using [**Home Assistant**](https://www.home-assistant.io/). Since my Home Assistant server was located outside of bluetooth range from my bed, I chose to use a MQTT broker to share commands from Home Assistant across my local area network. I have a separate computer near the bedroom that is running a python script that:

- Maintains a persistent Bluetooth connection with the bed.
- Subscribes to an MQTT topic (*home/bed/command*) to listen for Home Assistant commands.
- Executes bed movement commands when buttons are tapped or held in Home Assistant.

---

## Features

This controller supports the following functions for MMKD-compatible beds via BLE:
> **Note:** This script does not support sending separate commands to beds with an A + B side. All commands are sent to "**Both**" sides.

✅ Pillow/Head Tilt **Up & Down**  
✅ Back Tilt **Up & Down**  
✅ Leg Raise **& Lower**  
✅ **Presets**:
   - Sit (Back raised, feet lowered flat)
   - Flat (All tilts fully down)
   - Zero-G (Back partially raised, legs fully raised)

✅ **Vibration Functions**:
   - Vibrate **Head Massage** Motor
   - Vibrate **Foot Massage** Motor 

✅ **Home Assistant Custom Remote Control Card**:

![image](https://github.com/user-attachments/assets/03b76298-212b-4c0c-9cd3-cc5125b49e5d)

---

## Prerequisites

### **Software Requirements**
- **Python 3.7+**
- **Python Libraries**:
  - [**bleak**](https://github.com/hbldh/bleak) – A Bluetooth Low Energy library.
  - [**aiomqtt**](https://pypi.org/project/aiomqtt/) – For asynchronous MQTT messaging.
- **MQTT Broker** (e.g., [Mosquitto](https://github.com/home-assistant/addons/blob/master/mosquitto/DOCS.md))
- **Home Assistant** (optional, but recommended)
- **Home Assistant Custom Cards** *(if using the provided Lovelace YAML)*
  - [**button-card**](https://github.com/custom-cards/button-card) – Improves responsiveness for hold actions.
  - [**card_mod**](https://github.com/thomasloven/lovelace-card-mod) – Used for styling adjustments.

### **Hardware Requirements**
- **Computer running Python** (e.g., Raspberry Pi, Linux PC)
- **Bluetooth Adapter** that supports BLE (Bluetooth Low Energy)

---

## How to Use
1. Download the python script:

         git clone https://github.com/delizin/sb_adj_base_bed_controller.git
         cd adjustable-bed-mqtt-ble

3. Create a virtual environment (optional but recommended) and install python dependencies:
   
        python3 -m venv venv
        source venv/bin/activate
        pip install -r requirements.txt

   or manually install

        pip install bleak aiomqtt

5. Configure the Script for your BLE Device:
   - Edit the following configuration variables in the mqtt_bed_controller.py python script:
     - BED_ADDRESS = "D0:87:18:BA:53:BC"  # Replace with your bed's MAC address
     - MQTT_BROKER = "192.168.1.11"       # Your MQTT broker IP
     - MQTT_PORT = 1883                    # Default MQTT port
     - MQTT_USERNAME = "your_username"
     - MQTT_PASSWORD = "your_password"
     - MQTT_TOPIC = "home/bed/command"      # Change if needed (must match Home Assistant)
publish the message.

   **The provided UUIDs for WRITE_CHAR_UUID and NOTIFY_CHAR_UUID should work for most MMKD adjustable bases. Other models may require different UUIDs.**

6. Install Home Assistant Dependencies if using the Home Assistant dashboard, install: - See their websites for instructions. I found [HACS](https://www.hacs.xyz/) to be the simplest method for me.
   - [**button-card**](https://github.com/custom-cards/button-card)
   - [**card_mod**](https://github.com/thomasloven/lovelace-card-mod)
8. Modify Home Assistant configuration.yaml
   - Append the contents of configuration.yaml from this repository to your Home Assistant configuration.yaml file. This adds the commands as Home Assistant entities so I could use them for automations in the future and isn't strictly necessary, but you would need to remove the entity references in the cards if you chose not to do it.
10. Create Home Assistant Card for Remote Control
    1. On your Home Assistant Dashboard, go into Edit Mode, then click Add Card
    2. Select the card type as "Manual" and clear any text that may be prepopulated in the Card Configuration code box.
    3. Copy and paste the contents of the home-assistant-card.yaml file from this repository into the Card Configuration code box.
11. Ensure that the MQTT Broker server is running.
12. Run the mqtt_bed_controller.py python script.

        python mqtt_bed_controller.py

*After ensuring that the script is working, I recommend running it as a system or background process so that it is always on.*

---

## Troubleshooting
- BLE Connection Issues:
  - Make sure your BLE adapter is working properly and that the BED_ADDRESS and UUIDs match your device.

- MQTT Issues:
  - Verify that your MQTT broker settings are correct. Check the MQTT logs to ensure that messages are being published and received.

- Home Assistant Dashboard:
  - If custom button cards or card_mod are not working as expected, ensure you have installed them via HACS or manually and that your Home Assistant version is up-to-date.

---

## License
This project is licensed under the [MIT License](https://en.wikipedia.org/wiki/MIT_License).
