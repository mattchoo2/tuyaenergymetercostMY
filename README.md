
# tuyaenergymetercostMY
Tuya Energy Meter - Energy &amp; Cost calculation (Malaysia)

## Prior Setup

 1. Tuya Meter hardware installation
 2. Add device in homeassistant via Tuya (Cloud)
 3. You will find 3 ready entities on your added device: 
	 - Meter Current (A)
	 - Meter Power (W)
	 - Meter Voltage (V)
 
 ## New Entities Setup
 ### Power Consumption (kWh)
 4. Setting > Devices & services > Helpers > Create Helper
 5. Select "Integral sensor"
  ![kwh](https://github.com/mattchoo2/tuyaenergymetercostMY/blob/main/helper.png)
 6. Metric = k
 7. Input sensor = Meter Power (W)
 8. Integration method = Trapezoidal
 9. Percision = 2 

 ### Power Consumption (kWh) per day or per month
 10. Setting > Devices & services > Helpers > Create Helper
 11. Select "Utility Meter"
  ![kwhdm](https://github.com/mattchoo2/tuyaenergymetercostMY/blob/main/period%20meter.png)
 12. Input sensor = choose the new kWh entity
 13. Reset cycle = day or month accordingly
 14. Periodically resetting = ON


 ### Power Cost (MYR) per day or per month

```yaml
sensor:
  - platform: template
    sensors:
      daily_energy_cost:
        unique_id: "daily_energy_cost_sensor"
        friendly_name: "Daily Energy Cost"
        unit_of_measurement: 'RM'
        value_template: >
          {% set kwh = states('sensor.utility_energy_meter_daily') | float %}
          {% if kwh <= 200 %}
            {{ (kwh * 0.218) | round(2) }}
          {% elif kwh <= 300 %}
            {{ ((200 * 0.218) + ((kwh - 200) * 0.334)) | round(2) }}
          {% elif kwh <= 600 %}
            {{ ((200 * 0.218) + (100 * 0.334) + ((kwh - 300) * 0.516)) | round(2) }}
          {% elif kwh <= 900 %}
            {{ ((200 * 0.218) + (100 * 0.334) + (300 * 0.516) + ((kwh - 600) * 0.546)) | round(2) }}
          {% else %}
            {{ ((200 * 0.218) + (100 * 0.334) + (300 * 0.516) + (300 * 0.546) + ((kwh - 900) * 0.571)) | round(2) }}
          {% endif %}
      monthly_energy_cost:
        unique_id: "monthly_energy_cost_sensor"
        friendly_name: "Monthly Energy Cost"
        unit_of_measurement: 'RM'
        value_template: >
          {% set kwh = states('sensor.utility_energy_meter_monthly') | float %}
          {% if kwh <= 200 %}
            {{ (kwh * 0.218) | round(2) }}
          {% elif kwh <= 300 %}
            {{ ((200 * 0.218) + ((kwh - 200) * 0.334)) | round(2) }}
          {% elif kwh <= 600 %}
            {{ ((200 * 0.218) + (100 * 0.334) + ((kwh - 300) * 0.516)) | round(2) }}
          {% elif kwh <= 900 %}
            {{ ((200 * 0.218) + (100 * 0.334) + (300 * 0.516) + ((kwh - 600) * 0.546)) | round(2) }}
          {% else %}
            {{ ((200 * 0.218) + (100 * 0.334) + (300 * 0.516) + (300 * 0.546) + ((kwh - 900) * 0.571)) | round(2) }}
          {% endif %}
```

**Explanation:**

- **sensor.utility_energy_meter_daily** 
- **sensor.utility_energy_meter_monthly** 
- Replace these two entities in yaml script with your own new entities name created in earlier steps
