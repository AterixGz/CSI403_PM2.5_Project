# 💨 BKK Air Alert

> ระบบแจ้งเตือนคุณภาพอากาศกรุงเทพมหานคร ผ่าน Discord — รันเป็น Desktop App บน Windows / macOS / Linux

[![Platform](https://img.shields.io/badge/platform-Windows%20%7C%20macOS%20%7C%20Linux-blue)](https://github.com/YOUR_USERNAME/bkk-air-alert/releases)
[![Electron](https://img.shields.io/badge/Electron-28-47848F?logo=electron)](https://www.electronjs.org/)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)

---

## ✨ Features

- 🕐 **ตรวจสอบอัตโนมัติ** ทุก 15 / 30 / 60 / 180 นาที
- 🌫 **PM2.5, AQI, PM10, O₃, NO₂** จาก WAQI API
- 🌡 **อุณหภูมิ, ความชื้น, ลม** จาก OpenWeatherMap API
- 🟢🟡🟠🔴🟣⛔ **จัดระดับ AQI 7 ระดับ** พร้อมคำแนะนำภาษาไทย
- 📈 **แนวโน้ม PM2.5** (rising / falling / stable)
- 🔔 **Desktop Notification** + Discord Rich Embed
- ⏱ **Cooldown 60 นาที** ป้องกัน spam
- 🖥 **System Tray** — รันอยู่เบื้องหลัง ไม่รบกวนการใช้งาน
- ⚙️ **Zero-config** — ตั้งค่าแค่ Discord Webhook URL เท่านั้น

---

## 📸 ตัวอย่าง Discord Embed

```
🟠 Air Quality Alert — กรุงเทพมหานคร
ไม่ดีต่อกลุ่มเสี่ยง | ความเสี่ยง: สูงต่อกลุ่มเสี่ยง

PM2.5 = 84 µg/m³ (เกินมาตรฐาน 50)

🌫 PM2.5 (µg/m³)
84 µg/m³  ██████░░░░  มีผลต่อสุขภาพ (≤90)

📊 AQI          🌡 อุณหภูมิ      💨 ลม
130             31.7°C 💧61%    6.73 m/s SE

🏭 PM10         ☁️ Ozone         🔬 NO₂
83 µg/m³        16.2 µg/m³      5.2 µg/m³

📈 แนวโน้ม PM2.5
📈 กำลังเพิ่มขึ้น

💡 คำแนะนำ
กลุ่มเสี่ยงควรลดกิจกรรมกลางแจ้ง ควรสวมหน้ากาก N95

📍 สถานี: Bangkok | อัปเดต: 17/4/2569 20:11
```

---

## 🚀 การติดตั้ง

### ผู้ใช้ทั่วไป (ดาวน์โหลด Installer)

1. ไปที่ [Releases](https://github.com/YOUR_USERNAME/bkk-air-alert/releases)
2. ดาวน์โหลด `BKK Air Alert Setup x.x.x.exe` (Windows)
3. ติดตั้งและเปิดแอป
4. วาง Discord Webhook URL → เลือกเวลา → เปิด Toggle → บันทึก ✅

### นักพัฒนา (Build จาก Source)

**Prerequisites:** Node.js v18+

```bash
# Clone
git clone https://github.com/YOUR_USERNAME/bkk-air-alert.git
cd bkk-air-alert

# Install dependencies (ครั้งแรกใช้เวลา 5-15 นาที)
npm install

# Run development
npm start

# Build installer
npm run build:win    # Windows (.exe)
npm run build:mac    # macOS (.dmg)
npm run build:linux  # Linux (.AppImage)
```

ไฟล์ installer จะอยู่ในโฟลเดอร์ `dist/`

---

## ⚙️ การตั้งค่า

แอปต้องการเพียง **Discord Webhook URL** เท่านั้น ไม่มี `.env` ไม่มี config file เพิ่มเติม

### วิธีสร้าง Discord Webhook

1. Discord → คลิกขวา Channel → **Edit Channel**
2. **Integrations** → **Webhooks** → **New Webhook**
3. ตั้งชื่อ → **Copy Webhook URL**
4. วาง URL ในแอป

---

## 🏗 สถาปัตยกรรม

```
┌─────────────────────────────────────────────┐
│              Electron App                   │
│                                             │
│  ┌──────────────┐    IPC    ┌────────────┐  │
│  │ Main Process │◄────────►│ Renderer   │  │
│  │              │           │ (Settings  │  │
│  │ • Scheduler  │           │  UI)       │  │
│  │ • API calls  │           └────────────┘  │
│  │ • AQI Logic  │                           │
│  │ • System Tray│                           │
│  └──────┬───────┘                           │
└─────────┼───────────────────────────────────┘
          │
    ┌─────▼──────────────────────┐
    │  External APIs             │
    │  • OpenWeatherMap (Weather) │
    │  • WAQI (AQI / PM2.5)      │
    └─────┬──────────────────────┘
          │
    ┌─────▼──────────────────────┐
    │  Discord Webhook           │
    │  (Rich Embed Notification) │
    └────────────────────────────┘
```

---

## 📊 เงื่อนไขการแจ้งเตือน

| ค่า | Threshold | ที่มา |
|-----|-----------|-------|
| PM2.5 | > 50 µg/m³ | มาตรฐานคุณภาพอากาศไทย |
| AQI | > 100 | US EPA Standard |
| อุณหภูมิ | > 35°C | กรมอุตุนิยมวิทยา |
| Ozone (O₃) | > 100 µg/m³ | WHO Guideline |
| Cooldown | 60 นาที | ป้องกัน Alert Fatigue |

---

## 📁 โครงสร้างโปรเจค

```
bkk-air-alert/
├── src/
│   ├── main.js        # Electron Main Process (Logic, API, Tray)
│   └── index.html     # Renderer Process (Settings UI)
├── assets/            # App icons
├── package.json       # Project config & build settings
└── README.md
```

---

## 🔧 Tech Stack

| Technology | การใช้งาน |
|------------|-----------|
| [Electron](https://www.electronjs.org/) v28 | Desktop app framework |
| [Node.js](https://nodejs.org/) | Runtime & HTTP requests |
| [electron-builder](https://www.electron.build/) | Packaging & installer |
| OpenWeatherMap API | Weather data |
| WAQI (World Air Quality Index) API | AQI & pollutant data |
| Discord Webhook API | Notification delivery |

---

## 📖 เอกสารเพิ่มเติม

- [คู่มือติดตั้งและใช้งาน (DOCX)](docs/installation-manual.docx)
- [Pattern สรุปโปรเจค (DOCX)](docs/pattern-summary.docx)

---

## 📝 License

MIT License — ดูรายละเอียดใน [LICENSE](LICENSE)
