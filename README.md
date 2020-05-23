_I'm no longer working with localtuya-homeassistant. Please take a look at https://github.com/TradeFace/tuyamqtt which IMO is a better solution._


# localtuya-homeassistant

Local handling for Tuya Devices under Home-Assistant and Hassio, getting parameters from them (as Power Meters: Voltage, Current, Watt)

# How it works:

   1. Copy switch.py and the other 2 integration/platform files, and pytuya handler folder, to /custom_components/localtuya/ folder, inside /config folder (via Samba for HASSIO).
   
   2. Identify device_id, local_key and attribute numbers corresponding to current, current_consumption and voltage (by default I'm using 4,5 and 6). For me best way to do that is here: https://github.com/cwwilson08/tuyapi/blob/master/docs/SETUP.md

   3. Use this on your configuration.yaml file (filling proper numbers for attributes):
```
       switch:
         - platform: localtuya
           host: 192.168.1.251
           local_key: 1234567890abcdef
           device_id: abcdef1234567890abcdef
           protocol: 3.3
           current: 4
           current_consumption: 5
           voltage: 6
           switches:
             some_switch1:
               friendly_name: TUYA_LED
               id: 1
             switch2:
               friendly_name: TUYA_SW01
               id: 101
```
   NOTE: Values reported in attributes are already in (respectively) Amps, Watts and Volts.

   4. Use this declaration on your configuration.yaml file, for stating sensors that handle its attributes:
```   
       sensor:
         - platform: template
           sensors:
             tuya-sw01_voltage:
               value_template: >-
                 {{ states.switch.some_switch1.attributes.voltage }}
               unit_of_measurement: 'V' 
             tuya-sw01_current:
               value_template: >-    
                 {{ states.switch.some_switch1.attributes.current }}
               unit_of_measurement: 'A'      
             tuya-sw01_current_consumption:
               value_template: >-
                 {{ states.switch.some_switch1.attributes.current_consumption }}
               unit_of_measurement: 'W' 
```               
   5. If you got all the parameters ok you should get working switch and sensors feeding readouts to HA
   
   NOTE: You can do as changes as you want in scripts ant/or yaml files. But: You can't declare your "custom_component" as "tuya", tuya is a forbidden word from 0.88 version or so. So if you declare a switch.tuya, the embedded (cloud based) Tuya component will be load instead custom_component one.
   
   NOTE2: tuya-sw01, TUYA-SW01, is the name (friendly name), so you can name it as you want, regarding to change this word every where it appears. Really you can name the sensors not using the name of the original switch. But the recomendation is to do it, so you can know what sensor it is (to be more traceable).

# Thanks to:
mileperhour, for all this work (I just tweaked it a bit)

sean6541, for the working (standard) Python Handler for Tuya devices.

some-other-user, i really can't find now, (i will credit/thank him here), who published a partialy Python script that inspired this solution
   
rdelcorro, for last fixes of pytuya module break from HASSIO last updates: no module named 'pytuya' .
