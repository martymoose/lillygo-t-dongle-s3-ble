# LilyGo T-Dongle S3 BLE Proxy

A complete ESPHome configuration for the LilyGo T-Dongle S3 board with ST7789 LCD display, acting as a Bluetooth Low Energy (BLE) proxy for Home Assistant.

## Features

- **BLE Scanning**: Continuously scans for Bluetooth Low Energy devices
- **Real-time Display**: ST7789 240x280 LCD with live BLE device tracking
- **WiFi Connectivity**: Automatic WiFi with fallback hotspot
- **Home Assistant Integration**: Native integration via ESPHome
- **OTA Updates**: Over-the-air firmware updates
- **Device Tracking**: Monitor up to 5 BLE devices with signal strength
- **Time Sync**: Automatic time synchronization with Home Assistant

## Hardware

### LilyGo T-Dongle S3 Specifications
- **Microcontroller**: ESP32-S3 (Dual-core, 240 MHz)
- **Flash Memory**: 8 MB
- **RAM**: 8 MB
- **Dimensions**: Compact USB dongle form factor
- **Power**: USB-C (5V)

### Display
- **Type**: ST7789V TFT LCD
- **Resolution**: 240x280 pixels
- **Color**: 16-bit RGB565
- **Interface**: SPI

### Pin Configuration
```
SPI Bus:
  CLK (Clock):   GPIO12
  MOSI (Data):   GPIO11

Display:
  CS (Chip Select):  GPIO10
  DC (Data/Command): GPIO13
  RST (Reset):       GPIO9
  BL (Backlight):    GPIO14
```

## Quick Start

### 1. Create Secrets File

Create `secrets.yaml` in your ESPHome configuration directory:

```yaml
wifi_ssid: "Your WiFi Network"
wifi_password: "Your WiFi Password"
ota_password: "your_secure_ota_password"
api_key: "auto_generated_api_key_from_esphome"
```

### 2. Add Configuration

Copy `esphome-config.yaml` to your ESPHome device configuration or manually add the configuration to your ESPHome dashboard.

### 3. Flash Device

1. Connect LilyGo T-Dongle S3 via USB-C
2. In ESPHome dashboard, click **Install**
3. Select **Plug into this computer**
4. Choose the device port
5. Wait for compilation and flashing (3-5 minutes)

### 4. Verify in Home Assistant

After flashing, the device will appear in Home Assistant under **Settings → Devices & Services → ESPHome**.

## Configuration Guide

### WiFi Configuration

```yaml
wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password
  
  # Fallback hotspot for initial setup
  ap:
    ssid: "LilyGo-T-Dongle-S3"
    password: "12345678"
```

**Options:**
- `ssid`: Your WiFi network name
- `password`: Your WiFi password
- `ap.ssid`: Fallback hotspot name
- `ap.password`: Fallback hotspot password

### OTA (Over-the-Air) Updates

```yaml
ota:
  password: !secret ota_password
```

Enables wireless firmware updates without needing USB connection.

### Logging

```yaml
logger:
  level: DEBUG
```

**Log Levels:**
- `DEBUG`: Verbose output (default, useful for troubleshooting)
- `INFO`: Important messages only
- `WARNING`: Only warnings and errors
- `ERROR`: Only errors

### API Configuration

```yaml
api:
  encryption:
    key: !secret api_key
```

Secures communication between device and Home Assistant.

### Time Synchronization

```yaml
time:
  - platform: homeassistant
    id: homeassistant_time
```

Syncs device time with Home Assistant for accurate timestamps.

### SPI Bus (for LCD Display)

```yaml
spi:
  clk_pin: GPIO12      # Clock
  mosi_pin: GPIO11     # Data (MOSI = Master Out Slave In)
```

### ST7789 Display Configuration

```yaml
display:
  - platform: st7789v
    id: lcd_display
    cs_pin: GPIO10          # Chip Select
    dc_pin: GPIO13          # Data/Command
    reset_pin: GPIO9        # Reset
    backlight_pin: GPIO14   # Backlight PWM (optional)
    rotation: 0             # 0, 90, 180, 270 degrees
    width: 240
    height: 280
    lambda: |-
      # Custom display rendering code
```

**Display Options:**
- `rotation`: Screen orientation (0, 90, 180, 270)
- `invert_colors`: Set to `true` if colors are inverted
- `brightness`: 0.0-1.0 (if backlight_pin is set)

### Fonts

```yaml
font:
  - file: "gfonts://Roboto"
    id: font_small
    size: 12
  - file: "gfonts://Roboto"
    id: font_medium
    size: 16
  - file: "gfonts://Roboto"
    id: font_large
    size: 22
```

**Available Fonts:**
- Google Fonts: `gfonts://FontName`
- Local files: `/path/to/font.ttf`
- System fonts: `mdi:` for Material Design Icons

### BLE Scanner Configuration

```yaml
esp32_ble_tracker:
  scan_parameters:
    interval: 1.1s      # Time between scan starts
    window: 1.1s        # Scan duration
    duration: 30s       # Total scan time before restart
    active: true        # Active scanning (requests device info)
    continuous: true    # Continuous scanning
```

**Tuning Scan Performance:**
- **Lower interval/window**: More power consumption, faster detection
- **Higher interval/window**: Less power consumption, slower detection
- **interval >= window**: Always true, interval is time between scans

### Text Sensors (BLE Device Names)

```yaml
text_sensor:
  - platform: template
    name: "BLE Device 1"
    id: ble_device_1
    icon: mdi:bluetooth
    update_interval: never  # Update via lambda only
```

Add more devices (2-5) by duplicating and incrementing the ID.

### Numeric Sensors

