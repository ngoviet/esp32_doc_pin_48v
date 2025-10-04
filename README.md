# ESP32 BMS Monitor with Fan Control

ESPHome configuration for monitoring JK-BMS battery management system with automatic fan control based on temperature from Home Assistant.

## 🔋 Features

- **JK-BMS Monitoring**: Complete monitoring of 16S LiFePO4 battery system via Modbus
- **200+ Sensors**: Cell voltages, temperatures, current, power, capacity, cycles, etc.
- **Automatic Fan Control**: Temperature-based fan control with hysteresis logic
- **Home Assistant Integration**: Native ESPHome API integration
- **Dual Board Support**: ESP32 and ESP32-C3 SuperMini configurations
- **Secure Configuration**: WiFi credentials in separate secrets file

## 📋 Hardware Requirements

### ESP32 Configuration
- **Board**: ESP32 Dev Module
- **UART Pins**: GPIO16 (TX), GPIO17 (RX) 
- **Fan Pin**: GPIO25 (PWM)
- **Framework**: ESP-IDF

### ESP32-C3 SuperMini Configuration  
- **Board**: ESP32-C3-DevKitM-1
- **UART Pins**: GPIO21 (TX), GPIO20 (RX)
- **Fan Pin**: GPIO10
- **Framework**: ESP-IDF

### JK-BMS Connection
- **Protocol**: Modbus RTU over UART
- **Baud Rate**: 115200
- **Device Address**: 0x09
- **Update Interval**: 5-10s

## 🚀 Quick Start

### 1. Clone Repository
```bash
git clone https://github.com/ngoviet/esp32_doc_pin_48v.git
cd esp32_doc_pin_48v
```

### 2. Setup Secrets
Copy and edit the secrets file:
```bash
cp secrets.yaml.example secrets.yaml
# Edit secrets.yaml with your WiFi credentials
```

### 3. Flash Firmware
```bash
# For ESP32-C3
esphome run esp32-c3-bms-monitor.yaml

# Or use batch files
flash-c3.bat  # Flash via USB
log.bat       # View logs
```

## 📁 File Structure

```
├── esp32-c3-bms-monitor.yaml    # Main ESP32-C3 configuration
├── base-setup.yaml              # Base ESPHome components
├── fan-control.yaml             # Fan control logic
├── secrets.yaml                 # WiFi credentials (gitignored)
├── flash-c3.bat                 # Flash script
├── log.bat                      # Log viewer script
└── .gitignore                   # Git ignore file
```

## 🌡️ Fan Control Logic

The fan system automatically controls cooling based on temperature from Home Assistant:

### Auto Mode (Default)
- **Turn ON**: When temperature ≥ T_HIGH (default: 42°C)
- **Turn OFF**: When temperature < T_LOW (default: 41°C)  
- **Hysteresis**: Prevents rapid on/off cycling

### Manual Mode
- Override automatic control
- Direct on/off switch control

### Temperature Source
- **Primary**: `sensor.device_h240909079_device_temperature` from Home Assistant
- **Fallback**: None (fan turns OFF if no data for safety)

## 🔧 Configuration

### Fan Settings
```yaml
substitutions:
  fan_pin: GPIO10                    # Fan control pin
  FAN_THR_HI_DEFAULT: "42.0"        # Auto ON threshold
  FAN_THR_LO_DEFAULT: "41.0"        # Auto OFF threshold
```

### BMS Settings
```yaml
substitutions:
  device_addr: "9"                   # BMS Modbus address
  tx_pin: GPIO21                     # UART TX pin  
  rx_pin: GPIO20                     # UART RX pin
  modbus_controller_update_interval: "10s"
```

### WiFi Setup (secrets.yaml)
```yaml
wifi_ssid: "Your_WiFi_Name"
wifi_password: "Your_WiFi_Password"
wifi_bssid: "aa:bb:cc:dd:ee:ff"    # Optional: specific AP
wifi_channel: 11                    # Optional: specific channel
```

## 📊 Available Sensors

### Battery Monitoring
- Cell voltages (1-16): Individual cell voltage monitoring
- Pack voltage: Total battery pack voltage
- Current: Charging/discharging current with direction
- Power: Real-time power consumption/generation
- SOC: State of Charge percentage
- SOH: State of Health percentage

### Temperature Monitoring  
- MOSFET temperature: Power switching components
- Battery temperatures: Multiple temperature probes
- Ambient temperatures: Environmental monitoring

### Protection & Status
- Overvoltage/undervoltage protection settings
- Overcurrent protection settings  
- Temperature protection limits
- Balancing status and current
- Charging/discharging status
- Alarm conditions and error codes

## 🔗 Home Assistant Integration

### Entities Created
- **200+ sensors**: All BMS parameters
- **Fan controls**: Auto/manual modes, thresholds
- **Binary sensors**: Charging, discharging, balancing status
- **Diagnostics**: WiFi signal, IP address, device info

### Dashboard Cards
```yaml
# Example fan control card
type: entities
entities:
  - entity: switch.fan_auto_mode
  - entity: switch.fan_manual_control  
  - entity: number.fan_t_high_c
  - entity: number.fan_t_low_c
  - entity: sensor.temperature_sensor_1
```

## 🛡️ Security Features

- **Secrets Management**: WiFi credentials stored separately
- **Git Protection**: `.gitignore` prevents credential exposure
- **OTA Protection**: Optional password protection for updates
- **API Encryption**: Optional API communication encryption

## 🐛 Troubleshooting

### Common Issues

**CRC Check Failed**
```
[W][modbus:135]: Modbus CRC Check failed! 6C0!=603
```
- Normal occasional errors due to electrical noise
- Does not affect functionality if data is still received
- Consider adding `send_wait_time: 50ms` to reduce frequency

**No Temperature Data**
```
[W][fan]: No temperature data available, turning fan OFF
```
- Check Home Assistant entity: `sensor.device_h240909079_device_temperature`
- Verify ESPHome can connect to Home Assistant
- Fan will safely turn OFF until data is available

**Compilation Errors**
- Ensure all `id:` references match between files
- Check YAML indentation (spaces, not tabs)
- Verify pin assignments for your specific board

### Debug Commands
```bash
# Check configuration
esphome config esp32-c3-bms-monitor.yaml

# View logs
esphome logs esp32-c3-bms-monitor.yaml --device 192.168.x.x

# Clean build
esphome clean esp32-c3-bms-monitor.yaml
```

## 📝 License

This project is open source. Feel free to modify and distribute.

## 🤝 Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📞 Support

- **Issues**: Open an issue on GitHub
- **Email**: ngoducvietds@gmail.com
- **ESPHome Docs**: https://esphome.io/

---

**⚡ Built with ESPHome for reliable battery monitoring and smart fan control**