
# tuyaenergymetercostMY
Tuya Energy Meter - Energy &amp; Cost calculation (Malaysia) aka Tenaga/ TNB/ MyTNB electricity bill calculation

## Prior Setup

 1. Tuya Meter hardware installation
 2. Add device in homeassistant via Tuya (Cloud)
 3. You will find 3 ready entities on your added device: 
	 - Meter Current (A)
	 - Meter Power (W)
	 - Meter Voltage (V)
 
 ## New Entities Setup
 ### Power Consumption (kWh)
 1. Setting > Devices & services > Helpers > Create Helper
 2. Select "Integral sensor"<br>
  ![kwh](https://github.com/mattchoo2/tuyaenergymetercostMY/blob/main/helper.png)
 3. Metric = k
 4. Input sensor = Meter Power (W)
 5. Integration method = Trapezoidal
 6. Percision = 2 

 ### Power Consumption (kWh) per day or per month
 1. Setting > Devices & services > Helpers > Create Helper
 2. Select "Utility Meter"<br>
  ![kwhdm](https://github.com/mattchoo2/tuyaenergymetercostMY/blob/main/period%20meter.png)
 3. Input sensor = choose the new kWh entity
 4. Reset cycle = day or month accordingly
 5. Periodically resetting = ON
 6. Offset = you can use this to match data to your energy bill cycle (i keep to zero for ease of reference)


 ### Power Cost (MYR) per day or per month
 To calculate your energy costs in Home Assistant based on Malaysia's tiered tariff structure, you can create a template sensor that applies the appropriate rates to your "Tuya Power" entity's cumulative kWh usage. Here's how you can set this up:
 1. Access Your Home Assistant Configuration:
    - Navigate to your Home Assistant configuration directory, typically found at /config.
 2. Define a Template Sensor:
    - Open your configuration.yaml file or a separate sensors file if you have one.
    - Add the following template sensor configuration:
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

## Bar Chart Creation
To display a bar chart in Home Assistant where each bar represents a month's total energy usage and cost, you can utilize the **ApexCharts Card**, a custom Lovelace card that offers advanced graphing capabilities. Here's how to set it up:

**1. Install the ApexCharts Card**:

If you haven't already installed the ApexCharts Card, you can do so via HACS (Home Assistant Community Store):

-   Navigate to **HACS** in your Home Assistant sidebar.
    
-   Search for **ApexCharts Card** and install it.
    
-   After installation, add the resource to your Lovelace configuration:
    
 ```yaml
    resources:
      - url: /hacsfiles/apexcharts-card/apexcharts-card.js
        type: module 
 ```

    

**2. Configure the ApexCharts Card**:

Add the following configuration to your Lovelace dashboard to display a bar chart of monthly energy usage and cost:


<!--stackedit_data:
eyJoaXN0b3J5IjpbNDAxMzc0ODEwLDE5MDk3NTEyMDldfQ==
-->