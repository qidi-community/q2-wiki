# Displaying Toolhead and Host CPU in Temperature List

Add these temperature sensors to your printer.cfg to display additional temperature readings:

```ini
[temperature_sensor Host_CPU]
sensor_type: temperature_host
min_temp: 10
max_temp: 100

[temperature_sensor toolhead]
sensor_type: temperature_mcu
sensor_mcu: THR
min_temp: 0
max_temp: 100
```

## Installation

1. Add the configuration to your printer.cfg
2. Restart Klipper