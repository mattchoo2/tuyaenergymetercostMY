# tuyaenergymetercostMY
Tuya Energy Meter - Energy &amp; Cost calculation (Malaysia)


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
