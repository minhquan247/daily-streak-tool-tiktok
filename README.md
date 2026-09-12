# TikTok Automatic Sender

Tự động gửi video TikTok hàng ngày đến danh sách bạn bè để duy trì streak.

## Tính năng

- Giao diện UI (Tkinter) để quản lý danh sách người nhận
- Tự động scan danh sách DM và resolve username
- Hỗ trợ đa nền tảng: Linux, Windows, macOS
- Lên lịch gửi tự động hàng ngày theo giờ cố định
- Inject cookie từ file (không cần login lại mỗi lần)
- Tự động xử lý Screen Time popup, Sleep Hours popup
- Tuy nhiên, người dùng cần phải tự tay xử lý captcha khi trang yêu cầu.

## Yêu cầu

- Python 3.10+
- Google Chrome đã cài đặt
- TikTok account đã đăng nhập

## Cài đặt

```bash
# Clone repo
git clone https://github.com/minhquan247/daily-streak-tool-tiktok
cd daily-streak-tool-tiktok

# Tạo virtual environment
python3 -m venv .venv
source .venv/bin/activate  # Linux/macOS
.venv\Scripts\activate     # Windows

# Cài dependencies
pip install -r requirements.txt
playwright install chromium
playwright install-deps  # Bắt buộc trên Linux/Ubuntu
```

## Cấu hình

### 1. Export cookie từ TikTok

- Cài extension [EditThisCookie](https://chromewebstore.google.com/detail/editthiscookie-v3/ojfebgpkimhlhcblbalbfjblapadhbol)
- Đăng nhập TikTok trên Chrome
- Click extension → Export → lưu thành `cookies.json` vào thư mục project

### 2. Cấu hình `config.json`

Tạo file `config.json` từ mẫu `config.example`:

```json
{
  "schedule": {
    "time": "00:00",
    "timezone": "Asia/Ho_Chi_Minh",
    "run_on_start": true
  },
  "cookie_file": "cookies.json",
  "tiktok": {
    "headless": false,
    "user_data_dir_linux": "~/chrome-debug",
    "user_data_dir_macos": "~/Library/Application Support/chrome-debug",
    "user_data_dir_windows": "~\\AppData\\Local\\chrome-debug",
    "message_delay_seconds": [8, 18],
    "navigation_timeout_ms": 60000
  },
  "recipients": [],
  "videos": [
    "https://www.tiktok.com/@username/video/..."
  ],
  "telegram": {
    "enabled": false,
    "bot_token": "",
    "chat_id": ""
  }
}
```

## Sử dụng

### Chạy UI (Máy tính cá nhân / Cài đặt ban đầu)

```bash
python3 ui.py
```

1. Điền thông tin config (giờ gửi, delay, video links)
2. Click **Scan DM List** để lấy danh sách người nhận
3. Click **Resolve Usernames** để lấy username thật
4. Tick chọn người muốn gửi
5. Click **Start Sender**

### Chạy trực tiếp CLI

```bash
python3 main.py
```

---

## 🐧 Hướng dẫn chạy trên Ubuntu VPS (Headless / CLI 24/7)

### 💻 Cấu hình VPS đề xuất
- **CPU**: 1 vCPU
- **RAM**: 1 GB RAM (hoặc 2 GB)
- **Disk**: 10 GB - 15 GB SSD
- **OS**: Ubuntu 22.04 LTS / 24.04 LTS

### 💡 Mẹo nhỏ tối ưu khi thuê VPS 1GB RAM (Tạo Swap RAM)

Để phòng trường hợp Chromium ngốn bộ nhớ lúc tải trang làm văng script, hãy tạo 2GB Swap RAM bằng các lệnh sau:

```bash
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

### 2. Cài đặt trên VPS Ubuntu

```bash
# Update hệ thống & cài thư viện cần thiết
sudo apt update && sudo apt install -y python3-pip python3-venv git tmux

# Clone repo & truy cập thư mục
git clone https://github.com/minhquan247/daily-streak-tool-tiktok.git
cd daily-streak-tool-tiktok

# Tạo môi trường ảo & cài đặt dependencies
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
playwright install chromium
playwright install-deps  # ⚠️ Cài đặt các thư viện phụ thuộc hệ thống cho Chromium trên Ubuntu
```

### 3. Thiết lập cho VPS (Headless mode)

Trong file `config.json` trên VPS, hãy đảm bảo đặt `"headless": true`:

```json
"tiktok": {
  "headless": true
}
```

Upload file `cookies.json` đã export từ máy cá nhân lên thư mục project trên VPS.

### 4. Chạy ngầm 24/7 với `tmux`

```bash
# Mở session tmux mới
tmux new -s tiktok

# Kích hoạt môi trường và chạy script
source .venv/bin/activate
python3 main.py

# Thoát màn hình tmux (script vẫn chạy ngầm): Nhấn Ctrl + B rồi nhấn D
# Khi muốn mở lại xem log: tmux attach -t tiktok
```

---

## Lưu ý

- `cookies.json` hết hạn sau vài tháng → cần export lại
- Không commit `cookies.json` và `config.json` lên GitHub
- Giữ process chạy liên tục để scheduler hoạt động (dùng `tmux` hoặc `screen` trên Linux)

> ⚠️ **Disclaimer:** This project is for educational purposes only. 
> Automated interaction with TikTok may violate their Terms of Service.
> Use at your own risk.
