# Secrets — 이름·발급 절차·교체 주기 (값은 절대 여기 적지 않는다)

설정 위치: 이 저장소 → **Settings → Secrets and variables → Actions → New repository secret**

| 이름 | 무엇 | 권한 범위 | 교체 주기 | 발급일 |
|---|---|---|---|---|
| `DATAGO_KEY` | 공공데이터포털 인증키(katSale) | 그 API 활용신청 계정 | 개발계정 2년(CostApp `docs/API-사용지도.md` 0절(대장 · 2026-10-15 합침)) | 발급일 2026-08-29 |
| `COSTAPP_READ_TOKEN` | foodlog(CostApp) 비공개 저장소를 읽는 fine-grained PAT | **foodlog 저장소만 · Contents: Read-only** | **90일** | 발급일 2026-10-15 (재발급 · 처음 08-29 것은 이름 오타 `COSTAPP_READ_TOKE`로 등록돼 한 번도 안 쓰였다 → 삭제) |
| `TELEGRAM_BOT_TOKEN` | 알림 봇 토큰 | 봇 하나 | 유출 시 즉시 | 발급일 2026-08-29 |
| `TELEGRAM_CHAT_ID` | 알림 받을 채팅 ID | 비밀은 아니지만 Secrets 에 둔다 | — | 발급일 2026-08-29 |

⚠️ 위 표의 **발급일 YYYY-MM-DD** 를 실제 발급한 날로 고쳐 둘 것 — `security.yml` 이 이 날짜로 60일 지난 것을 알린다(90일 만료 30일 전).

## 발급 절차 (클릭 순서)

### 1. `COSTAPP_READ_TOKEN` — fine-grained PAT
1. GitHub 오른쪽 위 프로필 → **Settings** → 왼쪽 맨 아래 **Developer settings**
2. **Personal access tokens → Fine-grained tokens → Generate new token**
3. Token name `foodlog-data-read-costapp` · **Expiration: 90 days**
4. **Repository access: Only select repositories → `foodlog`** 하나만
5. **Permissions → Repository permissions → Contents: Read-only** (다른 건 전부 No access)
6. Generate → 값을 복사해 이 저장소 Secrets 에 `COSTAPP_READ_TOKEN` 으로 저장. 브라우저를 닫으면 다시 못 본다.
7. 이 파일의 발급일을 오늘로 고친다.

### 2. `DATAGO_KEY`
- 공공데이터포털 로그인 → **마이페이지 → 활용신청 현황** → katSale(정산정보) 항목 → **일반 인증키(Encoding)** 값. CostApp `.env` 의 것과 같은 값.

### 3. `TELEGRAM_BOT_TOKEN` · `TELEGRAM_CHAT_ID`
1. 텔레그램에서 **@BotFather** → `/newbot` → 이름·아이디 정하면 토큰이 온다 → `TELEGRAM_BOT_TOKEN`
2. 만든 봇에게 아무 말이나 보낸다(대화 시작).
3. 브라우저에서 `https://api.telegram.org/bot<토큰>/getUpdates` 를 열어 `"chat":{"id":숫자}` 의 숫자 → `TELEGRAM_CHAT_ID`
4. 확인: Actions 탭 → daily → Run workflow → 텔레그램에 요약 한 줄이 오면 끝.

## 교체(회전) 순서 — 유출이 의심될 때도 같다
1. 새 값 발급 → 2. Secrets 에서 같은 이름으로 **Update** → 3. Run workflow 로 성공 확인 → 4. 옛 값 폐기(PAT: Developer settings 에서 Delete · 봇: BotFather `/revoke`) → 5. 이 파일 발급일 갱신.
