# Phone2Hub

> **Phone to Hub**: Turn your smartphone into a full-featured PC dock.
> 
> 케이블 하나만 꽂으면 스마트폰을 고화질 4K 웹캠, 마이크, 무선 인터넷, AirPlay 오디오 수신기로 바꿔주는 올인원 오픈소스 도구입니다.

---

## 🌟 핵심 목표 (Vision)
기존의 유료 구독형 앱(Camo, Duet Display 등)의 파편화와 과도한 비용을 해결하고, **"꽂기만 하면 바로 작동하는 무료 올인원 PC 허브"**를 제공합니다.

* **4K 60fps 초저지연 웹캠 & 마이크**: Zoom, Discord, OBS 등에서 즉시 인식
* **USB 플러그 앤 플레이**: 복잡한 계정 로그인이나 네트워크 설정 불필요
* **AirPlay 무선 수신 & 스피커**: 아이폰 음악/사운드를 PC 스피커로 출력
* **원클릭 인터넷 테더링 & 파일 전송**: 외장 동글 없이 무선 통신망 공유
* **100% 무료 & 오픈소스**: 백도어 없는 투명한 코드

---

## 📁 프로젝트 구조 (Monorepo)

```text
phone2hub/
├── docs/                   # 아키텍처 및 상세 기획 문서
│   └── ARCHITECTURE.md     # 시스템 설계, 통신 프로토콜, MVP 로드맵
├── windows/                # [Windows Host] 가상 카메라 드라이버 및 수신 런처
└── ios/                    # [iOS Client] 카메라/마이크 캡처 및 USB 송신 앱 (Swift)
```

---

## 🚀 개발 단계 (Milestones)

- [ ] **Phase 1 (MVP)**: 
  - [ ] iOS: 초경량 카메라 캡처 & USB(usbmuxd/소켓) 송신 앱
  - [ ] Windows: 가상 웹캠(DirectShow/MediaFoundation) 드라이버 등록 및 뷰어
- [ ] **Phase 2**:
  - [ ] 마이크(가상 오디오 드라이버) 연동
  - [ ] USB 케이블 연결 시 자동 감지(Plug & Play) UI 런처
- [ ] **Phase 3**:
  - [ ] AirPlay 오디오/화면 미러링 수신 기능
  - [ ] 보조 모니터(가상 디스플레이) 확장 기능 연구

---

## 📄 라이선스
MIT License (예정)
