# Web E2E 실행 환경

## 서버와 Google OAuth

1. origin은 `http://localhost:3000`, 기본 callback은 `http://localhost:3000/api/auth/callback/google`이다. 서버 포트, BASE_URL, Google client의 등록 redirect URI를 맞춘다. 충돌을 피하려고 3100 서버를 추가하지 않는다.
2. 수동 QA는 사용자가 지정한 서버를 사용할 수 있다. 자동 실행기는 기존 서버를 채택하지 않고 임시 DB에 연결된 실행 소유 Next 서버를 준비한다. 포트 충돌 시 기존 서버를 보존하고 소유권을 확인한 뒤 해결한다.
3. `.env`에서 `TEST_USER_EMAIL`, `TEST_USER_PASSWORD`, `AUTH_GOOGLE_ID`, `AUTH_GOOGLE_SECRET`의 존재만 확인한다. 값은 출력하지 않는다. TEST_USER는 Admin이며 실제 Google 로그인 UI에 사용한다. 일반 계정은 실행기가 생성한 별도 자격증명으로 개발 로그인한다.
4. 관리자 세션의 실제 Admin role과 verified-email을 확인한다. credential 로그인, 가짜 callback/session 또는 임의 role 변경을 Google 성공으로 인정하지 않는다. 추가 인증은 사용자에게 요청하고 기다린다.

## Web 전용 준비

- 실행기는 임시 PostgreSQL·객체 저장소·Next 서버·HTTP/WebSocket proxy·전용 Chrome 프로필/CDP를 준비한다. 휴대폰 조회·ADB·Metro·LAN 설정은 필요 없다.
- `.env.e2e.local`의 선택 설정: `VAULT_E2E_WEB_PORT`(3000), `VAULT_E2E_CDP_PORT`(9223). `VAULT_E2E_CHROME_PATH`는 프로세스 환경에서 Chrome 실행 파일을 지정할 때 사용한다. 두 포트는 서로 달라야 하며 시작 전에 비어 있어야 한다.
- Argent로 Chromium을 조작하기 전에 관련 스킬을 읽고 현재 CDP 대상을 확인한다. 사용자 Chrome 프로필이나 다른 실행의 CDP 서비스를 종료하지 않는다.
- 전체 여정 하나에서 일반 계정 하나를 유지한다. 관리자만 별도 계정이다. 최종 qualification은 동일 소스의 전체 여정을 새 DB/계정으로 두 번 통과해야 한다.
- 이미지 업로드는 제외한다. 파일 선택기·Computer Use·사진 fixture를 준비하지 않는다. IMAGE 저장 및 PROGRESS 계약은 Vault/API suite에서 유지한다.

## 속도를 높이는 순서

선행 서버/OAuth 조건을 먼저 확인하고 기존 단편을 재사용해 10단계를 연결한다. 변경·실패·새 의심이 없는 개별 흐름을 반복하지 않는다. 단계별 UI 저장/재진입과 canonical 결과를 함께 기록한다. 제품 문제가 나오면 서버 로그와 실제 오류 원인을 확인해 고치고 영향을 받은 범위부터 재검증한다.

순서는 `web-free-memo`, `admin-google-licenses`, `regular-license-redemption`, `quest-season-license`, `market-purchase-all`, `web-paid-diary`, `diary-shuffle`, `market-ownership`, `diary-apply-thirty`, `quest-ten-rewards`다. 날짜·점수·잔액을 직접 조작하지 않는다. Quest 행동과 보상 수령은 정상 UI로 수행한다.

## 실행과 판정

`.argent/flows/vault/journey-manifest.json` version 3은 위 9개 stage의 Chromium live-recorded flow만 허용한다. 시간 제한은 1분~6시간이며 각 flow는 launch와 실제 check를 포함한다. 정적 run dependency까지 해시를 고정한다. 이전 version 2나 native flow를 재사용하여 전체 통과로 처리하지 않는다.

`VAULT_E2E_RECORD=1`은 Web 녹화용 환경을 유지하는 내부 옵션이다. 부분 재생/녹화 환경은 최종 두 번 검증을 대체하지 않는다. 현재 10단계 manifest와 live-recorded 흐름이 준비되어도, 새 환경 전체 자동 재생과 두 번 qualification은 별도로 확인해야 한다. manifest 존재를 실행 성공으로 추정하지 않는다.

첫 fresh 재생은 무료 Memo 동작 555단계가 통과했으나 idle 경고 6건으로 코드 2였고 후속 단계는 실행되지 않았다. 다음 실행 전 [첫 fresh 재생 기록](lessons.md#첫-fresh-전체-재생--기능-통과와-안정화-증거-부족을-구분)을 확인한다. 이 결과는 과거 strict 판정이다. 현재 판정은 SKILL.md의 최신 사용자 기준을 따른다.

`VAULT_E2E_DIAGNOSTIC_CONTINUE_IDLE_WARNINGS=1`은 내부 기능 진단 옵션이다. UI 보고서의 유일한 부족이 idle 경고이고 canonical 저장 검증도 성공한 경우에만 후속 단계를 실행한다. UI 실패/오류·미확인 skip·다른 경고·canonical 실패는 계속 차단한다. 보고서의 해당 단계는 `incomplete`/code 2이며, 전체 실행도 code 2다. 두 번 qualification 성공으로 세지 않는다. 기본 세 명령에는 이 옵션을 설정하지 않는다.

Quest 날짜 선택은 실행 시작 시 호스트의 현재 월을 기준으로 직전 달 1–21일의 실제 날짜 라벨을 고정한다. `quest-calendar.json`에 anchor·timeZone·selector bindings를 기록한다. 날짜·시계는 주입하지 않으며 월이 바뀐 실행은 환경 실패로 중단한다. 연도 경계·윤년 binding은 주력 suite에서 검증하고 실제 다른 달 UI의 qualification 여부는 실행 보고서로 확인한다.

자격증명·라이선스 원문은 비공개 실행 저장소/Argent secret으로 전달하며 종료 시 복구·삭제한다. 실행 소유 서버·DB·프로필만 정리한다. [해결 기록](lessons.md)의 Web 관련 항목을 먼저 읽는다. 과거 모바일 기록은 현재 준비 지침이 아니다.

전용 Chrome은 startup의 `--window-size=1200,1271`로 창 크기를 고정한다. 현재 Mac/Chrome에서 실제 content viewport1200×1184(DPR1)를 확인했으며 Argent의¼ idle capture가 정수 크기가 된다. 다른 OS/Chrome에서는 toolbar 크기가 다를 수 있으므로 실제 geometry를 다시 확인한다. Electron에는 이 flag로 viewport를 보장하지 않는다. readiness 경고를 숨기거나 pixel threshold를 낮추는 설정이 아니다.

기본 E2E Chrome은 `--disable-gpu`로 소프트웨어 렌더링을 사용한다. 하드웨어 합성에서 정적 background/blur와 도장 주변의 작은 capture pixel 변화가 재현됐고, 소프트웨어 합성·래스터화를 실제 확인한 fresh 로그인100회/300단계에서는 경고0이었다. UI/canonical checks는 유지하며 passing localized pixel 경고는 최신 사용자 기준에 따라 별도 보고한다. 이 suite는 GPU 가속·하드웨어 렌더링 성능의 검증이 아니다. qualification에는 진단용 capture instrumentation이나 Chrome wrapper를 넣지 않는다.
