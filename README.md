# 📡 SIM OTA Test Tool

OTA 서버 인프라 없이 PC에서 직접 SIM OTA envelope을 생성하고 단말에 전송하는 웹 기반 도구.

## 💡 이 도구가 필요한 이유

SIM OTA 메시지를 단말에 전송하려면 통상 OTA 서버 인프라가 필요합니다. 이 도구는 USB 시리얼 포트의 AT+CSIM 명령을 통해 ENVELOPE APDU를 직접 SIM에 전달하여, 서버 없이도 BIP 트리거와 RFM(Remote File Management)을 수행할 수 있습니다. SIM을 물리적 리더기에 연결하지 않고 단말 USB 연결만으로 SIM 파일을 업데이트할 수 있으며, eSIM only 디바이스에서도 동일하게 동작합니다. 대용량 데이터는 Concatenated SMS로 자동 분할 전송됩니다.

## ⚡ 주요 기능

### OTA 메시지 자동 생성 + 전송
- BIP 트리거(자사/GP) 및 RFM envelope을 자동 계산하여 생성
- USB 연결만으로 AT+CSIM 커맨드를 통해 단말에 직접 주입
- 대용량 메시지는 Concatenated SMS로 자동 분할 처리
- adb 기반 IMS SMS 주입 방식도 지원 (SIP MESSAGE 루프백)

### SIM 파일 원격 업데이트 (RFM)
- **PLMN 설정** — HPLMNwAcT, OPLMNwAcT, PLMNwAcT, FPLMN, EHPLMN 편집으로 국내 및 로밍 테스트 환경 구축
- **UST 제어** — USIM Service Table 개별 서비스 ON/OFF로 SIM 기능 제어
- **URSP 업데이트** — 5G 네트워크 슬라이싱 룰을 단말에 직접 기록하여 슬라이싱 테스트 활용

## 💻 다운로드

[Releases](https://github.com/joostone-ahn/sim-ota-test-releases/releases) 페이지에서 최신 exe를 다운로드하세요.

## 📖 사용 방법

자세한 사용법은 [사용자 가이드](https://github.com/joostone-ahn/sim-ota-test-releases/blob/main/manual/user_guide_kr_v1.0.0.md)를 참고하세요.

## 📚 참고 규격

- 3GPP TS 31.111 — USIM Application Toolkit (USAT)
- 3GPP TS 31.115 — Secured packet structure for (U)SIM Toolkit
- ETSI TS 102.225 — Secured packet structure for UICC based applications
- ETSI TS 102.226 — Remote APDU structure for UICC based applications
- 3GPP TS 24.526 — UE policies (URSP)
- 3GPP TS 27.007 — AT command set (AT+CSIM)
- GlobalPlatform SCP80/81 — OTA Secure Channel Protocol

## 👤 Author

**JUSEOK AHN (안주석)**  
Email: ajs3013@lguplus.co.kr  
Organization: LG U+  
Role: Technical Specialist, Telecommunications Engineer

## 📄 License

© 2026 JUSEOK AHN <ajs3013@lguplus.co.kr>. All rights reserved.
