# Phone2Hub System Architecture & Specification

This document defines the overall system design, technology stack, communication protocols, and development roadmap for the **Phone2Hub** project.  
Refer to this document across sessions and development environments to maintain engineering continuity.

---

## 1. Project Overview

* **Project Name**: Phone2Hub (Phone to Hub)
* **Goal**: An all-in-one open-source solution that transforms a smartphone (specifically iPhone) into essential PC hardware peripherals (webcam, microphone, network adapter, audio speaker, etc.) simply by connecting via a USB cable to a Windows PC.
* **Target Audience**: 
  * Windows + iPhone users looking for a professional-grade streaming/meeting setup without purchasing expensive dedicated webcams or hardware dongles.
  * Users fatigued by fragmented, costly subscription-based software (e.g., Camo at ~$40/yr, Duet Display, etc.).

---

## 2. System Architecture

```
+-----------------------------------+               +--------------------------------------+
|       iOS Client (iPhone)         |               |         Windows Host (PC)            |
|                                   |               |                                      |
|  [AVFoundation Camera/Mic]        |               |  [usbmuxd / TCP Port Forwarding]     |
|              │                    |               |                   │                  |
|              ▼                    |               |                   ▼                  |
|  [Hardware H.264 / AAC Encoder]   |               |  [FFmpeg / H.264 Video Decoder]      |
|              │                    |               |                   │                  |
|              ▼                    |               |                   ▼                  |
|  [usbmuxd Socket Client]          | ──USB Cable── |  [Virtual Camera Driver (OBS Source)]|
|                                   |               |                   │                  |
+-----------------------------------+               |                   ▼                  |
                                                    |  [System Apps: Zoom, Discord, etc.]  |
                                                    +--------------------------------------+
```

### 2.1. iOS Client (Sender)
* **Key Role**: Captures camera video (4K/1080p @ 60fps) and microphone audio, encodes them via hardware acceleration, and streams frames over the USB port.
* **Tech Stack**: 
  * Language: Swift (Xcode)
  * Frameworks: AVFoundation (Camera/Audio), VideoToolbox (Hardware-accelerated encoding), Network.framework
* **Core Policies**:
  * Minimal UI: Keeps a "Waiting for Connection" screen or dims the screen to conserve battery.
  * Background / Sleep handling: Sets `isIdleTimerDisabled = true` to prevent screen lock during active camera capture.

### 2.2. Transport Layer
* **USB Protocol**: `usbmuxd` (Apple USB Multiplex Daemon)
  * Utilizes Apple's native USB multiplexing protocol to create high-speed local TCP socket tunnels (100–500+ Mbps) over a single cable without needing a Wi-Fi connection.
  * Latency: Under 30–50ms (ideal for real-time video conferencing).

### 2.3. Windows Host (Receiver)
* **Key Role**: Receives video/audio streams over the USB socket, decodes them, and feeds them into Windows virtual devices (Virtual Webcam / Microphone).
* **Tech Stack**:
  * Core Logic: C++ or Python (for prototyping)
  * Video Decoding: FFmpeg (libavcodec)
  * Virtual Webcam Driver: OBS VirtualCam driver or DirectShow Virtual Audio/Video Filter
  * UI Launcher: Tauri (lightweight Rust + Web UI) or Flutter / Electron

---

## 3. Development Roadmap (Milestones)

### Phase 1: MVP (Minimum Viable Product - Video Streaming)
1. **Windows Receiver Setup**:
   * Verify `usbmuxd` port forwarding service on Windows (via Apple Mobile Device Support / iTunes or open-source `libimobiledevice` DLLs).
   * Prepare setup script for virtual webcam driver (OBS-VirtualCam).
2. **iOS Sender Prototype**:
   * Basic AVFoundation camera preview implementation.
   * Stream H.264 video frames through a local socket connection.
3. **End-to-End Verification**:
   * Connect iPhone ➡️ Windows over USB and verify real-time video playback in Zoom/Discord/OBS.

### Phase 2: Audio & User Experience (UX) Enhancements
* Synchronized microphone audio (AAC) transmission and Windows virtual microphone registration.
* Background auto-start on cable connection and Windows system tray mini-launcher.
* In-app controls: resolution (4K, 1080p), framerate (30/60fps), lens selection (Wide, Ultra-Wide, Front).

### Phase 3: All-in-One Hub Extensions
* **AirPlay Audio Receiver**: Output iPhone audio/music playback directly to PC speakers.
* **One-Click Tethering Guide**: Automated configuration for Windows network adapters.
* **Wireless Mode (Wi-Fi)**: mDNS / Bonjour discovery for cable-free local network streaming.

---

## 4. Distribution & Monetization Strategy

1. **GitHub Open Source Release (100% Free)**:
   * Source code and portable binaries published completely free on GitHub.
   * Community growth via platforms such as Reddit (`r/windows`, `r/iphone`, `r/opensource`) to attract early adopters.
2. **App Store & Microsoft Store Distribution (Convenience & Sponsorship)**:
   * Distributed on the Microsoft Store / iOS App Store at an affordable price ($3.99–$4.99) or free with optional Pro upgrade.
   * Optional Pro features: 4K 60fps lossless bitrate presets, AI background blur/bokeh, virtual lighting adjustments.
