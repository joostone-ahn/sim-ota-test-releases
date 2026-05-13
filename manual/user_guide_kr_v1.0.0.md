# SIM OTA Test Tool — 사용자 가이드

> 버전: v1.0.0

---

## 1. 시작하기

### 1.1. 실행

`SIM-OTA-Test.exe`를 더블클릭합니다.
- 콘솔 창이 열리고 서버가 시작됩니다 (닫지 마세요)
- 브라우저가 자동으로 `http://127.0.0.1:8085`에서 열립니다

### 1.2. 사전 준비 (단말 설정)

**Android (Samsung):**
1. 개발자 모드 활성화: 설정 → 휴대전화 정보 → 빌드번호 5회 탭
2. 개발자 옵션: USB 디버깅 ON, 3GPP AT 명령 ON
3. USB 모드 변경: 전화 앱 → `319712358` → 비밀번호 `0821` → USB Setting → **DM + MODEM + ADB**
4. USB 케이블로 PC에 연결

### 1.3. KID2 자동 채움 (선택)

GP BIP 사용 시 OTA Key를 자동으로 채우려면, exe와 같은 폴더에 `test_profile.json`을 배치합니다:

```json
{
  "profiles": [
    {"msisdn": "821012345678", "kid2": "AAAA...32자리hex..."}
  ]
}
```

---

## 2. 화면 구성

화면은 좌측 **설정 패널**과 우측 **실행 로그**로 나뉩니다.

### 좌측 패널 (위→아래 순서)

| 영역 | 설명 |
|------|------|
| 단말 연결 | 포트 선택 + Connect 버튼 |
| 연결 정보 | AT/adb 상태, IMSI, MSISDN, 칩셋 |
| 전송 방식 | AT+CSIM (기본) |
| 메시지 타입 | 자사 BIP / GP BIP / RFM |
| 세부 설정 | 타입별 옵션 입력 + APDU 미리보기 |
| 전송 버튼 | 모든 설정 완료 시 활성화 |

### 우측 패널

- 실행 로그: 전송 과정이 실시간으로 표시됨
- 📋 복사: 로그를 클립보드에 복사
- 🗑 클리어: 로그 초기화

### 상단 메뉴

| 버튼 | 내용 |
|------|------|
| 📋 지원 범위 | 칩셋별 지원 현황 (Qualcomm/Exynos/MTK/Apple) |
| 📱 단말 설정 | Android/iOS USB 설정 가이드 |

---

## 3. 단말 연결

1. 포트 드롭다운에서 모뎀 포트 선택
   - 페이지 로드 시 자동 탐색됨
   - 🔄 버튼으로 수동 새로고침
   - **Modem** 포트를 선택 (Diagnostic/DIAG 아님)
2. **Connect** 클릭

### 연결 성공 시 표시 정보

| 항목 | 설명 |
|------|------|
| ● AT+CSIM (녹색) | 시리얼 통신 정상 |
| ● adb (녹색) | adb 연결 정상 |
| IMSI | SIM에서 읽은 15자리 가입자 식별번호 |
| MSISDN | SIM에서 읽은 전화번호 |
| 칩셋 | MediaTek / Qualcomm / Exynos |

Connect 시 자동 수행:
- SIM 파일 읽기 (IMSI, MSISDN, PLMN EF 5종, UST)
- GP BIP IMSI 필드 자동 채움
- `test_profile.json` 매칭 시 KID2 자동 채움

---

## 4. 전송 방식

Connect 성공 후 활성화됩니다.

| 방식 | 설명 |
|------|------|
| 🔌 AT+CSIM | USB 시리얼로 ENVELOPE 직접 전송 (기본) |

---

## 5. 자사 BIP

BIP 트리거 메시지를 SIM에 전송합니다.

### 사용법
1. **📋 자사 BIP** 선택
2. 세부 유형 선택:
   - 개통 (0x02)
   - IMSI변경 (0x05)
   - 자동국업 (0x03)
3. **전송** 클릭

### 결과 판정
| 응답 | 의미 |
|------|------|
| SW=91xx | BIP 채널 오픈 요청 발생 ✅ |
| SW=9000 | proactive 미요청 (SIM 미반응) |

---

## 6. GP BIP

GP BIP 트리거 메시지를 전송합니다. OTA Key(KID2)가 필요합니다.

### 사용법
1. **🔐 GP BIP** 선택
2. 세부 유형 선택:
   - 개통 (+0200)
   - 번호변경 (+0500)
   - 자동국업 (+0300)
