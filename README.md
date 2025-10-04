# ESP32 Giám Sát BMS với Điều Khiển Quạt Tự Động

Cấu hình ESPHome để giám sát hệ thống quản lý pin JK-BMS với điều khiển quạt tự động dựa trên nhiệt độ từ Home Assistant.

## 🔋 Tính Năng

- **Giám Sát JK-BMS**: Giám sát toàn diện hệ thống pin LiFePO4 16S qua Modbus
- **200+ Cảm Biến**: Điện áp cell, nhiệt độ, dòng điện, công suất, dung lượng, chu kỳ...
- **Điều Khiển Quạt Tự Động**: Điều khiển quạt theo nhiệt độ với logic hysteresis
- **Tích Hợp Home Assistant**: Kết nối trực tiếp qua ESPHome API
- **Hỗ Trợ Đa Board**: Cấu hình cho ESP32 và ESP32-C3 SuperMini
- **Cấu Hình Bảo Mật**: Thông tin WiFi được lưu trong file riêng biệt

## 📋 Yêu Cầu Phần Cứng

### Cấu Hình ESP32
- **Board**: ESP32 Dev Module
- **Chân UART**: GPIO16 (TX), GPIO17 (RX) 
- **Chân Quạt**: GPIO25 (PWM)
- **Framework**: ESP-IDF

### Cấu Hình ESP32-C3 SuperMini  
- **Board**: ESP32-C3-DevKitM-1
- **Chân UART**: GPIO21 (TX), GPIO20 (RX)
- **Chân Quạt**: GPIO10
- **Framework**: ESP-IDF

### Kết Nối JK-BMS
- **Giao Thức**: Modbus RTU qua UART
- **Tốc Độ**: 115200 baud
- **Địa Chỉ Thiết Bị**: 0x09
- **Tần Suất Cập Nhật**: 5-10 giây

## 🚀 Bắt Đầu Nhanh

### 1. Tải Repository
```bash
git clone https://github.com/ngoviet/esp32_doc_pin_48v.git
cd esp32_doc_pin_48v
```

### 2. Thiết Lập Secrets
Sao chép và chỉnh sửa file secrets:
```bash
cp secrets.yaml.example secrets.yaml
# Chỉnh sửa secrets.yaml với thông tin WiFi của bạn
```

### 3. Flash Firmware
```bash
# Cho ESP32-C3
esphome run esp32-c3-bms-monitor.yaml

# Hoặc dùng file batch
flash-c3.bat  # Flash qua USB
log.bat       # Xem logs
```

## 📁 Cấu Trúc File

```
├── esp32-c3-bms-monitor.yaml    # Cấu hình chính ESP32-C3
├── base-setup.yaml              # Các thành phần ESPHome cơ bản
├── fan-control.yaml             # Logic điều khiển quạt
├── secrets.yaml                 # Thông tin WiFi (đã gitignore)
├── flash-c3.bat                 # Script flash
├── log.bat                      # Script xem log
└── .gitignore                   # File git ignore
```

## 🌡️ Logic Điều Khiển Quạt

Hệ thống quạt tự động điều khiển làm mát dựa trên nhiệt độ từ Home Assistant:

### Chế Độ Tự Động (Mặc Định)
- **Bật Quạt**: Khi nhiệt độ ≥ T_HIGH (mặc định: 42°C)
- **Tắt Quạt**: Khi nhiệt độ < T_LOW (mặc định: 41°C)  
- **Hysteresis**: Ngăn chặn bật/tắt liên tục

### Chế Độ Thủ Công
- Ghi đè điều khiển tự động
- Điều khiển bật/tắt trực tiếp

### Nguồn Nhiệt Độ
- **Chính**: `sensor.device_h240909079_device_temperature` từ Home Assistant
- **Dự Phòng**: Không có (quạt tắt nếu không có dữ liệu để đảm bảo an toàn)

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
  device_addr: "9"                   # Địa chỉ Modbus BMS
  tx_pin: GPIO21                     # Chân TX UART  
  rx_pin: GPIO20                     # Chân RX UART
  modbus_controller_update_interval: "10s"
