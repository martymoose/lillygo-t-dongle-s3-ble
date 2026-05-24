# Quick Start Guide

Get your LilyGo T-Dongle S3 BLE Proxy running in 5 minutes!

## Prerequisites

- Home Assistant with ESPHome add-on
- LilyGo T-Dongle S3 hardware
- USB cable
- WiFi network

## 1. Create Secrets File

In Home Assistant, go to **Settings → Add-ons → ESPHome → Files**.

Create `secrets.yaml`:

```yaml
wifi_ssid: "YourNetworkName"
wifi_password: "YourWiFiPassword"
ota_password: "change_me_to_something_secure"
api_key: "abcd1234efgh5678ijkl9012mnop3456"
```

> **Get API Key**: In ESPHome dashboard, click "New Device" → "Create Configuration" and copy the auto-generated key.

## 2. Add Configuration

In ESPHome dashboard, click **"Create new device"** and paste the `esphome-config.yaml` content. Name it `lillygo-ble-proxy`.

## 3. Flash Device

1. Click the device name
2. Click **"Install"**
3. Select **"Plug into this computer"**
4. Connect LilyGo T-Dongle S3 via USB
5. Select the device port
6. Wait for compilation and flashing (3-5 minutes)

## 4. Connect to WiFi

The device will reboot and either:

**Option A - Auto Connect**:
- Automatically connects to your WiFi
- OLED displays "Connected"

**Option B - Use Fallback Hotspot**:
1. Find "LilyGo-T-Dongle-S3" WiFi network
2. Connect with password: `12345678`
3. Open browser to device IP
4. Configure WiFi

## 5. Verify in Home Assistant

Go to **Settings → Devices & Services → ESPHome**.

You should see new device with sensors:
- `ble_device_count` (number of devices detected)
- `ble_signal_strength` (signal strength in dBm)
- `wifi_info` (connection status)

## What Now?

- **View OLED Display**: Shows real-time BLE device count, WiFi status, and signal strength
- **Check Logs**: Click device → "Logs" to see BLE devices being detected
- **Create Automations**: Use the sensors in Home Assistant automations
- **Customize**: See [WIKI.md](docs/WIKI.md) for advanced configuration

## Troubleshooting

**Display shows "Disconnected"**:
- Check WiFi password in `secrets.yaml`
- Use fallback hotspot to reconfigure

**No BLE devices detected**:
- Make sure BLE devices are powered on and advertising
- Check "Logs" tab in ESPHome for details

**Device doesn't appear in Home Assistant**:
- Restart ESPHome integration: Settings → Add-ons → ESPHome → Reload
- Check Home Assistant logs for errors

## Next Steps

1. **Read Full Documentation**: See [README.md](README.md) and [WIKI.md](docs/WIKI.md)
2. **Customize Display**: Modify the OLED display layout
3. **Optimize Scanning**: Adjust BLE scan parameters for your needs
4. **Create Automations**: Track specific BLE devices in Home Assistant

---

**Need Help?** Check the [Troubleshooting](docs/WIKI.md#troubleshooting) section or review logs in ESPHome dashboard.
