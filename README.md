# ESP32 JK-BMS Monitor

<!-- readme-axi type=esp32 -->

**English** · [Tiếng Việt](README.vi.md)

An **ESPHome** configuration for monitoring a **JK-BMS 24V 280Ah** battery with an **ESP32**
(ESP32-WROOM-32). The ESP32 connects to the JK-BMS lithium battery over **RS485/UART** and is
integrated with **Home Assistant** through ESPHome.

> 🇻🇳 Built and maintained by one person in Việt Nam — if it saves you time, please
> [support the project](#support-the-project).

## Contents

- [Features](#features)
- [Hardware](#hardware)
- [Quick start](#quick-start)
- [Configuration](#configuration)
- [Home Assistant](#home-assistant)
- [Troubleshooting](#troubleshooting)
- [Project layout](#project-layout)
- [Development](#development)

## Features

- **JK-BMS monitoring** of a 24V 280Ah pack (8-cell LiFePO4) over RS485/UART, updated every 5 seconds
- **Voltage, current, temperature and capacity** sensors, plus system diagnostics
- **Charge / discharge switches** controllable from Home Assistant
- **Home Assistant integration** through ESPHome
- **Optional BSSID / channel pinning** for a faster, more stable WiFi connection

## Hardware

### Specifications

- **Battery**: JK-BMS 24V 280Ah (8 cell LiFePO4)
- **Microcontroller**: ESP32-WROOM-32
- **Interface**: UART (GPIO16 TX, GPIO17 RX)
- **Baud rate**: 115200
- **Update interval**: every 5 seconds

### Wiring

```
JK-BMS RS485 ↔ TTL Converter ↔ ESP32
    A+ ────────── A+ ───────────
    B- ────────── B- ───────────
                 VCC ─────────── 3.3V
                 GND ─────────── GND
                 TXD ─────────── GPIO17 (RX)
                 RXD ─────────── GPIO16 (TX)
```

## Quick start

### 1. Prepare

- Install ESPHome
- Connect the ESP32 to the JK-BMS through an RS485/TTL converter
- **Security configuration**: copy `secrets.yaml.example` → `secrets.yaml` and fill in your real details
- **WiFi setup**: set the SSID and password, plus BSSID/channel if needed

### 2. Flash the firmware

```bash
# First flash
esphome run jk-bms-24v280ah.yaml

# Or use the script
esphome-flash.bat
```

### 3. View logs

```bash
# Realtime logs
esphome logs jk-bms-24v280ah.yaml

# Or use the script
esphome-logs.bat
```

## Configuration

### Basic WiFi

Only the SSID and password in `secrets.yaml` are needed:

```yaml
wifi_ssid: "Your_WiFi_Name"
wifi_password: "Your_WiFi_Password"
```

### Advanced WiFi (optional)

If several access points share the same SSID, or you want to prefer a specific AP:

```yaml
# Pin a specific BSSID and channel
wifi_bssid_t3: "aa:bb:cc:dd:ee:ff"
wifi_channel_t3: 11
```

Benefits of pinning the BSSID/channel:

- **Faster connect**: no need to scan every channel
- **Stable connection**: always connects to the intended AP
- **Better roaming**: avoids connecting to a distant AP or a weak signal

### Security

**IMPORTANT — protect your credentials:**

1. **NEVER** commit `secrets.yaml` to Git
2. Use **strong passwords** (at least 12 characters)
3. **Change the OTA password** from the default
4. Check `.gitignore` before committing

Setting up secrets:

```bash
# 1. Copy template
cp secrets.yaml.example secrets.yaml

# 2. Edit it with your real details
# 3. Check gitignore
git status  # secrets.yaml must NOT be listed

# 4. Generate a strong OTA password
openssl rand -base64 32
```

## Home Assistant

After a successful flash, the device appears in Home Assistant automatically through the ESPHome
integration. It provides:

### Sensors

- **Voltage**: total, per cell, min/max, delta
- **Current**: charge, discharge, power
- **Temperature**: sensor 1, 2, MOSFET
- **Capacity**: remaining, charge cycles
- **System**: WiFi signal, RAM, uptime

### Binary sensors

- Charging / discharging state
- Cell balancing
- Online connection

### Switches

- Charging on/off
- Discharging on/off

## Troubleshooting

### Common errors

1. **Cannot connect to the BMS**: check the RS485 wiring and the baud rate
2. **Unstable WiFi**: check the signal strength and the channel configuration
3. **Data does not update**: check the logs, restart the device

### Debug

```bash
# Detailed logs
esphome-logs.bat

# Erase the flash and reinstall
esphome-erase.bat
esphome-flash.bat
```

### Getting help

If you have a problem or a question, please open an
[issue](https://github.com/ngoviet/esp32-jk-bms-monitor/issues) on GitHub.

## Project layout

```
├── common.yaml              # Shared config for all ESP32s
├── jk-bms-24v280ah.yaml    # JK-BMS specific config
├── esphome-flash.bat       # Firmware flash script
├── esphome-erase.bat       # Flash erase script
├── esphome-logs.bat        # Log viewer script
└── README.md               # This document
```

## Development

### Contributing

1. Fork project
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

<!-- support:start -->
## Support the project

<p align="center">
  <img src="https://raw.githubusercontent.com/ngoviet/.github/main/assets/support/banner.svg" width="720" alt="Built and maintained by one person in Việt Nam">
</p>

<p align="center">
  <a href="https://buymeacoffee.com/ngoviet"><img src="https://raw.githubusercontent.com/ngoviet/.github/main/assets/support/btn-coffee.svg" height="56" alt="Buy me a coffee"></a>
  &nbsp;
  <a href="https://github.com/sponsors/ngoviet"><img src="https://raw.githubusercontent.com/ngoviet/.github/main/assets/support/btn-sponsors.svg" height="56" alt="GitHub Sponsors"></a>
</p>

<p align="center">
  <img src="https://raw.githubusercontent.com/ngoviet/.github/main/assets/support/vietqr-card.svg" width="300" alt="VietQR — MB Bank — NGO DUC VIET — 911051212">
</p>

<p align="center"><sub>🇻🇳 VietQR: scan with any Vietnamese banking app, transfer note <code>Ung ho esp32-jk-bms-monitor</code>. Thank you for keeping this project alive!</sub></p>
<p align="center"><sub>Crypto (BEP20 network): <code>0x57f07d44fb581cddc028a0c67d63a8cc05aa6caa</code> · USDT · USDC · BNB · ETH · BTC</sub></p>
<!-- support:end -->

## License

[MIT](LICENSE) © Ngô Đức Việt. See the [LICENSE](LICENSE) file for details.
