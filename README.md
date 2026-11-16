# Phone2Hub

> **Phone to Hub**: Turn your smartphone into a full-featured PC dock.
> 
> An all-in-one open-source tool that transforms your smartphone into a high-definition 4K webcam, microphone, wireless network adapter, and AirPlay audio receiver with a single cable plug.

---

## 🌟 Vision & Key Objectives
Phone2Hub aims to solve the fragmentation and expensive subscription models of existing proprietary apps (such as Camo, Duet Display, etc.) by delivering a **"plug-and-play, completely free, all-in-one PC dock"**.

* **4K 60fps Ultra-low Latency Webcam & Microphone**: Instantly recognized across Zoom, Discord, OBS, and more.
* **USB Plug & Play**: No complicated account sign-ins or network configuration required.
* **AirPlay Wireless Receiver & Speaker**: Stream iPhone audio and music directly through your PC speakers.
* **One-Click Internet Tethering & File Transfer**: Share mobile network connections without external dongles.
* **100% Free & Open Source**: Transparent codebase with zero backdoors or hidden fees.

---

## 📁 Project Structure (Monorepo)

```text
phone2hub/
├── docs/                   # Architecture and technical specifications
│   └── ARCHITECTURE.md     # System design, protocols, and MVP roadmap
├── windows/                # [Windows Host] Virtual camera driver and receiver launcher
└── ios/                    # [iOS Client] Camera/mic capture and USB streaming app (Swift)
```

---

## 🚀 Milestones

- [ ] **Phase 1 (MVP)**: 
  - [ ] iOS: Lightweight camera capture & USB (usbmuxd/socket) streaming client
  - [ ] Windows: Virtual webcam driver (DirectShow/MediaFoundation) registration & previewer
- [ ] **Phase 2**:
  - [ ] Microphone (Virtual Audio Driver) integration
  - [ ] Auto-detection (Plug & Play) UI launcher on USB connection
- [ ] **Phase 3**:
  - [ ] AirPlay audio/screen mirroring receiver module
  - [ ] Virtual display (secondary monitor) extension research

---

## 📄 License
MIT License (Planned)
