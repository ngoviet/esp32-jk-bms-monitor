# ESP32 JK-BMS Monitor

ESPHome configuration cho việc giám sát pin JK-BMS 24V 280Ah thông qua ESP32.

## 📋 Mô tả

Project này sử dụng ESP32 để kết nối và giám sát pin lithium JK-BMS qua giao thức RS485/UART, tích hợp với Home Assistant thông qua ESPHome.

## 🔧 Thông số kỹ thuật

- **Pin**: JK-BMS 24V 280Ah (8 cell LiFePO4)
- **Vi điều khiển**: ESP32-WROOM-32
- **Giao tiếp**: UART (GPIO16 TX, GPIO17 RX)
- **Tốc độ baud**: 115200
- **Cập nhật**: 5 giây/lần

## 📁 Cấu trúc file

```
├── common.yaml              # Config chung cho tất cả ESP32
├── jk-bms-24v280ah.yaml    # Config riêng cho JK-BMS
├── esphome-flash.bat       # Script flash firmware
├── esphome-erase.bat       # Script xóa flash
├── esphome-logs.bat        # Script xem logs
└── README.md               # Tài liệu này
```

## 🚀 Cách sử dụng

### 1. Chuẩn bị
- Cài đặt ESPHome
- Kết nối ESP32 với JK-BMS qua RS485/TTL converter
- **Cấu hình bảo mật**: Copy `secrets.yaml.example` → `secrets.yaml` và điền thông tin thực tế
- **WiFi Setup**: Cấu hình SSID, password, và tùy chọn BSSID/channel nếu cần

### 2. Flash firmware
```bash
# Flash lần đầu
esphome run jk-bms-24v280ah.yaml

# Hoặc sử dụng script
esphome-flash.bat
```

### 3. Xem logs
```bash
# Xem logs realtime
esphome logs jk-bms-24v280ah.yaml

# Hoặc sử dụng script
esphome-logs.bat
```

## 📊 Dữ liệu giám sát

### Sensors
- **Điện áp**: Tổng, từng cell, min/max, delta
- **Dòng điện**: Sạc, xả, công suất
- **Nhiệt độ**: Sensor 1, 2, MOSFET
- **Dung lượng**: Còn lại, chu kỳ sạc
- **Hệ thống**: WiFi signal, RAM, uptime

### Binary Sensors
- Trạng thái sạc/xả
- Cân bằng cell
- Kết nối online

### Switches
- Bật/tắt sạc
- Bật/tắt xả

## � Cấu hình WiFi

### Cấu hình cơ bản:
Chỉ cần SSID và password trong `secrets.yaml`:
```yaml
wifi_ssid: "Your_WiFi_Name"
wifi_password: "Your_WiFi_Password"
```

### Cấu hình nâng cao (tùy chọn):
Nếu có nhiều Access Point cùng SSID hoặc muốn ưu tiên AP cụ thể:
```yaml
# Chỉ định BSSID và channel cụ thể
wifi_bssid_t3: "aa:bb:cc:dd:ee:ff"
wifi_channel_t3: 11
```

### Lợi ích của việc chỉ định BSSID/Channel:
- 🚀 **Faster connect**: Không cần scan toàn bộ channels
- 🎯 **Stable connection**: Luôn kết nối đúng AP mong muốn  
- 📡 **Better roaming**: Tránh kết nối AP xa hoặc signal yếu

## �🔗 Kết nối phần cứng

```
JK-BMS RS485 ↔ TTL Converter ↔ ESP32
    A+ ────────── A+ ─────────── 
    B- ────────── B- ───────────
                 VCC ─────────── 3.3V
                 GND ─────────── GND  
                 TXD ─────────── GPIO17 (RX)
                 RXD ─────────── GPIO16 (TX)
```

## 📈 Home Assistant Integration

Sau khi flash thành công, thiết bị sẽ tự động xuất hiện trong Home Assistant qua ESPHome integration.

## � Bảo mật

### ⚠️ QUAN TRỌNG - Bảo mật thông tin:
1. **KHÔNG BAO GIỜ** commit file `secrets.yaml` lên Git
2. Sử dụng **passwords mạnh** (ít nhất 12 ký tự)
3. **Thay đổi OTA password** khỏi mặc định
4. Kiểm tra `.gitignore` trước khi commit

### Thiết lập bảo mật:
```bash
# 1. Copy template
cp secrets.yaml.example secrets.yaml

# 2. Chỉnh sửa với thông tin thực tế
# 3. Kiểm tra gitignore
git status  # secrets.yaml KHÔNG được hiển thị

# 4. Tạo OTA password mạnh
openssl rand -base64 32
```

## �🛠️ Troubleshooting

### Lỗi thường gặp:
1. **Không kết nối được BMS**: Kiểm tra kết nối RS485, baud rate
2. **WiFi không ổn định**: Kiểm tra signal strength, cấu hình channel
3. **Dữ liệu không cập nhật**: Kiểm tra logs, restart thiết bị

### Debug:
```bash
# Xem logs chi tiết
esphome-logs.bat

# Xóa flash và cài lại
esphome-erase.bat
esphome-flash.bat
```

## 📝 License

MIT License - Xem file LICENSE để biết thêm chi tiết.

## 🤝 Contributing

1. Fork project
2. Tạo feature branch (`git checkout -b feature/amazing-feature`)  
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Mở Pull Request

## 📞 Support

Nếu có vấn đề hoặc câu hỏi, vui lòng mở issue trên GitHub.