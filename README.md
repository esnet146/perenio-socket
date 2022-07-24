# perenio-socket
Replacement the module WR2 with esp8285

Замена tuya модуля wr2 в розетке Perenio на esp-02s(esp8285) 

Модуль можно заказать на али https://a.aliexpress.com/_Dnqn3gn

Было:
![photo_5398096145688936565_x](https://user-images.githubusercontent.com/64173457/180664538-5d51b894-e19e-467b-a7cc-2b846606abc6.jpg)

Стало:
![photo_5399890071924096236_y](https://user-images.githubusercontent.com/64173457/180664547-dda255e4-87c4-4a99-aaee-682d2f316b51.jpg)

Код прошивки:
```
substitutions:
  board_name: "socket"

esphome:
  name: $board_name
  platform: ESP8266
  board: esp01_1m

  on_boot:
    - priority: -200.0
      then:
        - light.turn_on:
            id: led_$board_name
            brightness: 100%
            effect: Pulse

# disable logging
logger:
  baud_rate: 0

api:
  password: !secret passwordapi

ota:
  password: !secret passwordota


wifi:
  networks:
  - ssid: !secret wifi1
    password: !secret password1

web_server:
  port: 80
button:
  - platform: restart
    name: Reset.$board_name

output:
  - platform: esp8266_pwm
    id: led
    pin: GPIO01
    inverted: True 

light:
  - platform: monochromatic
    name: LED_$board_name
    output: led
    internal: true
    default_transition_length: 2s
    id: led_$board_name
    effects:
      - pulse:
          name: Pulse

binary_sensor:
  - platform: gpio
    pin:
      number: GPIO03 # кнопка
      mode: INPUT_PULLUP
      inverted: true
    name: key_$board_name
    internal: true
    on_press:
      - switch.toggle: relay

  - platform: status
    name: Status.$board_name
    id: status_id
    internal: true
    on_release:
      then:
        - light.turn_on:
            id: led_$board_name
            brightness: 100%
            effect: Pulse
    on_press:
      then:
        - light.turn_off: led_$board_name

switch:
  - platform: gpio
    name: relay_$board_name
    pin: GPIO04
    id: relay
    restore_mode: ALWAYS_OFF
    on_turn_on:
    - light.turn_on: led_$board_name
    on_turn_off:
    - light.turn_off: led_$board_name


sensor:
  - platform: wifi_signal
    name: WiFi_Signal.$board_name
  - platform: uptime
    name: Uptime_Sensor_$board_name
    id: uptime_sensor

  - platform: hlw8012
    sel_pin: 
      number: GPIO14
      inverted: True
    cf_pin: GPIO13
    cf1_pin: GPIO05
    current:
      name: Current_$board_name
      filters:
       - multiply: 1.0
    voltage:
      name: Voltage_$board_name
    power:
      name: Power_$board_name
    energy:
      name: Energy_$board_name
    update_interval: 5s
    voltage_divider: 1700 #1710 #1760
    current_resistor: 0.001 #0.00095
    change_mode_every: 3
    model: BL0937


text_sensor:
  - platform: wifi_info
    ip_address:
      name: ESP IP Address.$board_name
    ssid:
      name: ESP Connected SSID.$board_name
    bssid:
      name: ESP Connected BSSID.$board_name
    mac_address:
      name: ESP Mac Wifi Address.$board_name
```
