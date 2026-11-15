# Phone2Hub 시스템 아키텍처 및 기획 명세서

이 문서는 **Phone2Hub** 프로젝트의 전체 설계, 기술 스택, 통신 프로토콜 및 개발 로드맵을 정의합니다.  
새로운 대화 세션이나 개발 환경에서 이 문서를 참조하여 개발을 연속성 있게 진행할 수 있습니다.

---

## 1. 프로젝트 개요 (Overview)

* **프로젝트명**: Phone2Hub (Phone to Hub)
* **목표**: 스마트폰(특히 iPhone)을 Windows PC에 USB 케이블로 연결했을 때, PC의 하드웨어 부품(웹캠, 마이크, 랜카드, 오디오 스피커 등)으로 확장해 주는 올인원 통합 오픈소스 솔루션.
* **타깃 사용자**: 
  * 고가의 웹캠이나 동글을 사지 않고 고품질 방송/회의 환경을 만들고 싶은 Windows + iPhone 사용자.
  * 기존 유료 구독 앱(Camo 연 5만 원, Duet Display 등)의 분산 및 비용에 피로감을 느끼는 사용자.

---

## 2. 시스템 아키텍처 (System Architecture)

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

### 2.1. iOS 클라이언트 (송신부)
* **주요 역할**: 카메라(4K/1080p, 60fps) 및 마이크 입력을 캡처하여 하드웨어 인코딩 후 USB 포트로 전송.
* **기술 스택**: 
  * 언어: Swift (Xcode)
  * 프레임워크: AVFoundation (카메라/오디오), VideoToolbox (하드웨어 가속 인코딩), Network.framework
* **핵심 정책**:
  * 복잡한 UI 배제: 앱 실행 시 "연결 대기 중" 또는 배터리 절약용 어두운 화면 유지.
  * 백그라운드 제한 대응: 카메라 촬영 중 화면이 꺼지지 않도록 `isIdleTimerDisabled = true` 설정.

### 2.2. 통신 계층 (Transport Layer)
* **USB 통신 프로토콜**: `usbmuxd` (Apple USB Multiplex Daemon)
  * 애플의 공식 USB 통신 방식을 활용하여 별도의 Wi-Fi 연결 없이도 케이블만으로 100~500Mbps 이상의 초고속 로컬 TCP 소켓 터널 생성.
  * 지연 시간(Latency): 0.03~0.05초 미만 (실시간 회의에 완벽 적합).

### 2.3. Windows 호스트 (수신부)
* **주요 역할**: USB 소켓을 통해 전송된 비디오/오디오 스트림을 수신, 디코딩 후 Windows 시스템 가상 디바이스(가상 웹캠/마이크)로 공급.
* **기술 스택**:
  * 코어 로직: C++ 또는 Python (프로토타입용)
  * 비디오 디코딩: FFmpeg (libavcodec)
  * 가상 웹캠 드라이버: OBS VirtualCam 오픈소스 드라이버 또는 DirectShow Virtual Audio/Video Filter
  * UI 런처: Tauri (경량 Rust + Web UI) 또는 Flutter / Electron

---

## 3. 개발 로드맵 (Milestones)

### Phase 1: MVP (최소 기능 제품 - 영상 스트리밍)
1. **Windows 수신 테스트**:
   * Windows에서 `usbmuxd` 포트 포워딩 서비스 확인 (`iTunes` 서비스 또는 오픈소스 `libimobiledevice` DLL 활용).
   * 가상 웹캠 드라이버(OBS-VirtualCam) 설치 스크립트 작성.
2. **iOS 송신 앱 프로토타입**:
   * 기본 AVFoundation 카메라 뷰 구현.
   * 로컬 포트로 H.264 비디오 프레임 송출.
3. **End-to-End 연결 검증**:
   * iPhone ➡️ Windows USB 연결 후 줌(Zoom)에서 iPhone 카메라 화면이 정상 출력되는지 검증.

### Phase 2: 오디오 & 사용자 경험(UX) 개선
* 마이크 오디오(AAC) 동기화 전송 및 Windows 가상 마이크 등록.
* "연결 시 자동 실행" 및 윈도우 트레이 아이콘 미니 런처 개발.
* 해상도(4K, 1080p), 프레임레이트(30/60fps), 렌즈 선택(광각/초광각/전면) 제어 기능.

### Phase 3: 올인원 허브 기능 확장
* **AirPlay 수신 모듈**: 폰에서 음악 재생 시 PC 스피커로 출력.
* **테더링 원클릭 가이드**: 윈도우 네트워크 어댑터 자동 세팅.
* **무선 모드(Wi-Fi)**: USB 케이블 없이도 동일 네트워크에서 자동 탐색(mDNS/Bonjour) 기능 지원.

---

## 4. 수익화 및 배포 전략

1. **GitHub 오픈소스 배포 (100% 무료)**:
   * 소스코드 및 무설치/포터블 릴리즈는 GitHub에 완전 무료 공개.
   * Reddit(r/windows, r/iphone, r/opensource) 바이럴 마케팅으로 초기 유저 및 스타 확보.
2. **스토어 유료 배포 (후원 및 자동 업데이트 편의성 제공)**:
   * Microsoft Store / iOS App Store에는 커피 한 잔 값($3.99~$4.99)의 유료 또는 무료+프로 인앱 결제 형태로 출시.
   * 프로 기능(선택 사항): 4K 60fps 무손실 비트레이트, AI 보케/배경 블러, 가상 조명 필터 등.
