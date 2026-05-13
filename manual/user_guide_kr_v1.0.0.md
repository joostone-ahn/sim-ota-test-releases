# SIM OTA Test Tool — 사용자 가이드

> 버전: v1.0.0  
> 최종 수정: 2026-05-12

---

## 목차

1. [개요](#1-개요)
2. [화면 구성](#2-화면-구성)
3. [디바이스 준비](#3-디바이스-준비)
4. [연결하기](#4-연결하기)
5. [전송 방식 선택](#5-전송-방식-선택)
6. [자사 BIP](#6-자사-bip)
7. [GP BIP](#7-gp-bip)
8. [RFM — PLMN 업데이트](#8-rfm--plmn-업데이트)
9. [RFM — UST 수정](#9-rfm--ust-수정)
10. [RFM — URSP 업데이트](#10-rfm--ursp-업데이트)
11. [RFM — APDU 직접 작성](#11-rfm--apdu-직접-작성)
12. [실행 로그](#12-실행-로그)
13. [KID2 자동 채움](#13-kid2-자동-채움)
14. [문제 해결](#14-문제-해결)

---

## 1. 개요

SIM OTA Test Tool은 OTA 서버 인프라 없이 PC에서 직접 SIM OTA envelope을 생성하고 단말에 전송하는 도구입니다. exe 파일을 실행하면 웹 브라우저에서 동작합니다.

**주요 기능:**
- BIP 트리거 전송 (자사 BIP / GP BIP)
- RFM(Remote File Management)으로 ADM Key 없이 SIM 파일 원격 업데이트
- PLMN, UST, URSP 등 주요 EF 파일 편집
- 대용량 데이터 자동 분할 전송 (Concatenated SMS)
- REFRESH 미발생 시 자동 Modem Reset + 폴링 복구
- APDU 색상 미리보기 및 실시간 전송 로그

---

## 2. 화면 구성

exe를 실행하면 자동으로 브라우저가 열리며 `http://127.0.0.1:8085`에 접속됩니다.

### 상단 헤더 바

| 영역 | 설명 |
|------|------|
| 📡 SIM OTA 테스트 v1.0.0 | 앱 타이틀 및 버전 |
| 📋 지원 범위 | 칩셋별 AT+CSIM / IMS SMS 지원 현황 팝업 |
| 📱 단말 설정 | Android/iOS USB 설정 가이드 팝업 |

### 좌측 설정 패널 (위→아래)

| 순서 | 영역 | 설명 |
|------|------|------|
| 1 | 단말 연결 | 포트 드롭다운 + 🔄 새로고침 + Connect 버튼 |
| 2 | 연결 정보 | AT/adb 상태 점, IMSI, MSISDN |
| 3 | 전송 방식 | 🔌 AT+CSIM 선택 |
| 4 | 메시지 타입 | 📋 자사 BIP / 🔐 GP BIP / 📂 RFM |
| 5 | 세부 설정 | 타입별 옵션 입력 + APDU 미리보기 |
| 6 | 전송 버튼 | 모든 설정 완료 시 활성화 |

### 우측 실행 로그 패널

| 영역 | 설명 |
|------|------|
| 로그 영역 | 전송 과정 실시간 표시 |
| 📋 복사 | 로그 전체를 클립보드에 복사 |
| 🗑 클리어 | 로그 초기화 |

---

## 3. 디바이스 준비

### Android (Samsung)

1. **개발자 모드 활성화**: 설정 → 휴대전화 정보 → 소프트웨어 정보 → 빌드번호 5회 탭
2. **개발자 옵션 설정**:
   - USB 디버깅 활성화 → USB 연결 → RSA 키 허용
   - 3GPP AT 명령 활성화
3. **USB 모드 변경**: 전화 앱 → `319712358` → 비밀번호 `0821` → USB Setting → **DM + MODEM + ADB**
4. USB 케이블로 PC에 연결

### iOS (iPhone)

Carrier 버전 업그레이드 후:
- 설정 → Carrier Settings → Baseband Manager → Logging Settings
  - **Qualcomm**: Passive, External Hardware (QXDM) — Windows PC 필요
  - **Apple Modem**: Passive, External Hardware (AT Only) — macOS 필요

---

## 4. 연결하기

### 포트 선택

1. USB로 디바이스를 연결합니다
2. **Serial Port** 드롭다운에 사용 가능한 모뎀 포트가 표시됩니다
   - 모뎀 포트가 자동으로 상단에 정렬됩니다
   - ADB가 연결된 경우 포트 옆에 기기 모델명이 `[모델명]` 형태로 표시됩니다
3. 포트가 보이지 않으면 🔄 버튼으로 새로고침합니다

> **참고**: Diagnostic/DIAG 포트는 자동으로 필터되어 목록에 표시되지 않습니다. 모뎀 포트만 선택 가능합니다.

### 연결 과정

**Connect** 버튼을 클릭하면 다음이 자동으로 진행됩니다:

1. 시리얼 포트 열기 및 AT 응답 확인
2. SIM 파일 읽기:
   - EF.IMSI (가입자 식별번호)
   - EF.MSISDN (전화번호)
   - PLMN EF 5종 상위 5개 레코드 (RFM placeholder용)
   - UST 17바이트 (UST 편집용)
3. adb 단말 탐지 및 칩셋 식별

### 연결 성공 시

| 표시 | 의미 |
|------|------|
| `● AT+CSIM` (녹색) | 시리얼 통신 정상 |
| `● adb` (녹색) | adb 연결 정상 |
| IMSI: 450060... | SIM에서 읽은 15자리 가입자 식별번호 |
| MSISDN: +8210... | SIM에서 읽은 전화번호 |

연결 완료 후 **전송 방식**과 **메시지 타입** 섹션이 활성화됩니다.

---

## 5. 전송 방식 선택

Connect 성공 후 활성화됩니다.

| 방식 | 설명 |
|------|------|
| 🔌 AT+CSIM | USB 시리얼 포트로 ENVELOPE APDU 직접 전송 |

> AT+CSIM이 기본이며 유일한 전송 방식입니다. IMS SMS는 개발 모드에서만 사용됩니다.

---

## 6. 자사 BIP

### 용도

자사 BIP(Bearer Independent Protocol) 트리거 메시지를 SIM에 전송합니다. CRC32 RC 기반, TAR=B00006.

### 사용법

1. 메시지 타입 → **📋 자사 BIP** 선택
2. 세부 유형 선택:

| 유형 | 태그 | 용도 |
|------|------|------|
| 개통 (0x02) | tag=02 | 개통 시 BIP 세션 트리거 |
| IMSI변경 (0x05) | tag=05 | IMSI 변경 후 BIP 트리거 |
| 자동국업 (0x03) | tag=03 | 자동 국업 BIP 트리거 |

3. **전송** 클릭

### 결과 판정

| 응답 | 의미 | 판정 |
|------|------|------|
| SW=91xx | proactive 요청 발생 (BIP 채널 오픈 시도) | ✅ 성공 |
| SW=9000 | proactive 미요청 | ⚠️ SIM 미반응 |

---

## 7. GP BIP

### 용도

GP(GlobalPlatform) BIP 트리거 메시지를 전송합니다. 3DES CC(Cryptographic Checksum) 기반, TAR=B20100. OTA Key(KID2)가 필요합니다.

### 사용법

1. 메시지 타입 → **🔐 GP BIP** 선택
2. 세부 유형 선택:

| 유형 | 접미사 | 용도 |
|------|--------|------|
| 개통 (+0200) | bip_tag=32 | 개통 BIP |
| 번호변경 (+0500) | bip_tag=35 | 번호변경 BIP |
| 자동국업 (+0300) | bip_tag=33 | 자동국업 BIP |

3. **IMSI** 확인 — Connect 시 자동 채움됨
4. **KID2** 입력 — 32자리 hex (16바이트 OTA Key)
   - 우측에 입력 카운터 `(N/32)` 표시
   - `test_profile.json` 매칭 시 자동 채움 ([13장](#13-kid2-자동-채움) 참조)
5. **전송** 클릭

### 전송 특성

GP BIP는 OTA 메시지가 168바이트로 단일 ENVELOPE 제한(137바이트)을 초과하므로, 항상 **2-part Concatenated SMS**로 분할 전송됩니다:

```
Part 1 전송 → 0.2초 대기 → Part 2 전송
```

---

## 8. RFM — PLMN 업데이트

### 용도

ADM Key 없이 PLMN 관련 EF 파일을 원격 업데이트합니다.

### 대상 EF

| 메뉴 | EF | 이름 | 필드 |
|------|-----|------|------|
| PLMN | HPLMNwAcT (6F62) | Home PLMN | MCC + MNC + AcT |
| PLMN | EHPLMN (6FD9) | Equivalent HPLMN | MCC + MNC |
| PLMN | FPLMN (6F7B) | Forbidden PLMN | MCC + MNC |
| 로밍SoR | OPLMNwAcT (6F61) | Operator PLMN | MCC + MNC + AcT |
| 로밍SoR | PLMNwAcT (6F60) | User PLMN | MCC + MNC + AcT |

### 사용법

1. **📂 RFM** → **PLMN** 또는 **로밍SoR** 선택
2. EF 파일 선택
3. PLMN 리스트 입력 (상위 5개 행):
   - **MCC**: 3자리 숫자 (예: `450`)
   - **MNC**: 2~3자리 숫자 (예: `06`)
   - **AcT**: 4자리 hex (예: `C0C0`)
4. **🔧 APDU 생성** 클릭 → 색상 미리보기 확인
5. **전송** 클릭

### Placeholder (회색 텍스트)

Connect 시 읽은 현재값이 입력 필드의 placeholder로 표시됩니다. 이는 **참고용**이며 편집 초기값으로 사용되지 않습니다. 사용자가 입력한 값만 UPDATE BINARY 데이터로 사용됩니다.

### 특수 입력

| 입력 | 동작 |
|------|------|
| 빈 행 (미입력) | 해당 레코드 스킵 (변경 안 함) |
| `FFF` 또는 `AAA` | 레코드 삭제 (FFFFFF로 초기화) |

### APDU 색상 미리보기

생성된 APDU가 바이트별 색상으로 표시됩니다:
- 회색: CLA
- 파란색: INS
- 보라색: P1, P2
- 주황색: Lc
- 녹색: Data

### 🗑️ 모두 지우기

입력된 모든 행을 초기화합니다.

---

## 9. RFM — UST 수정

### 용도

USIM Service Table(EF.UST)의 개별 서비스를 활성화/비활성화합니다.

### 사용법

1. **📂 RFM** → **UST** 선택
2. 서비스 드롭다운에서 대상 서비스 선택 (n1~n136)
   - 3GPP 표준 서비스명이 함께 표시됩니다
3. **True**(활성화) 또는 **False**(비활성화) 선택
4. **🔧 APDU 생성** 클릭 → 미리보기 확인
5. **전송** 클릭

### 동작 원리

- 대상 서비스의 비트만 변경하고, 같은 바이트 내 다른 서비스(최대 7개)는 보존됩니다
- Connect 시 캐시된 UST 값을 기준으로 비트 연산을 수행합니다

---

## 10. RFM — URSP 업데이트

### 용도

EF.URSP(5G 라우팅 정책)의 BER-TLV Tag 80 데이터를 업데이트합니다.

### 사용법

1. **📂 RFM** → **URSP** 선택
2. TLV 데이터 입력 (hex, `80`으로 시작)
   - 🔗 **URSP rule** 링크: 외부 TLV 생성/분석 도구로 이동
3. **🔧 APDU 생성** 클릭 → Step 1/Step 2 미리보기 확인
4. **전송** 클릭

### 자동 처리 (3단계 시퀀스)

URSP 업데이트는 SIM 내부 파일 테이블 의존성 때문에 자동으로 3단계로 처리됩니다:

| 단계 | 동작 | 소요 시간 |
|------|------|-----------|
| Step 1 | UST n132 OFF 전송 | ~1초 |
| Modem Reset | AT+CFUN=0/1 + 3초 안정화 | ~5초 |
| Step 2 | UST n132 ON + URSP DELETE + SET DATA 전송 | ~1초 |
| Modem Reset | AT+CFUN=0/1 + 3초 안정화 | ~5초 |

총 소요 시간: 약 10~18초

### 대용량 분할 전송

TLV 데이터가 커서 Step 2의 OTA 메시지가 137바이트를 초과하면, 자동으로 **Concatenated SMS**로 분할 전송됩니다:
- Part 1 → 0.2초 대기 → Part 2 (Modem Reset 없음)
- 모든 파트 전송 완료 후에만 Modem Reset 실행

### 실시간 입력 검증

입력 시 다음이 자동으로 검증됩니다:
- Tag `80` 확인
- BER-TLV Length 파싱 및 Value 길이 일치 확인
- hex 유효성, 홀수 자릿수 검출
- 오류 시 주황색 메시지 표시

---

## 11. RFM — APDU 직접 작성

### 용도

임의의 SIM APDU 명령을 직접 입력하여 RFM envelope으로 전송합니다.

### 사용법

1. **📂 RFM** → **APDU 직접 작성** 선택
2. 텍스트 영역에 APDU hex 입력 (줄바꿈으로 구분 가능)
3. 입력 시 실시간 색상 미리보기 표시
4. **전송** 클릭

### 예시

```
00A4080C022F33
00D600000124
00A4080C047FFF6F61
00D600000525F510C0C0
```

---

## 12. 실행 로그

### 로그 색상

| 색상 | 의미 |
|------|------|
| 🟢 녹색 | 성공 (전송 시작/완료, 메시지 생성) |
| 🔵 파란색 | ENVELOPE 전송/응답 |
| 🟡 노란색 | 경고 (REFRESH 미발생, Modem Reset 실행) |
| 🔴 빨간색 | 오류 |
| ⚪ 회색 | 상세 정보 (raw 데이터) |

### 성공 예시

```
────────────────────────────────────────
[16:06:16.754] 전송 시작: AT+CSIM | 자사 BIP — 자동국업
[16:06:16.755] 메시지 생성 완료: 64 bytes
[16:06:16.769] 메시지 전송
  ENVELOPE 전송
    → AT+CSIM=128,"80C2..."
  ENVELOPE 응답 (proactive 요청)
    ← +CSIM: 4,"911E"
[16:06:17.063] 전송 완료: 0.3초
```

### Modem Reset 예시

```
  ENVELOPE 응답 (REFRESH 미발생)
    ← +CSIM: 4,"9000"
  Modem Reset 자동 실행
    → AT+CFUN=0
    ← OK
    → AT+CFUN=1
    ← OK
    3초 대기...
```

### 분할 전송 예시

```
  ENVELOPE 전송 (1st)
    → AT+CSIM=366,"80C2..."
  ENVELOPE 응답 (정상)
    ← +CSIM: 4,"9000"
   다음 파트 전송 대기...
  ENVELOPE 전송 (2nd)
    → AT+CSIM=192,"80C2..."
  ENVELOPE 응답 (REFRESH 미발생)
    ← +CSIM: 4,"9000"
  Modem Reset 자동 실행
```

### 로그 버튼

- **📋 복사**: 전체 로그를 텍스트로 클립보드에 복사
- **🗑 클리어**: 로그 버퍼 초기화

---

## 13. KID2 자동 채움

### 설정 방법

exe와 같은 폴더에 `test_profile.json` 파일을 배치합니다:

```json
{
  "profiles": [
    {
      "msisdn": "821012345678",
      "kid2": "680EC11FAF3C7DABAC250A7F291C8FBE"
    }
  ]
}
```

### 동작

- Connect 시 SIM에서 읽은 MSISDN과 `test_profile.json`의 msisdn을 매칭
- 매칭 성공 시 GP BIP의 KID2 필드가 자동으로 채워짐
- `+82`, `0` 접두사 모두 정규화하여 매칭

### 파일이 없는 경우

- KID2 자동 채움이 동작하지 않을 뿐, 다른 기능에는 영향 없음
- GP BIP 사용 시 KID2를 수동으로 입력하면 됨

---

## 14. 문제 해결

### 포트가 보이지 않음

- 디바이스가 USB로 연결되어 있는지 확인
- USB 모드가 DM + MODEM + ADB인지 확인
- 🔄 버튼으로 포트 목록 새로고침
- Windows: Samsung USB 드라이버 설치 여부 확인

### Connect 실패: "AT 응답 없음"

- 개발자 옵션에서 3GPP AT 명령이 활성화되어 있는지 확인
- USB를 분리했다가 다시 연결

### 전송 후 SW=6F00

- ENVELOPE 크기 초과 (단일 전송 한계)
- 도구가 자동으로 분할 전송을 시도하지만, 극단적으로 큰 데이터는 실패할 수 있음
- URSP TLV 데이터를 줄여서 재시도

### Modem Reset 후 "phone failure"

- 정상 동작입니다. 도구가 자동으로 폴링 복구를 수행합니다
- AT 폴링 (최대 30회)으로 모뎀 복구를 확인한 후 다음 단계를 진행합니다
- 30회 초과 시 타임아웃 경고가 표시되지만, 대부분 정상 복구됩니다

### URSP 업데이트 실패

- TLV 형식이 올바른지 확인 (Tag 80, BER-Length 일치)
- 🔗 URSP rule 도구로 TLV를 생성/검증하세요
- 입력 검증에서 오류가 표시되면 수정 후 재시도

### 콘솔 창이 닫힘

- 콘솔 창을 닫으면 서버가 종료됩니다
- exe를 다시 실행하세요

---

**© 2026 JUSEOK AHN <ajs3013@lguplus.co.kr> All rights reserved.**
