**mitsubishi2MQTT**
ESP8266/ESP32 module to control Mitsubishi Electric HVAC unit. Support control with Home Assistant, buit-in web app and MQTT.

***

## Screenshots:
| ![Main](https://github.com/dzungpv/mitsubishi2MQTT/blob/master/images/main_page.jpeg) | ![Control](https://github.com/dzungpv/mitsubishi2MQTT/blob/master/images/control_page.jpeg) | ![Config](https://github.com/dzungpv/mitsubishi2MQTT/blob/master/images/config_page.jpeg) |
| --------------------------------------- | --------------------------------------- | --------------------------------------- |                                
| ![Init](https://github.com/dzungpv/mitsubishi2MQTT/blob/master/images/initial_setup.jpeg) | ![Unit](https://github.com/dzungpv/mitsubishi2MQTT/blob/master/images/unit_page.jpeg) | ![Status](https://github.com/dzungpv/mitsubishi2MQTT/blob/master/images/status_page.jpeg) |

***

## Features:
 - Initial config: WIFI AP mode and web portal
 - Web interface for configuration, status and control, firmware upgrade
 - Homeassistant autodiscovery and control with MQTT
 - Control with MQTT
 - Multilanguages, user can change in SETUP->UNIT or choose in initial setup

***

## Supported Mitsubishi Electrict Units
Basically, if the unit has a [`CN105`](https://github.com/dzungpv/mitsubishi2MQTT/blob/master/hardware/CN105.jpg) header on the main board, it should
work. The [HeatPump
wiki](https://github.com/SwiCago/HeatPump/wiki/Supported-models) has a long confirmed list.

The same [`CN105`](https://github.com/dzungpv/mitsubishi2MQTT/blob/master/hardware/CN105.jpg) connector is used by the Mitsubishi KumoCloud remotes, which
have a
[compatibility list](https://www.mitsubishicomfort.com/themes/custom/MitsubishiMegaSite/src/pdf/M_Submittal_kumo_cloud.pdf)
available.

The software and hardware has been tested by the author for 5 years on the following units:
* `MSZ-HL25VA`
* `MSZ-GE25VA`
* `MSY-GM24VA`

## Demo Circuit

<img src="https://github.com/dzungpv/mitsubishi2MQTT/blob/master/hardware/CN105_ESP8266.png"/>

This circuit using by the author, here is some [boards](https://github.com/dzungpv/mitsubishi2MQTT/blob/master/hardware/Before_Install.jpg) before install.

You can also use ESP-32 module for more processing power. If using ESP-32 by default app use Serial (serial 0 port).
You can assign any compatible pin in the SETUP->OTHERS to use custom pin, example TX: 26, RX: 27, check ESP-32 manual for pin, when TX and RX set it will use Serial2 port. 
You can see more pictures in [`hardware`](https://github.com/dzungpv/mitsubishi2MQTT/tree/master/hardware) folder.
***

## Attention:
:warning: You have to open the indoor unit to have access to the `CN105` port. You should disconnected the main power before install the module. 
Faulty handling may cause leakage of water, electric shock or fire! :warning:
***

## Parts

### Parts required to make a CN105 female connector

- PAP-05V-S CONN HOUSING PA 5POS 2MM WHITE 
    - Digi-Key Part Number 	455-1489-ND 
    - <https://www.digikey.com/product-detail/en/jst-sales-america-inc/PAP-05V-S/455-1489-ND/759977>
- SPHD-002T-P0.5  CONN TERM PHD CRIMP 24-28AWG TIN  
    - Digi-Key Part Number 	455-1313-1-ND
    - <https://www.digikey.com/product-detail/en/jst-sales-america-inc/SPHD-002T-P0.5/455-1313-1-ND/608809>
- JUMPER SPHD-001T-P0.5 X2 12" (pre-crimped alternative to 455-1313-1-ND connectors)
    - Digi-Key Part Number    455-3086-ND
    - <https://www.digikey.co.uk/product-detail/en/jst-sales-america-inc/APAPA22K305/455-3086-ND/6009462>

### Other part suggestions

- Premade pigtails
    - <https://m.aliexpress.com/item/1005002904897793.html> select 5P option
- ESP-8266 Wemos D1 Mini
    - <https://m.aliexpress.com/item/1005005121150737.htm>
***

## How to use:
 - Step 1: Flash the sketch with flash size include SPIFFS option.
 - Step 2: Connect to device AP with name HVAC-XXXXXXXXXXXX (XXXX... 12 character MAC address)
 - Step 3: You should be automatically redirected to the web portal or go to 192.168.4.1
 - Step 4: Set Wifi information, mqtt(optional), language and save & reboot. Fall back to AP mode if WiFi connection fails (AP password sets to default SSID name from step 2).
 - Step 5: Connect to the device I with local domain: HVAC-XXXXXXXXXXXX.local
 - Step 6: (optional): Set MQTT information for use with Home Assistant
 - Step 7: (optional): Set Login password to prevent unwanted access in SETUP->ADVANCE->Login Password
 - Step 8: (optional): Turn off heat mode or quiet mode in SETUP->UNIT

Nightly builds are available for select platforms via GitHub Actions. Go to [the workflow](https://github.com/dzungpv/mitsubishi2MQTT/actions/workflows/build.yml), select the latest build, then check the **Artifacts** section. 

***

## How to build code from source:
  - ESP IDF (Recomended): clone the project with --recursive tag: ```git clone https://github.com/dzungpv/mitsubishi2MQTT.git --recursive```, Install [ESP IDF 4.4.6 ](https://docs.espressif.com/projects/esp-idf/en/v4.4/esp32/get-started/index.html) set the target and run command: idf.py build
  - Arduino: Intall require libraries (name and path in platformio.ini), rename file main.cpp in main folder to main.ino, open it and build
  - Platformio: Install, open it and choose a variant to build

***

## MQTT topic use cases
The `topic` is: "mqtt_topic/mqtt_friendly_name"
- topic/power/set OFF
- topic/mode/set AUTO HEAT COOL DRY FAN_ONLY OFF ON
- topic/temp/set 16-31
- topic/remote_temp/set also called "room_temp", the implementation defined in "HeatPump" seems not work in some models
- topic/fan/set 1-4 AUTO QUIET
- topic/vane/set 1-5 SWING AUTO
- topic/wide-vane/set << < | > >>
- topic/settings
- topic/state
- topic/debug/packets
- topic/debug/packets/set on off
- topic/debug/logs
- topic/debug/logs/set on off
- topic/custom/send as example "fc 42 01 30 10 02 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 7b " see https://github.com/SwiCago/HeatPump/blob/master/src/HeatPump.h
- topic/system/set to control the device with commands: "restart": reboot the device, "factory": reset device to fatory state.
***

## Special thanks
SwiCago for the great libraries: https://github.com/SwiCago/HeatPump

Hadley in New Zealand. His blog post, describing baud rate and details of cn105, Raspberry Pi Python code:

<https://nicegear.co.nz/blog/hacking-a-mitsubishi-heat-pump-air-conditioner/>

Wayback machine link as the site no longer exists:
<https://web.archive.org/web/20171007190023/https://nicegear.co.nz/blog/hacking-a-mitsubishi-heat-pump-air-conditioner/>

***

## License

Licensed under the GNU Lesser General Public License.
https://www.gnu.org/licenses/lgpl-3.0.txt

***
If you like my work and use it, give a star.