```yaml
sensor:
  - platform: template
    name: "BLE Device Count"
    id: ble_device_count
    unit_of_measurement: "devices"
    icon: mdi:counter
    update_interval: 10s
    
  - platform: template
    name: "BLE Signal Strength"
    id: ble_signal_strength
    unit_of_measurement: "dBm"
    icon: mdi:signal-variant
    update_interval: 5s
```

## Display Customization

### Changing Colors

Colors are in RGB format: `Color(red, green, blue)` where each value is 0-255.

```yaml
# Common Colors
Color(255, 0, 0)      # Red
Color(0, 255, 0)      # Green
Color(0, 0, 255)      # Blue
Color(255, 255, 255)  # White
Color(0, 0, 0)        # Black
Color(255, 255, 0)    # Yellow
Color(255, 165, 0)    # Orange
Color(255, 192, 203)  # Pink
```

### Drawing Functions

```lambda
// Clear screen
it.fill(Color(0, 0, 0));

// Draw text
it.printf(x, y, font_id, color, TextAlign::TOP_LEFT, "Text: %s", variable.c_str());

// Draw rectangle (filled)
it.filled_rectangle(x, y, width, height, color);

// Draw rectangle (outline)
it.rectangle(x, y, width, height, color);

// Draw line
it.line(x1, y1, x2, y2, color);

// Draw circle
it.circle(x, y, radius, color);
```

## Sensor Examples

### Get WiFi Signal Strength

```yaml
sensor:
  - platform: wifi_signal
    name: "WiFi Signal Strength"
    update_interval: 30s
```

### Get Device Uptime

```yaml
sensor:
  - platform: uptime
    name: "Device Uptime"
```

### Temperature Sensor (if available)

```yaml
sensor:
  - platform: adc
    pin: GPIO3
    name: "Internal Temperature"
    unit_of_measurement: "°C"
    update_interval: 60s
```

## Home Assistant Integration

### Automation Example: Alert on Device Detected

```yaml
automation:
  - alias: "BLE Device Detected"
    trigger:
      platform: numeric_state
      entity_id: sensor.ble_device_count
      above: 0
    action:
      service: notify.notify
      data:
        message: "BLE device detected! Count: {{ states('sensor.ble_device_count') }}"
```

### Template Sensor: Device Status

```yaml
template:
  - sensor:
      - name: "BLE Status"
        state: >
          {% if states('sensor.ble_device_count') | int > 0 %}
            Active
          {% else %}
            Idle
          {% endif %}
```

### Script: Check Device Activity

```yaml
script:
  check_ble_activity:
    sequence:
      - service: homeassistant.update_entity
        target:
          entity_id: sensor.ble_device_count
```

## Troubleshooting

### Display Not Showing

1. Check GPIO pin connections (10, 13, 9, 14)
2. Verify SPI pins (12, 11)
3. Check backlight pin voltage
4. Review logs: `logger: level: DEBUG`

### No BLE Devices Detected

1. Ensure BLE devices are powered on and advertising
2. Verify scan parameters in `esp32_ble_tracker`
3. Check ESPHome logs for errors
4. Try reducing scan interval for more frequent scans

### WiFi Connection Issues

1. Verify SSID and password in `secrets.yaml`
2. Check WiFi signal strength
3. Use fallback hotspot to manually configure
4. Reset device and reconfigure

### Compilation Errors

1. Verify all GPIO pins are correct
2. Check YAML syntax (indentation, colons)
3. Ensure fonts are available online
4. Clear ESPHome cache: Delete `.esphome` directory

### Device Slow/Laggy

1. Reduce `logger.level` to `WARNING` or `ERROR`
2. Increase BLE `scan_parameters.interval`
3. Decrease `update_interval` for sensors
4. Reduce number of tracked devices

## Advanced Configuration

### Custom Scan Duration

For more aggressive scanning (higher power consumption):

```yaml
esp32_ble_tracker:
  scan_parameters:
    interval: 0.5s    # Very frequent scans
    window: 0.5s
    duration: 60s     # Longer scan duration
    active: true
    continuous: true
```

### Low Power Mode

```yaml
esp32_ble_tracker:
  scan_parameters:
    interval: 5s      # Less frequent
    window: 3s        # Shorter scan
    duration: 30s
    active: false     # Passive scanning uses less power
    continuous: true
```

### Multiple BLE Tracker Configurations

```yaml
esp32_ble_tracker:
  scan_parameters:
    interval: 1.1s
    window: 1.1s
```

### Custom Fonts from File

```yaml
font:
  - file: "/config/fonts/custom_font.ttf"
    id: custom_font
    size: 16
```

## Common BLE Service UUIDs

```yaml
# Battery Service
0000180F-0000-1000-8000-00805F9B34FB

# Device Information Service
0000180A-0000-1000-8000-00805F9B34FB

# Environmental Sensing Service
0000181A-0000-1000-8000-00805F9B34FB

# Health Thermometer Service
0000180E-0000-1000-8000-00805F9B34FB

# Heart Rate Service
0000180D-0000-1000-8000-00805F9B34FB
```

## Resources

- [ESPHome Documentation](https://esphome.io/)
- [ESP32-S3 Datasheet](https://www.espressif.com/products/socs/esp32-s3/resources)
- [Home Assistant ESPHome Integration](https://www.home-assistant.io/integrations/esphome/)
- [ST7789 Display Documentation](https://www.waveshare.com/wiki/1.47inch_LCD_Module)

## License

This configuration is provided as-is for personal and home automation use.

## Support

For issues or questions:
1. Check the [Troubleshooting](#troubleshooting) section
2. Review ESPHome logs in the dashboard
3. Check Home Assistant logs
4. Visit [ESPHome Forums](https://github.com/esphome/esphome/issues)
