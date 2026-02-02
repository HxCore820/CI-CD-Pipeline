# 🚀 CI/CD Pipeline với Debug SSH (SSHX)

Pipeline này được thiết kế để **tự động build/test khi push code** và **cho phép bật phiên SSH Debug thủ công** khi cần kiểm tra trực tiếp môi trường GitHub Actions.

Phù hợp cho:
- 🧪 Debug lỗi khó tái hiện
- 🛠️ Kiểm tra môi trường runner
- 📦 Thử nghiệm build, script, container

---

## ✨ Tính năng chính

- ✅ **Tự động chạy khi `push` code**
- 🎛️ **Chạy thủ công (Workflow Dispatch)**
- 🔐 **Bật SSH Debug an toàn bằng SSHX**
- ⏳ Phiên SSH **tự động đóng khi job kết thúc**
- 🧼 Không ảnh hưởng pipeline khi không bật Debug

---

## 🧩 Cách hoạt động

### 1️⃣ Khi `push` code
Pipeline sẽ:
- Checkout mã nguồn
- Chạy các bước build / test thông thường  
👉 **KHÔNG mở SSH**

### 2️⃣ Khi chạy thủ công (Debug Mode)
Pipeline sẽ:
- Checkout mã nguồn
- Chạy build
- 🔓 **Mở phiên SSH Debug (SSHX)** để bạn truy cập trực tiếp vào runner

---

## ▶️ Cách bật SSH Debug

1. Vào tab **Actions** trong repository
2. Chọn workflow **CI/CD Pipeline**
3. Nhấn **Run workflow**
4. ✅ Tick chọn **Bật chế độ Debug (SSHX)**
5. Nhấn **Run**
6. Mở log → click vào **URL SSHX** để vào terminal

📌 *Lưu ý:*  
- Phiên SSH chỉ tồn tại **trong thời gian job đang chạy**
- Khi workflow kết thúc → SSH tự động ngắt

---

## 🔐 Bảo mật

- SSH **chỉ được bật khi chạy thủ công**
- Không mở cổng mạng công khai
- Không lưu key, không ghi log thông tin nhạy cảm
- An toàn cho môi trường CI/CD

---

## 🧪 Ví dụ Workflow

```yaml
name: CI/CD Pipeline

on:
  push:
  workflow_dispatch:
    inputs:
      debug_mode:
        description: 'Bật chế độ Debug (SSHX)'
        required: false
        default: false
        type: boolean

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: 📥 Checkout code
        uses: actions/checkout@v3

      - name: ⚙️ Build (Ví dụ)
        run: echo "Đang chạy build..."

      - name: 🛠️ SSH Debug (SSHX)
        if: ${{ github.event_name == 'workflow_dispatch' && inputs.debug_mode == true }}
        run: curl -sSf https://sshx.io/get | sh -s run