3. **IMSI** 확인 (자동 채움됨)
4. **KID2** 입력 (32자리 hex, 우측에 카운터 표시)
5. **전송** 클릭

### 전송 특성
- 메시지가 크므로 항상 2개 파트로 분할 전송됩니다
- Part 1 → 짧은 대기 → Part 2 순차 전송

---

## 7. RFM (Remote File Management)

ADM Key 없이 SIM 파일을 원격 업데이트합니다.

### 7.1. PLMN 업데이트

1. **📂 RFM** → **PLMN** 선택
2. EF 파일 선택:
   - **HPLMNwAcT**: Home PLMN (MCC + MNC + AcT)
   - **EHPLMN**: Equivalent HPLMN (MCC + MNC)
   - **FPLMN**: Forbidden PLMN (MCC + MNC)
3. PLMN 리스트 입력 (상위 5개 행):
   - MCC: 3자리 숫자 (예: `450`)
   - MNC: 2~3자리 숫자 (예: `06`)
   - AcT: 4자리 hex (예: `C0C0`) — HPLMNwAcT만
4. **🔧 APDU 생성** 클릭 → 색상 미리보기 확인
5. **전송** 클릭

**특수 입력:**
- 빈 행: 스킵 (해당 레코드 변경 안 함)
- `FFF` 또는 `AAA`: 레코드 삭제 (FFFFFF로 초기화)

**Placeholder (회색 텍스트):** Connect 시 읽은 현재값. 참고용이며 편집 초기값이 아닙니다.

**🗑️ 모두 지우기:** 입력된 모든 행 초기화

### 7.2. 로밍 SoR

1. **📂 RFM** → **로밍SoR** 선택
2. EF 파일 선택:
   - **OPLMNwAcT**: Operator PLMN
   - **PLMNwAcT**: User PLMN
3. 이후 PLMN과 동일

### 7.3. UST (USIM Service Table)

1. **📂 RFM** → **UST** 선택
2. 서비스 드롭다운에서 대상 선택 (n1~n136)
3. **True**(활성화) 또는 **False**(비활성화) 선택
4. **🔧 APDU 생성** 클릭
5. **전송** 클릭

> 같은 바이트 내 다른 서비스 상태는 보존됩니다.

### 7.4. URSP (5G 라우팅 정책)

1. **📂 RFM** → **URSP** 선택
2. TLV 데이터 입력 (hex, `80`으로 시작)
   - 🔗 **URSP rule** 링크: 외부 TLV 생성 도구
3. **🔧 APDU 생성** 클릭 → Step 1/Step 2 미리보기 확인
4. **전송** 클릭

**자동 처리 (약 10~18초):**
1. Step 1: UST n132 OFF → Modem Reset
2. Step 2: UST n132 ON + URSP 업데이트 → Modem Reset

데이터가 크면 Step 2가 자동으로 분할 전송됩니다.

**실시간 입력 검증:**
- Tag `80` 확인
- BER-TLV 길이 파싱 및 일치 확인
- 오류 시 주황색 메시지 표시

### 7.5. APDU 직접 작성

1. **📂 RFM** → **APDU 직접 작성** 선택
2. 텍스트 영역에 APDU hex 입력 (줄바꿈으로 구분)
3. 입력 시 실시간 색상 미리보기 표시
4. **전송** 클릭

---

## 8. 실행 로그

### 로그 색상

| 색상 | 의미 |
|------|------|
| 녹색 | 성공 (전송 시작/완료) |
| 파란색 | ENVELOPE 전송/응답 |
| 노란색 | 경고 (REFRESH 미발생, Modem Reset) |
| 빨간색 | 오류 |
| 회색 | 상세 정보 |

### 로그 예시

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

### Modem Reset 발생 시

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

---

## 9. 주의 사항

- 전송 중에는 전송 버튼이 비활성화됩니다 (완료까지 대기)
- URSP 업데이트는 2단계 자동 처리로 약 10~18초 소요됩니다
- Modem Reset 후 3초 안정화 대기 동안 추가 명령 불가
- 포트 선택 시 Diagnostic 포트가 아닌 Modem 포트를 선택하세요
- GP BIP 사용 시 KID2를 모르면 `test_profile.json`을 준비하세요
- 콘솔 창을 닫으면 서버가 종료됩니다

---

**© 2026 JUSEOK AHN <ajs3013@lguplus.co.kr> All rights reserved.**
