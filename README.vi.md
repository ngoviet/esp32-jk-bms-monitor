# ESP32 giám sát JK-BMS

<!-- readme-axi type=esp32 -->

[English](README.md) · **Tiếng Việt**

Cấu hình **ESPHome** giám sát pin **JK-BMS 24V 280Ah** bằng **ESP32** (ESP32-WROOM-32). ESP32 kết
nối và giám sát pin lithium JK-BMS qua giao thức **RS485/UART**, tích hợp với **Home Assistant**
thông qua ESPHome.

> 🇻🇳 Dự án do một người Việt tự xây dựng và duy trì — nếu nó giúp ích cho bạn, hãy
> [ủng hộ dự án](#ủng-hộ-dự-án).

## Mục lục

- [Tính năng](#tính-năng)
- [Phần cứng](#phần-cứng)
- [Bắt đầu nhanh](#bắt-đầu-nhanh)
- [Cấu hình](#cấu-hình)
- [Home Assistant](#home-assistant)
- [Xử lý sự cố](#xử-lý-sự-cố)
- [Cấu trúc dự án](#cấu-trúc-dự-án)
- [Phát triển](#phát-triển)

## Tính năng

- **Giám sát JK-BMS** cho pin 24V 280Ah (8 cell LiFePO4) qua RS485/UART, cập nhật 5 giây/lần
- **Cảm biến điện áp, dòng điện, nhiệt độ, dung lượng** cùng thông số hệ thống
- **Công tắc bật/tắt sạc, xả** điều khiển từ Home Assistant
- **Tích hợp Home Assistant** qua ESPHome
- **Wi-Fi tự chọn AP mạnh nhất** trong các AP cùng SSID, kèm **điểm truy cập cứu hộ** `24v280ah-cuu` khi không vào được Wi-Fi nhà
- **Cảm biến chẩn đoán**: RSSI, chất lượng Wi-Fi, AP đang bám (BSSID), IP, heap trống, uptime
- Dựa trên [syssi/esphome-jk-bms](https://github.com/syssi/esphome-jk-bms) **3.1.0**, ESPHome **2026.11**, framework **ESP-IDF**

## Phần cứng

### Thông số kỹ thuật

- **Pin**: JK-BMS 24V 280Ah (8 cell LiFePO4)
- **Vi điều khiển**: ESP32-WROOM-32
- **Giao tiếp**: UART (GPIO16 TX, GPIO17 RX)
- **Tốc độ baud**: 115200
- **Cập nhật**: 5 giây/lần

### Kết nối phần cứng

```
JK-BMS RS485 ↔ TTL Converter ↔ ESP32
    A+ ────────── A+ ───────────
    B- ────────── B- ───────────
                 VCC ─────────── 3.3V
                 GND ─────────── GND
                 TXD ─────────── GPIO17 (RX)
                 RXD ─────────── GPIO16 (TX)
```

## Bắt đầu nhanh

### 1. Chuẩn bị

- Cài đặt ESPHome
- Kết nối ESP32 với JK-BMS qua RS485/TTL converter
- **Cấu hình bảo mật**: Copy `secrets.yaml.example` → `secrets.yaml` và điền thông tin thực tế
- **Wi-Fi**: điền SSID, mật khẩu, `ota_password` và `ap_password` (mật khẩu điểm truy cập cứu hộ)

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

## Cấu hình

### Cấu hình WiFi cơ bản

Chỉ cần SSID và password trong `secrets.yaml`:

```yaml
wifi_ssid: "Your_WiFi_Name"
wifi_password: "Your_WiFi_Password"
```

### Không ghim BSSID/kênh

Bản cũ ghim BSSID và kênh của một AP Aruba. Từ ESPHome 2026.x, thiết bị chỉ nhận đúng AP đã ghim: ngày 19/11/2026, BSSID ghim thuộc một AP đã được thay, nên sau lần nạp OTA thiết bị không bao giờ vào lại mạng (phải nạp lại qua USB). Nay `common.yaml` để `fast_connect: false`, thiết bị quét cả 3 AP cùng SSID và chọn AP mạnh nhất. **Đừng thêm lại `bssid`/`channel`.**

### Cấu trúc cấu hình

`jk-bms-24v280ah.yaml` nạp `common.yaml` bằng `packages:` (trộn sâu). Không dùng `<<: !include`: đó là phép trộn nông, file thiết bị có `wifi:`/`sensor:` là thay hẳn khối đó của `common.yaml` (bản cũ vì thế mất cấu hình Wi-Fi và mọi cảm biến chẩn đoán).

Mọi `name:` trong file thiết bị là nguồn của entity_id trên Home Assistant (`sensor.tang_3_24v280ah_*`): đừng đổi tên.

### Bảo mật

**QUAN TRỌNG — Bảo mật thông tin:**

1. **KHÔNG BAO GIỜ** commit file `secrets.yaml` lên Git
2. Sử dụng **passwords mạnh** (ít nhất 12 ký tự)
3. **Thay đổi OTA password** khỏi mặc định
4. Kiểm tra `.gitignore` trước khi commit

Thiết lập bảo mật:

```bash
# 1. Copy template
cp secrets.yaml.example secrets.yaml

# 2. Chỉnh sửa với thông tin thực tế
# 3. Kiểm tra gitignore
git status  # secrets.yaml KHÔNG được hiển thị

# 4. Tạo OTA password mạnh
openssl rand -base64 32
```

## Home Assistant

Sau khi flash thành công, thiết bị sẽ tự động xuất hiện trong Home Assistant qua ESPHome
integration, với các dữ liệu giám sát:

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

## Xử lý sự cố

### Lỗi thường gặp

1. **Không kết nối được BMS**: Kiểm tra kết nối RS485, baud rate
2. **Mất Wi-Fi**: xem cảm biến `WiFi RSSI`/`WiFi BSSID`. Không vào được mạng thì sau 1 phút thiết bị phát `24v280ah-cuu`: nối điện thoại vào, mở `192.168.4.1` để nhập lại Wi-Fi hoặc nạp firmware
3. **Dữ liệu không cập nhật**: Kiểm tra logs, restart thiết bị

### Debug

```bash
# Xem logs chi tiết
esphome-logs.bat

# Xóa flash và cài lại
esphome-erase.bat
esphome-flash.bat
```

### Cần hỗ trợ

Nếu có vấn đề hoặc câu hỏi, vui lòng mở
[issue](https://github.com/ngoviet/esp32-jk-bms-monitor/issues) trên GitHub.

## Cấu trúc dự án

```
├── common.yaml              # Config chung cho tất cả ESP32
├── jk-bms-24v280ah.yaml    # Config riêng cho JK-BMS
├── esphome-flash.bat       # Script flash firmware
├── esphome-erase.bat       # Script xóa flash
├── esphome-logs.bat        # Script xem logs
└── README.md               # Tài liệu này
```

## Phát triển

### Đóng góp

1. Fork project
2. Tạo feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Mở Pull Request

<!-- support:start -->
## Ủng hộ dự án

<p align="center">
  <img src="https://raw.githubusercontent.com/ngoviet/.github/main/assets/support/banner-v2.svg" width="560" alt="Built and maintained by one person in Việt Nam">
</p>

<p align="center">Một người Việt tự xây dựng và duy trì, miễn phí và mã nguồn mở. Sự ủng hộ của bạn giúp hỗ trợ thêm thiết bị, sửa lỗi nhanh hơn và ra bản mới đều đặn.</p>

<p align="center">
  <a href="https://buymeacoffee.com/ngoviet"><img src="https://raw.githubusercontent.com/ngoviet/.github/main/assets/support/btn-coffee.svg" height="56" alt="Buy me a coffee"></a>
  &nbsp;
  <a href="https://github.com/sponsors/ngoviet"><img src="https://raw.githubusercontent.com/ngoviet/.github/main/assets/support/btn-sponsors.svg" height="56" alt="GitHub Sponsors"></a>
</p>

<p align="center">
  <img src="https://raw.githubusercontent.com/ngoviet/.github/main/assets/support/vietqr-card.svg" width="300" alt="VietQR — MB Bank — NGO DUC VIET — 911051212">
  &nbsp;
  <img src="https://raw.githubusercontent.com/ngoviet/.github/main/assets/support/crypto-card.svg" width="300" alt="Crypto — BNB Smart Chain (BEP20) — 0xcbf0402b232cb24b4d998f09a4884f87b5f74750">
</p>

<p align="center"><b>Ví tiền mã hoá, chỉ gửi qua mạng BNB Smart Chain (BEP20):</b><br><code>0xcbf0402b232cb24b4d998f09a4884f87b5f74750</code></p>

<p align="center">🇻🇳 VietQR: quét bằng app ngân hàng bất kỳ, nội dung <code>Ung ho esp32-jk-bms-monitor</code>. Cảm ơn bạn đã giúp dự án tiếp tục phát triển!</p>
<!-- support:end -->

## Giấy phép

[MIT](LICENSE) © Ngô Đức Việt. Xem file [LICENSE](LICENSE) để biết thêm chi tiết.
