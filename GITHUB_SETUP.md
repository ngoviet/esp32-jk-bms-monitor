# 🚀 Hướng dẫn Upload lên GitHub

## Bước 1: Tạo Repository trên GitHub
1. Đăng nhập vào GitHub.com
2. Click nút "New" để tạo repository mới
3. Điền thông tin:
   - **Repository name**: `esp32-jk-bms-monitor`
   - **Description**: `ESPHome configuration for JK-BMS 24V 280Ah monitoring with ESP32`
   - **Public/Private**: Chọn theo nhu cầu
   - ❌ **KHÔNG** tick "Add a README file" (vì đã có rồi)
   - ❌ **KHÔNG** tick "Add .gitignore" (vì đã có rồi)
4. Click "Create repository"

## Bước 2: Connect local repo với GitHub
Copy URL repository từ GitHub (dạng: https://github.com/username/esp32-jk-bms-monitor.git)

Chạy lệnh sau trong terminal:

```bash
# Thêm remote origin
git remote add origin https://github.com/YOUR_USERNAME/esp32-jk-bms-monitor.git

# Đổi branch thành main (nếu cần)
git branch -M main

# Push code lên GitHub
git push -u origin main
```

## Bước 3: Xác minh
1. Reload trang GitHub repository
2. Kiểm tra các file đã upload:
   ✅ README.md
   ✅ common.yaml
   ✅ jk-bms-24v280ah.yaml
   ✅ secrets.yaml.example
   ✅ .gitignore
   ✅ Script files (.bat)
   ❌ secrets.yaml (KHÔNG có - đây là điều tốt!)

## 🔒 Bảo mật đã được đảm bảo:
- ✅ File secrets.yaml được gitignore
- ✅ Không có mật khẩu hardcoded
- ✅ Template file hướng dẫn người dùng

## 📝 Sau khi upload thành công:
1. Thêm topics cho repository: `esphome`, `esp32`, `jk-bms`, `battery-monitor`
2. Tạo release đầu tiên nếu muốn
3. Chia sẻ link repository!

---
🎉 **Repository sẵn sàng để public và chia sẻ!**