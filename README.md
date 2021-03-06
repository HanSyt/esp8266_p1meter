# esp8266_p1meter

Software for the ESP2866 that sends P1 smart meter data to an mqtt broker (with OTA firmware updates)

## Connection of the P1 meter to the ESP8266

| ESP8266 Pin | P1 Pin |
| ----        | ----   |
| GND         | GND    |
| 3V3         | RTS    |
| DATA (RXD)  | D2     |

To connect my Landys and Gyr E350 (ZCF110), I used a 10K resistor connected between DATA (RXD) and RTS.
Many howto's mention RTS requires 5V (VIN) to activate the P1 port, but for me 3V3 suffices.

![RJ11 P1 connetor](http://gejanssen.com/howto/Slimme-meter-uitlezen/RJ11-pinout.png)



## Data Sent

All metrics are send to their own MQTT topic.
The nodemcu sends out to the following MQTT topics:

```
sensors/power/p1meter/consumption_low_tarif 2209397
sensors/power/p1meter/consumption_high_tarif 1964962
sensors/power/p1meter/actual_consumption 313
sensors/power/p1meter/instant_power_usage 313
sensors/power/p1meter/instant_power_current 1000
sensors/power/p1meter/gas_meter_m3 968922
sensors/power/p1meter/actual_tarif_group 2
sensors/power/p1meter/short_power_outages 3
sensors/power/p1meter/long_power_outages 1
sensors/power/p1meter/short_power_drops 0
sensors/power/p1meter/short_power_peaks 0
```

This fork has some extra topics for return delivery and dutch 3Phase system

## Home Assistant Configuration

I use this for home assistant `sensors.yaml`:

```
 - platform: mqtt
   name: P1 Consumption Low Tarif
   unit_of_measurement: 'kWh'
   state_topic: "sensors/power/p1meter/consumption_low_tarif"
   value_template: "{{ value|float / 1000 }}"
   icon: mdi:counter
   #
 - platform: mqtt
   name: P1 Delivery Low Tarif
   unit_of_measurement: 'kWh'
   state_topic: "sensors/power/p1meter/delivery_low_tarif"
   value_template: "{{ value|float / 1000 }}"
   icon: mdi:counter
   #
 - platform: mqtt
   name: P1 Consumption High Tarif
   unit_of_measurement: 'kWh'
   state_topic: "sensors/power/p1meter/consumption_high_tarif"
   value_template: "{{ value|float / 1000 }}"
   icon: mdi:counter
   #
 - platform: mqtt
   name: P1 Delivery High Tarif
   unit_of_measurement: 'kWh'
   state_topic: "sensors/power/p1meter/delivery_high_tarif"
   value_template: "{{ value|float / 1000 }}"
   icon: mdi:counter
   #
 - platform: mqtt
   name: P1 Actual Power Consumption
   unit_of_measurement: 'kW'
   state_topic: "sensors/power/p1meter/actual_consumption"
   value_template: "{{ value|float / 1000 }}"
   icon: mdi:speedometer
   #
 - platform: mqtt
   name: P1 Actual Power Delivery
   unit_of_measurement: 'kW'
   state_topic: "sensors/power/p1meter/actual_delivery"
   value_template: "{{ value|float / 1000 }}"
   icon: mdi:speedometer
   #
 - platform: mqtt
   name: P1 Instant Power Usage
   unit_of_measurement: 'kW'
   state_topic: "sensors/power/p1meter/instant_power_usage"
   value_template: "{{ value|float / 1000 }}"
   icon: mdi:speedometer
   #
 - platform: mqtt
   name: P1 L1 Instant Power Current
   unit_of_measurement: 'A'
   state_topic: "sensors/power/p1meter/l1_instant_power_current"
   value_template: "{{ value|float / 1000 }}"
   icon: mdi:speedometer
   #
 - platform: mqtt
   name: P1 L2 Instant Power Current
   unit_of_measurement: 'A'
   state_topic: "sensors/power/p1meter/l2_instant_power_current"
   value_template: "{{ value|float / 1000 }}"
   icon: mdi:speedometer
   #
 - platform: mqtt
   name: P1 L3 Instant Power Current
   unit_of_measurement: 'A'
   state_topic: "sensors/power/p1meter/l3_instant_power_current"
   value_template: "{{ value|float / 1000 }}"
   icon: mdi:speedometer
   #   
 - platform: mqtt
   name: P1 L1 Voltage
   unit_of_measurement: 'V'   
   state_topic: "sensors/power/p1meter/l1_voltage"
   icon: mdi:speedometer
   #
 - platform: mqtt
   name: P1 L2 Voltage
   unit_of_measurement: 'V'   
   state_topic: "sensors/power/p1meter/l2_voltage"
   icon: mdi:speedometer
   #
 - platform: mqtt
   name: P1 L3 Voltage
   unit_of_measurement: 'V'   
   state_topic: "sensors/power/p1meter/l3_voltage"
   icon: mdi:speedometer
   #
 - platform: mqtt
   name: P1 Gas Usage
   unit_of_measurement: 'm3'
   state_topic: "sensors/power/p1meter/gas_meter_m3"
   value_template: "{{ value|float / 1000 }}"
   icon: mdi:counter
   #
 - platform: mqtt
   name: P1 Actual Tarif Group
   state_topic: "sensors/power/p1meter/actual_tarif_group"
   #
 - platform: mqtt
   name: P1 Short Power Outages
   state_topic: "sensors/power/p1meter/short_power_outages"
   icon: mdi:exclamation
   #
 - platform: mqtt
   name: P1 Long Power Outages
   state_topic: "sensors/power/p1meter/long_power_outages"
   icon: mdi:exclamation
   #
 - platform: mqtt
   name: P1 Short Power Drops
   state_topic: "sensors/power/p1meter/short_power_drops"
   icon: mdi:exclamation
   #
 - platform: mqtt
   name: P1 Short Power Peaks
   state_topic: "sensors/power/p1meter/short_power_peaks"
   icon: mdi:exclamation
   #
```

The automations are yours to create.
And always remember that sending alerts in case of a power outtage only make sense when you own a UPS battery :)


## Thanks to

This sketch is mostly copied and pasted from several other projects.
Standing on the heads of giants, big thanks and great respect to the writers and/or creators of:

- https://github.com/jantenhove/P1-Meter-ESP8266
- https://github.com/neographikal/P1-Meter-ESP8266-MQTT
- http://gejanssen.com/howto/Slimme-meter-uitlezen/
- https://github.com/rroethof/p1reader/
- http://romix.macuser.nl/software.html
- http://blog.regout.info/category/slimmeter/
- http://domoticx.com/p1-poort-slimme-meter-hardware/
