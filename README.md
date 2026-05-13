# 📡 SIM OTA Test Tool

OTA 서버 인프라 없이 PC에서 직접 SIM OTA envelope을 생성하고 단말에 전송하는 웹 기반 도구.

## 💡 이 도구가 필요한 이유

SIM OTA(Over-the-Air) 메시지를 단말에 전송하려면 통상 OTA 서버 인프라가 필요합니다. 이 도구는 USB 시리얼 포트의 AT+CSIM 명령을 통해 ENVELOPE APDU를 직접 SIM에 전달하여, 서버 없이도 BIP 트리거와 RFM(Remote File Management)을 수행할 수 있습니다. ADM Key 없이도 PLMN, UST, URSP 등 SIM 파일을 원격 업데이트할 수 있으며, 대용량 데이터는 Concatenated SMS로 자동 분할 전송됩니다.

## ⚡ 주요 기능

- **BIP 트리거** — 자사 BIP (CRC32 RC) / GP BIP (3DES CC) 개통·번호변경·자동국업
- **RFM (Remote File Management)** — PLMN, UST, URSP, 커스텀 APDU 원격 업데이트
- **대용량 분할 전송** — 단일 ENVELOPE 크기 초과 시 Concatenated SMS 자동 분할
- **URSP 3단계 자동 처리** — UST OFF → Modem Reset → UST ON + URSP 업데이트
- **IMS SMS 주입** — adb 기반 SIP MESSAGE 루프백 자기주입 (개발 모드)
- **자동 Modem Reset** — REFRESH 미발생 시 AT+CFUN=0/1 자동 실행 + 폴링 복구
- **APDU 색상 미리보기** — CLA / INS / P1 / P2 / Lc / Data 구분 표시
- **KID2 자동 채움** — MSISDN 기반 OTA Key 매칭 (`test_profile.json`)
- **세션 로깅** — 매 전송마다 `logs/` 디렉토리에 meta.json 자동 저장

## 💻 다운로드

[Releases](https://github.com/joostone-ahn/sim-ota-test-releases/releases) 페이지에서 최신 exe를 다운로드하세요.

## 📖 사용 방법

자세한 사용법은 [사용자 가이드](manual/user_guide_kr_v1.0.0.md)를 참고하세요.

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