```

### Thiết Lập WiFi (secrets.yaml)
```yaml
wifi_ssid: "Ten_WiFi_Cua_Ban"
wifi_password: "Mat_Khau_WiFi_Cua_Ban"
wifi_bssid: "aa:bb:cc:dd:ee:ff"    # Tùy chọn: AP cụ thể
wifi_channel: 11                    # Tùy chọn: kênh cụ thể
```

## 📊 Các Cảm Biến Có Sẵn

### Giám Sát Pin
- Điện áp tế bào (1-16): Giám sát điện áp từng tế bào riêng lẻ
- Điện áp gói pin: Tổng điện áp của gói pin
- Dòng điện: Dòng sạc/xả có hướng
- Công suất: Tiêu thụ/phát điện thời gian thực
- SOC: Phần trăm mức sạc
- SOH: Phần trăm tình trạng sức khỏe pin

### Giám Sát Nhiệt Độ  
- Nhiệt độ MOSFET: Linh kiện chuyển mạch công suất
- Nhiệt độ pin: Nhiều đầu dò nhiệt độ
- Nhiệt độ môi trường: Giám sát môi trường

### Bảo Vệ & Trạng Thái
- Cài đặt bảo vệ quá áp/thiếu áp
- Cài đặt bảo vệ quá dòng
- Giới hạn bảo vệ nhiệt độ
- Trạng thái và dòng cân bằng
- Trạng thái sạc/xả
- Điều kiện báo động và mã lỗi

## 🔗 Tích Hợp Home Assistant

### Thực Thể Được Tạo
- **200+ cảm biến**: Tất cả thông số BMS
- **Điều khiển quạt**: Chế độ tự động/thủ công, ngưỡng nhiệt
- **Cảm biến nhị phân**: Trạng thái sạc, xả, cân bằng
- **Chẩn đoán**: Tín hiệu WiFi, địa chỉ IP, thông tin thiết bị

### Cards Dashboard
```yaml
# Ví dụ card điều khiển quạt
type: entities
entities:
  - entity: switch.fan_auto_mode
  - entity: switch.fan_manual_control  
  - entity: number.fan_t_high_c
  - entity: number.fan_t_low_c
  - entity: sensor.temperature_sensor_1
```

## 🛡️ Tính Năng Bảo Mật

- **Quản Lý Secrets**: Thông tin WiFi được lưu riêng biệt
- **Bảo Vệ Git**: `.gitignore` ngăn chặn lộ thông tin nhạy cảm
- **Bảo Vệ OTA**: Tùy chọn bảo vệ mật khẩu cho cập nhật
- **Mã Hóa API**: Tùy chọn mã hóa giao tiếp API

## 🐛 Khắc Phục Sự Cố

### Các Vấn Đề Thường Gặp

**Lỗi Kiểm Tra CRC**
```
[W][modbus:135]: Modbus CRC Check failed! 6C0!=603
```
- Lỗi thỉnh thoảng do nhiễu điện bình thường
- Không ảnh hưởng chức năng nếu vẫn nhận được dữ liệu
- Cân nhắc thêm `send_wait_time: 50ms` để giảm tần suất

**Không Có Dữ Liệu Nhiệt Độ**
```
[W][fan]: No temperature data available, turning fan OFF
```
- Kiểm tra thực thể Home Assistant: `sensor.device_h240909079_device_temperature`
- Xác minh ESPHome có thể kết nối với Home Assistant
- Quạt sẽ an toàn TẮT cho đến khi có dữ liệu

**Lỗi Compilation**
- Đảm bảo tất cả tham chiếu `id:` khớp giữa các file
- Kiểm tra thụt lề YAML (dùng space, không phải tab)
- Xác minh phân công chân cho board cụ thể của bạn

### Lệnh Debug
```bash
# Kiểm tra cấu hình
esphome config esp32-c3-bms-monitor.yaml

# Xem logs
esphome logs esp32-c3-bms-monitor.yaml --device 192.168.x.x

# Xóa build cache
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