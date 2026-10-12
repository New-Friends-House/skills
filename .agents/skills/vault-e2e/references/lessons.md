# Vault E2E 시행착오와 해결 기록

## 컷오버 초기화 제거와 사전 검사 오류 허용 후 check (2026-10-11, 전체 suite 검증 완료)

- 기본 서랍·Catalog 초기화 단계 제거, 검사 오류가 있는 컷오버 시작 허용, 누락 서랍의 메모 Block ID 보고 변경 후 `$check`의 `fix → typecheck → pnpm test:vault:suite`가 모두 종료 코드 0으로 통과했다. 타입 검사는 21/21, 새 격리 환경의 주력 suite는 5개 파일 77/77, 실패·pending·todo 0, Vitest 17.18초였다. 보고서: `.cache/vault-e2e/2026-10-11T07-33-06-568Z-c668638d/suite.json`, 같은 실행의 `summary.json`.
- `fix`는 `vault-cutover-console.tsx`와 관리자 번역 `admin.ts`의 줄바꿈만 정리했다. 마지막 `fix`부터 suite 종료까지 5,193개 추적·비무시 파일의 구성과 hash가 같았고, 진단에 따른 제품·테스트 수정과 환경 정리 오류는 없었다. 기존 suite 통과는 새 오류 허용 분기의 전용 회귀 검증이나 실제 508건의 데이터 오류 해결을 뜻하지 않는다. 운영 DB·배포·Web UI·모바일 동작은 검증하지 않았다.

## pnpm 패치 문맥 공백과 check 범위 (2026-10-11, 전체 suite 검증 완료)

- `$check`의 `fix → typecheck → pnpm test:vault:suite`는 모두 종료 코드 0이었다. 타입 검사는 21/21 작업, 새 격리 환경의 suite는 5개 파일 77/77, 실패·pending·todo 0, Vitest 18.18초였다. 보고서: `.cache/vault-e2e/2026-10-11T05-11-45-381Z-db26da41/suite.json`, 같은 실행의 `summary.json`. 마지막 `fix` 이후 suite 종료까지 5,193개 추적·비무시 파일의 구성과 hash가 같았고 환경 정리 오류는 보고되지 않았다. 진단에 따른 수동 코드 수정은 없었으며 Web UI·모바일 동작은 검증하지 않았다.
- 추가 `git diff --check`는 이미 변경된 `patches/react-native-keyboard-controller@1.22.6.patch`의 공백 25건으로 종료 코드 2였다. 해당 줄은 모두 단일 공백으로 표현한 unified diff의 빈 문맥 줄이었고 패치 hash는 검사 시작부터 같았다. 패치 형식을 보존하고, 이 추가 diff 진단을 세 게이트의 결과와 구분해 보고한다. 일반 소스의 후행 공백이나 패치 추가 줄의 공백과 같은 경우로 취급하지 않는다.

- clean 작업 트리의 후속 `$check`도 세 게이트 종료 코드 0, 타입 검사 21/21, 주력 suite 5개 파일 77/77로 통과했다. 보고서: `.cache/vault-e2e/2026-10-11T06-15-15-035Z-8d65e51b/suite.json`, 같은 실행의 `summary.json`. 마지막 `fix`부터 suite 종료까지 5,194개 추적·비무시 경로의 구성과 hash가 같았고, 진단에 따른 제품·테스트 수정과 환경 정리 오류는 없었다. `git diff --check`는 현재 Git 변경분을 검사하므로, clean 작업 트리에서의 종료 코드 0은 앞선 패치 문맥 공백을 수정했다는 증거로 사용하지 않는다.

## Row store 교체 검토와 주력 suite (2026-10-10, 전체 suite 검증 완료)

- `$check`의 `fix → typecheck → pnpm test:vault:suite` 연속 실행은 모두 종료 코드 0으로 통과했다. `fix`는 `vault-scope-record.ts` 타입 선언의 줄바꿈만 정리했고 `typecheck`는 21/21 작업이 성공했다. 새 격리 환경의 suite는 5개 파일 77/77, 실패·pending·todo 0, Vitest 16.02초였다. 보고서: `.cache/vault-e2e/2026-10-10T08-51-04-359Z-63b7f422/suite.json`, 같은 실행의 `summary.json`. 마지막 `fix` 이후 suite 종료까지 소스 hash 변경이 없었고 환경 정리 오류와 미해결 진단도 없었다. 진단을 위한 제품·테스트 수정은 필요하지 않았으며 Web UI·모바일 동작은 검증하지 않았다. 과거 통과 수치 대신 매 실행의 보고서와 소스 변경 여부를 확인한다.
- 후속 미커밋 작업 트리의 `pnpm test:vault:suite`도 새 격리 환경에서 5개 파일 76/76, 실패·pending·todo 0, Vitest 15.83초, 종료 코드 0으로 통과했다. 보고서: `.cache/vault-e2e/2026-10-10T08-32-39-925Z-cc5ca175/suite.json`, 같은 실행의 `summary.json`. 실행 전후 소스 hash 변경과 환경 정리 오류가 없고, 진단을 위한 제품·테스트 수정은 필요하지 않았다. 별도 요청한 `fix`와 `typecheck`도 통과했지만, 이 API suite 결과는 Expo의 화면 보관 시간·스크롤 복원·메모리 감소나 Web UI 검증을 대신하지 않는다.
- 미커밋 row store 변경에서 `pnpm test:vault:suite`를 실행해 새 격리 환경의 5개 파일, 71/71, 실패·pending·todo 0, 138.81초, 종료 코드 0을 확인했다. 보고서: `.cache/vault-e2e/2026-10-10T06-54-42-421Z-fd3049d7/suite.json`, 같은 실행의 `summary.json`. 환경 정리 오류는 보고되지 않았으며 진단을 위한 제품·테스트 수정은 필요하지 않았다.
- 기존 suite는 Provider 사용자 전환·구독, 태그/관계 삭제, optimistic 저장·롤백을 검증했다. 당시 소스는 변경된 테이블 전체 행을 비교하고 그 테이블의 모든 reader를 알렸으므로, 이 최초 통과는 변경분 적용이나 버킷별 알림 횟수의 증거가 아니었다.
- 후속 구현은 shell `rowChanges`와 명령/rollback의 실제 변경 ID를 row store로 전달하고 필드/값 버킷만 알린다. 직접 패치된 shell 행은 다음 assembly에서도 복구·삭제해야 하므로 index에 patched ID를 남기고 진행 중인 preparation을 무효화한다. Query 교체 시 완전한 seed가 필요하며, 잘못된 delta를 거절한 뒤에도 다음 delta의 기준이 달라지므로 다음 publication은 해당 테이블을 다시 seed해야 누락된 삭제가 복구된다.
- 기존 `read-models.test.ts`에 버킷 격리·이동·삭제, 여러 테이블 적용 후 알림, snapshot identity, 마지막 구독 해제 후 정리, 직접 optimistic patch·rollback·재조립, 활성 Query 교체, 잘못된 행 수신 후 복구, 준비 중 invalidate 회귀를 추가했다. 새 격리 환경의 전체 suite 5개 파일 76/76, 실패·pending·todo 0, 133.77초, 종료 코드 0을 확인했다. 보고서: `.cache/vault-e2e/2026-10-10T07-14-32-274Z-d5103597/suite.json`, 같은 실행의 `summary.json`. 앞선 보강 중간 실행 74/74 보고서는 `.cache/vault-e2e/2026-10-10T07-10-31-966Z-24f7b341`에 보존한다.
- 행 저장소의 delta 적용과 무관한 구독 미통지를 검증한 결과이며 전체 publication 성능 측정은 아니다. scope patching·shell 조립·Query structural sharing 비용은 남는다. Web·실기기 UI 및 메모리·성능 측정은 실행하지 않았다. TanStack DB 의존성과 retirement wrapper 제거 후에도 이 경계 테스트를 유지한다.

## V1 Market fixture 정리와 관련 조회 재시작 검증 (2026-10-07, 전체 suite 재검증 완료)

- 첫 `pnpm test:vault:suite`는 68/71, 종료 코드 1이었다. 실패 증거는 `.cache/vault-e2e/2026-10-07T02-08-34-403Z-495eced2/suite.json`과 같은 실행의 `summary.json`에 보존한다.
- V1 Market 회귀가 만든 공개 GRID/BACKGROUND 상품을 정리하지 않아, 뒤의 Market 전체 목록과 여정 catalog snapshot 검증 두 건이 실패했다. User 격리만으로 공유 Catalog는 격리되지 않는다. 기존 `vault.test.ts`에서 생성 전에 두 상품 ID의 `onTestFinished` 정리를 등록했다. 이후 테스트의 전체 목록·가격·소유권·원장 검증은 유지한다. 공개 상품 fixture를 추가할 때는 성공·실패 양쪽에서 자신이 만든 상품을 정리한다.
- 조회 회귀의 `[2, 2]` 기대는 무관한 페이지도 재조회하던 이전 동작이었다. 현재 [C4 계약](../../../../docs/devs/vault-memory-round4.md)은 변경된 row와 selector에 관련된 조회만 재시작한다. 기존 `read-models.test.ts`에 변경된 페이지 상세 조회를 추가하고, 저장 전 오래된 제목·목록/해당 페이지 각 2회·무관한 페이지 1회·최종 cache/Collection의 최신 제목 보존을 함께 검증했다. 현재 계약과 대조한 테스트 보완이며 제품 코드는 변경하지 않았다.
- 새 격리 환경의 전체 suite는 5개 파일, 71/71, 실패·pending·todo 0, 164.37초, 종료 코드 0으로 통과했다. 보고서: `.cache/vault-e2e/2026-10-07T02-12-10-707Z-7706a89a/suite.json`, 같은 실행의 `summary.json`. 실행기 소유 환경 정리 오류는 없었다. Web E2E 및 repo-wide fix/typecheck는 실행하지 않았다. 이 결과는 API suite 증거이며 Web maintenance 해제나 전체 UI qualification 증거가 아니다.
- 2026-10-08 현재 작업 트리에서 `pnpm test:vault:suite`를 새 격리 환경으로 재실행해 5개 파일, 71/71, 실패·pending·todo 0, 132.57초, 종료 코드 0을 확인했다. V1 Catalog fixture 정리와 관련 조회 재시작 검증을 포함해 새 실패가 없어 제품·테스트 수정은 필요하지 않았다. 보고서: `.cache/vault-e2e/2026-10-08T06-31-55-373Z-5f54e242/suite.json`, 같은 실행의 `summary.json`. 환경 정리 오류는 보고되지 않았다. 이번 범위는 주력 suite만이며, Web maintenance 해제나 모바일 책장 배경 변경의 성능·표시 검증으로 해석하지 않는다.

## 셔플 추가 후 전체 qualification 완료 (2026-10-05)

- `pnpm test:vault:all`의 fresh 실행 `2026-10-04T19-05-56-602Z-db3401ec`은 종료code0이다. 기존5개 파일의 주력61/61, 실패·pending0과 Web 전체10단계 두 번의 UI·canonical이 모두 통과했다. 이전 실패 보고서는 수정하거나 삭제하지 않는다.
- pass1은3751 pass/실패·오류0/조건부skip68, pass2는3765 pass/실패·오류0/조건부skip66이다. 정확한 passing localized pixel 진단만26/9건이며 다른 경고는0이다. guard가 요구하지 않는 준비·탐색·추천 장식 정리만 생략했고 필수 acceptance 및 canonical10개는 각각 모두 실행됐다.
- 두 fresh 환경 모두 셔플 전후 페이지·Daily 정체성, 본문·태그 hash, 모양 변경과 새 소유 Sticker 저장·재진입을 확인했다. 각각 새 스티커4/3개, GRID3/BLOCK10/STICKER17의 장식30개 및 Quest7700점·잔여400점·보상1~10의 실제 지급을 검증했다.
- 일반 User·실행 ID·Chrome profile이 서로 다르고 전체10단계의 sourceHash/sources가 동일하다. 시작·종료 소스 스냅샷1682개는 파일 구성과 hash까지 일치한다. Chrome profile 제거, 소유 Web/CDP의 IPv4/IPv6 포트 해제, private fallback/credential lease 정리를 확인했고 Argent 종료는 사용한chromium-cdp-9223만 범위 지정했다.
- 결과: `.cache/vault-e2e/2026-10-04T19-05-56-602Z-db3401ec/summary.json`, 각 `pass-{1,2}/app.json`·`journey-canonical.json`, `qualification-proof.json`·`source-snapshot.json`. repo-wide fix/typecheck와 모바일/이미지 업로드/RevenueCat/운영 DB/배포는 실행하지 않았다. 아래 기록의 partial 결과는 당시 관측이며 이 fresh 전체 실행이 후속 완료 증거다.

## 장식용 페이지의 준비 순서와 날짜 버튼 혼동 (2026-10-05, 부분 검증 완료)

- fresh all `2026-10-04T18-14-04-382Z-b43dd4e3`은 suite61/61, pass1 전체10단계 UI·canonical이 통과했다. UI3761 pass/0 fail/0 error, 조건부skip44, 정확한 passing localized pixel 진단12건이었다. 장식30개 저장 및 Quest7700점/보상1~10도 확인했다. pass2 앞8단계는 통과했지만 장식96 pass/1 fail로 전체code1이며 최종 qualification은 아니다.
- `qa-vault-web-decoration-board-diary`가 먼저 paid 페이지를 찾고 `diary-next-page`를 무조건 눌렀다. 이 ID는 마지막 페이지의 다음 날짜에도 재사용된다. 세 준비 페이지의 순서가 달라지면 paid 뒤에 board가 없고 다른 날짜로 이동한다. paid 선택 경로만 고친 이전 수정은 이 별도 호출자에 충분하지 않았다.
- fresh authoring `2026-10-04T18-59-03-432Z-ba4004fb`에서 정상 Memo 작성·Admin 발급/일반 등록과 실제 세 페이지를 준비했다. 녹화한 이전/다음 페이지 왕복과 board 제목을 바탕으로 page-labelled 버튼만 사용해 첫 페이지로 정규화하고 최대 두 번 목표를 찾는다. 실제 board 제목 확인은 모든 guard 밖의 필수 acceptance이며 다른 날짜로 이동하지 않는다. 특정 페이지 순서나 paid 페이지 재선택은 요구하지 않는다.
- 수정 fragment는16 pass/0 fail/0 error/조건부skip8/경고0으로 통과했다. 부분 녹화는 전체 qualification을 대신하지 않는다. 소유 재현 환경 및 private export fallback을 정리하고 동일 소스의 fresh 전체 두 번을 다시 판정한다. 이전 pass1 성공과 pass2 실패 보고서는 보존한다.

## Quest 연속 완성 포인트 버튼의 viewport 준비 (2026-10-05, 부분 검증 완료)

- fresh all `2026-10-04T17-34-50-394Z-ae95000b`은 suite61/61과 셔플·장식30개를 포함한 앞9단계 UI·canonical이 통과했다. 장식435/0, GRID3/BLOCK10/STICKER17 모두 실제 저장됐다. Quest는21일 실제 노트·Checklist 준비와 daily/checklist 포인트 수령 후932 pass/1 fail로 첫3일 연속 포인트 버튼을 찾지 못했다. pass2는 미실행이며 보고서는 보존한다.
- 기존 Quest suite에 정상 Vault memo.move→page.prepare-diary로 지난달1~3일 연속 활동을 만들고 finishedCount1/claimable1/totalPoints0을 확인하는 회귀를 추가했다. source timestamp/points 직접 수정은 없다. suite `2026-10-04T18-06-17-233Z-b348a92d`61/61로 조건 계산은 이 경로에서 정상임을 확인했다.
- fresh authoring `2026-10-04T18-03-56-524Z-04c526fa`의 정상 Admin 발급/Plus·Quest 등록·시작과 Web1~3일118/0 뒤 실제 tree에3일 연속300포인트 버튼이 높이0으로 존재했다. viewport 준비가 빠진 flow 실패다. 실제 scroller를 아래로 이동한 뒤 높이0.029의 버튼 visible→claim→hidden→board ready를 녹화하고 take를 마쳤다. 첫3일 연속 await 앞에 그 목표의 scroll-to down만 추가한다.7회/3회 연속수령,21일 활동 및7300-point 실제10보상 검증은 줄이지 않는다.
- 부분 녹화/조건 회귀는 두 fresh 전체 qualification이 아니다. 다음 all에서 같은 소스의 전체10단계 두 번과 실제 포인트/보상 원장을 확인한다.

## 장식 Sticker 컬렉션의 고정 왼쪽 anchor 제거 (2026-10-05, 부분 검증 완료)

- fresh all `2026-10-04T17-01-41-820Z-ab8a14b6`은 suite61/61, 셔플 포함 앞8단계 UI·canonical을 통과한 뒤 장식292 pass/1 fail이었다. sample02의 sn26-sticker$03 왼쪽 검색이 끝까지 가도 목표를 찾지 못했다. Quest 보상/pass2는 실행되지 않았다. 실패 증거는 그대로 보존한다.
- 실제 UI의 ProductCatalogView는 Intl.Collator(en, numeric:true)로 컬렉션/스티커를 정렬한다. fresh authoring `2026-10-04T17-20-10-360Z-71ccf5a7`에서 정상 구매 후 첫 컬렉션 ev26-sticker$01, 현재 sample01 sk26-sticker$01보다 오른쪽에 sn26-sticker$03이 있음을 관측했다. 임의 표본마다 그 고정 anchor로 왼쪽 복귀하는 옛 경로가 틀렸다. picker를 닫는 제품 실패가 아니다.
- sample 바인딩의 STICKER만 실제 UI의 컬렉션→Item 숫자 순서로 정렬한다. 기존 runner 테스트에 collection$2→collection$10 회귀를 추가했고 suite `2026-10-04T17-28-49-462Z-25d3da7e`61/61로 검증했다. 다른 종류 바인딩 순서와30개 표본/권한/저장 검사는 유지한다.
- recorder에서 왼쪽 복귀 없이 sample01/02 선택·저장 각7/0을 실행하고 take를 마쳤다. 나머지 sample03~17은120 pass/0 fail/0 error/0 skip으로 재생됐으며 passing localized pixel 진단은 별도다. 이후 기존16개 fragment에서 obsolete 왼쪽 검색만 제거했다. 장식30개 canonical 및 전체 두 fresh qualification은 다음 all로 판정한다.
- 긴 Market MCP 호출은300초 응답 제한을 넘어 최종 보고서를 받지 못했고 같은 환경의 이미 구매한 첫 상품을 다시 열었다. 이를 통과로 세지 않는다. read-only QueryCache로 남은 새나곰02~07 및 잔액94,700을 확인하고 실제 UI의 잔여6개 구매132/0을 재생했다. 긴 전체 qualification은 기존 CLI json-stream 실행기의 단계 최종 보고서로 판정한다. private export secret fallback은0600과 소유 hash로 정리한다.

## 셔플 canonical의 Catalog DTO 필드와 Prisma 관계 (2026-10-05, 검증기 수정 후 재검증 중)

- `2026-10-04T16-34-19-322Z-c038f1cc`은 suite61/61과 무료/발급/등록/Quest이용권/Market/paid Diary의 canonical6단계가 통과했다. paid111/0, 셔플UI26/0 뒤 새 셔플 canonical은 code2였다. 날짜/페이지 label로 구분한 재선택은 전체 여정에서 통과했다.
- 새 verifier의 ItemWhereInput에 화면용 DTO의 artworkUrl을 잘못 사용했다. 실제 Prisma Item은 artworkImageId와 ManagedImage 관계를 가지며 artworkUrl은 공개 카탈로그 참조에서 파생된다. 저장된 관계 artworkImageId not null을 확인하도록 수정했다. 저장 검사를 삭제하거나 해당 canonical을 skip하지 않는다.
- 기존 paid-evidence 테스트의 no-op 거절은 shape 검증에서 끝나 candidate query를 실행하지 않아 잘못된 필드를 놓쳤다. 소유 STICKER5개에 실제 ManagedImage 관계 fixture를 만들고 실제 sampler→Vault page/sticker operations→verifyDiaryShuffle의 성공 경로를 추가했다. 내용·기존 비어 있지 않은 BlockTag·페이지 정체성 hash, 새 sticker ID를 검증한다. 소유지만 artwork 관계가 없는 Item으로 하나를 교체한 음성 경로는 owned artwork 검증에서 거절해야 한다. 테스트 파일은 새로 만들지 않았다.
- fixture는 격리 DB의 Catalog artwork metadata이며 사용자 이미지 업로드/운영 DB/외부 파일 저장 검증을 추가하지 않는다. fresh suite/all 및 두 번 qualification은 후속 보고서로 판정한다. 앞선 code2 로그는 그대로 보존한다.

- 성공 경로 suite `2026-10-04T16-53-17-144Z-a1086128`60/61 및 항목별 진단 `2026-10-04T16-59-03-316Z-de84302a`60/61에서 persisted position이 center임을 확인했다. interpret-sticker는 같은 parent의 top 추가 시 기존 top을 center로 옮겨 최상단 한 개를 유지한다. 모든 sampled top이 그대로 저장된다는 verifier 가정이 잘못이었다. 정확히 한 top과 나머지 center를 검증하고, 실제 sampler의 ID·Item·좌표·회전·크기가 서버 저장 결과와 같은지 성공 경로에 추가했다. 이 수정의 fresh all은 후속 판정이다.

## 새 paid 페이지의 첫 위치와 재선택 (2026-10-05, 부분 재검증 완료)

- 후속 fresh `2026-10-04T16-10-29-265Z-a3f8b6f0`도 suite61/61과 앞5단계 canonical 뒤 paid101/1로 실패했다. 무료 Diary 진입으로 원래 Memo가 먼저 준비되는 전체 여정은 원래 페이지가 먼저이며, 모든 QUICK를 처음 함께 준비한 최소 경로와 순서가 다르다. 이전 수정의 first/previous 가정은 전체 여정에 충분하지 않았다.
- `PageNavigation`의 diary-previous-page/next-page id는 양끝의 이전/다음 날짜 버튼에도 쓰인다. id만 탭하지 않고 실제 다음 페이지/이전 페이지 role=button label을 guard+tap에 사용한다. 원래 Memo 제목일 때만 해당 방향의 page-labelled 버튼을 선택하고, 이동 뒤 대상 제목의 필수 acceptance는 모든 guard 밖에 유지한다. navigation idle 뒤 original guard를 재확인해 이미 도착한 대상에서 역방향으로 돌아가지 않는다.
- fresh authoring `2026-10-04T16-26-17-587Z-ac86c838`에서 무료 Diary guard, 정상 Plus/Token 등록, 원래 Memo 준비, 새6종 생성91/0 및 실제 두 페이지 next/previous를 녹화했다. 같은 준비 순서의 수정 재진입은23/0(조건부skip8), 셔플·저장·재진입은34/0(조건부skip8)였다. 필수 제목/저장 검사는 모두 실행됐고 page-labelled 버튼만 눌렀다. 기존 runner 테스트에는 실제 nested guard의 설명된 skip을 수용하되 guard 종료 뒤 orphan skip을 계속 거절하는 회귀도 추가했다. 전체 두 fresh pass는 별도 판정한다.

- `2026-10-04T15-45-19-837Z-5bfeb0c1`은 suite61/61, 무료700, Admin75와 등록/Quest이용권/Market 전체의 UI·canonical5단계가 통과한 뒤 paid Diary98 pass/1 fail이었다. 현재 Chrome 잔존 문제는 이 실행에서 재발하지 않았고 다운로드 canonical도 통과했다. 종료 후9223 listener가 없어 소유 profile 교체 프로세스 정리까지 확인했다.
- fresh authoring `2026-10-04T16-01-00-210Z-34a26dcb`의 원래 Memo+Plus등록+6종 paid-content 최소 경로에서도98 pass/1 fail로 동일 재현했다. tree는 전체2페이지의2번째, 제목Vault journey memo를 보여줬다. 새 paid Memo는 첫 페이지에 자동 준비되어 있었다. 기존 재선택의 무조건 next는 이미 열린 대상에서 원래 Memo로 이동시켰다. 캡처 지연이나 Block 저장 실패가 아니다.
- 실제 previous→paid 제목 visible을 녹화하고, 원래 Memo 제목이 보일 때만 이전 버튼을 누르는 guard로 polish했다. required paid 제목 확인은 guard 밖에 유지한다. 이미 paid/원래 Memo 두 진입의 수정 fragment는14 pass/0 fail, 조건부skip2였고, Memo→Diary 재진입은15 pass/0 fail, 조건부skip4였다. 셔플 전체 UI fragment는26 pass/0 fail, 조건부skip4였다. 모두 unknown/실제 실패가 없고 branch가 요구하지 않는 tap만 skip됐다. 두 fresh 전체 qualification은 별도이다.
- authoring exported-license fallback은 앱 공유 .env.local에서 ARGENT_SECRET_ 접두사가 필요하다. 없는 경우에만0600으로 생성하고 hash가 소유 값과 같을 때 제거한다. raw 값/credentials는 기록하지 않는다. unknown secret·이미 로그인된 상태에서 login-prefix 재실행의 부분 실패도 final qualification으로 세지 않는다.

## 녹화 Chrome 잔존과 CDP IPv4/IPv6 분리 (2026-10-05, 실행기 수정 후 재검증 중)

- `2026-10-04T15-31-47-459Z-6f7041c0`의 suite60/60, 무료 Web700/0, Admin UI75/0 뒤 canonical이 code2였다. 계정 chooser의 조건부 skip은 정상이고, 새 라이선스의 소유권/발급 수 검사까지 통과했다. private download 검증 오류가 일반 canonical 오류 문구로 가려져 알려진 안전한 InfrastructureError만 JourneyEvidenceError로 전달하도록 보완했다.
- 소유 녹화 환경 종료 뒤 Argent flow의 launch가 만든 교체 Chrome PID가 남았다. 같은9223의 IPv4는 이전 프로필, IPv6는 새 프로필로 분리되어 이전 프로필에 export3개/새 프로필에0개가 있었다. 다음 녹화의 source materialization까지 이전 프로필을 선택했다. 이전 환경이 완전히 정리되었다는 메모만으로 새 브라우저 격리를 판정하지 않는다.
- runner preflight는 wildcard0.0.0.0 대신127.0.0.1과::1을 각각 확인한다. cleanup은 원래 자식뿐 아니라 정확히 이 실행이 만든 user-data-dir와 실행 파일이 일치하는 교체 프로세스도 재확인 후 종료하고, 프로세스가 사라진 뒤 profile을 삭제한다. 현재 세션이 만든 이전 PID는 저장된 materialized flow의 profile과 대조한 뒤 종료했다. 기존 사용자 서버/다른 profile은 종료하지 않는다.
- 기존 runner 테스트에 실제 교체 프로세스 종료·다른 profile 보존·반복 정리와 각 loopback 포트 충돌 거절을 추가했다. fresh suite/all 및 두 번 qualification은 이후 보고서로 판정한다. 앞선 code2와 raw evidence는 보존한다.

## 시간표의 24시간 팝오버 경로 변경 (2026-10-05, 부분 재검증 완료)

- fresh `all` `2026-10-04T15-21-00-557Z-1435fb40`은 suite60/60 뒤 무료 Web364 pass/1 fail, passing localized pixel 진단8건이었다. 태그 회귀 구간은 통과했고 Time table의 옛 오전 버튼이 없어 실패했다. 현재 StartEndTimeEditDialog는 시간 설정 dialog 안의 시작/종료 버튼과 별도24시간 Popover를 쓴다. 기존 시작 시간 visible은 trigger 문구에도 맞으므로 picker가 열린 증거가 아니다.
- 소유 authoring `2026-10-04T15-26-13-590Z-956896e9`에서 시작/종료 Popover, 시/분 Select, Escape→popover hidden→idle, 확인과09:00–10:00 저장을 새로 녹화했다. raw dynamic id/value를 실제 관측한 role/label/동일 dialog scope로 polish했다. 분 설정 전 시 선택은 현재 분을 보존하므로 interim09:00 대신09: prefix를 확인하고 분00 뒤 exact09:00을 검사한다. 초기 실패 녹화는 별도 보존했다.
- 무료 Time table과 paid Diary가 같은 `qa-vault-web-time-table-clock.yaml`을 재사용한다. 오전/오후를 되살리거나 시간 기대값을 삭제하지 않았다. 수정된 무료 전체 fragment는101 pass/실제 실패·skip0, passing localized pixel 진단2건이었다. 저장·체크·목록·재진입·trash의 기존 검증도 통과했다. 전체 all 및 두 fresh pass는 별도 실행한다.

## 재진입 태그 인덱스의 삭제 알림 누락 (2026-10-05, 수정 후 재검증 중)

- `2026-10-04T15-06-56-029Z-b86065d7`의 fresh `all`은 suite59/59 뒤 무료 태그 해제에서89 pass/1 fail이었다. 별도 fresh authoring `2026-10-04T15-10-46-638Z-39f68d90`의 동일 prefix도89 pass/1 fail로 재현됐다. 실제 tree 좌표의 체크박스와 label 모두 클릭해도 selected가 남았으며, 네트워크의 block.tags.set([]) 성공/서버 count0와 Collection·scope의 빈 관계를 확인했다. 공유 인덱스만 이전 selected를 반환했다.
- 설치된 TanStack DB의 기본 subscribeChanges는 전달하지 않은 키의 delete를 거른다. 두 태그 인덱스는 .values()로 기존 행을 seed하지만 구독의 includeInitialState 옵션을 생략했다. seed된 행을 이미 읽었다는 계약인 includeInitialState:false를 세 구독에 명시했다. 입력/대기 selector나 서버 저장 결과 검사는 유지한다.
- 기존 read-models 테스트 파일에 실제 Collection에서 기존 태그/관계를 seed 후 삭제하는 회귀를 추가했다. 수정 전 fresh suite `2026-10-04T15-16-05-673Z-d88a8985`는 정확히 해당 selected가 true로 남아59/60 실패였다. 수정 후 실제 Web 재진입·해제의 tag-unselected와 chip hidden을 recorder로 확인했다. 수정 후 fresh suite `2026-10-04T15-18-56-058Z-b0528978`은60/60(115.17초) 통과했고, 재진입·해제 이후 원래 태그 흐름의 재부착·사전 삭제 suffix도26 pass/실패·경고0이었다. 새 all 두 번 qualification은 별도 판정한다.

## 셔플 및 최신 read scope 커버리지 보강 (2026-10-05, 전체 재검증 중)

- 새 `all`의 suite 초기 실패는 compact scope 계약/18종 scope 등록과 Quest license fixture 누락이었다. 외부 Memo 확인은 다른 소유자의 데이터를 반환하지 않으며 요청자 소유의 빈 summary와 삭제 tombstone을 반환한다. 전체 등록 schema와 실제 실행 scope를 대조하고 외부 row·tag 유출 거절을 유지했다. API Season 토큰 구매 회귀와 Web 장식 구매 계약은 별도 fixture로 검증한다.
- 기존 테스트 파일에 sampler의 다른 모양·새 artwork·잠긴/이미지 없는 Item 제외·단일 모양·fallback·빈 catalog, 실제 DecorateDraft→Vault 저장의 연속 셔플·정체성/내용 보존·거절 rollback·다음 저장 복구, active gesture가 끝난 뒤 save coalescing·재시도를 보강했다. 새 테스트 파일은 만들지 않았다. `.cache/vault-e2e/2026-10-04T14-55-59-932Z-8ae47813/`의 suite는59/59(115.08초), 이후 fresh `all`의 suite도59/59(113.31초)였다.
- 소유 authoring 환경 `2026-10-04T14-51-55-937Z-295bb4eb`에서 실제 Admin Google 로그인·Quest 키 발급/export·일반 User 등록·미보유 모바일 안내·보유자 시작을 녹화했다. 보유자 버튼은 옛 `quest-purchase-or-start` 대신 `quest-start-owned`이다. 실패 raw wait는 보존하고 새 성공 wait만 정식 흐름에 넣었다. 셔플 및 일반 메뉴 재진입도 live recorder로 저장했다. 부분 녹화는 qualification이 아니다.
- manifest와 appJourney는 `quest-season-license` 및 새 `diary-shuffle`을 포함한10단계다. 셔플은 앞선 paid-content canonical과 레이아웃·새 소유 artwork·위치/크기/회전·페이지/블록/스타일을 비교한다. Web 시즌 Token 구매가 빠진 소유권 UI 잔액은94,100이며 API 가격/구매 회귀는 유지한다.
- `2026-10-04T15-04-00-592Z-df5074c3`은 suite59/59 뒤 Quest flow preflight가 code2였다. 성공 live wait를 raw `tool: await-ui-element`만으로 남기면 실행기의 정식 `await`/`assert` 검사 수를 만족하지 않는다. recorder의 cross-tree 검증을 통과한 wait를 `await: visible/hidden`으로 polish했다. fresh 전체 두 번 결과는 이후 보고서로 확인한다.
- authoring에 사용한 임시0600 secret fallback은 hash 일치 확인 후 삭제했고 소유 record runner는 종료했다. 키 원문은 스킬/커밋/보고서에 기록하지 않는다. 개인 메모리·운영 DB·repo-wide fix/typecheck는 수정/실행하지 않았다.

## Quest 모바일 전용 판매와 기존 Web 구매 계약 (2026-10-04, 모바일 전용 유지 결정·전체 재검증 중)

- 수정 뒤 fresh `all` 실행 `.cache/vault-e2e/2026-10-03T15-20-37-369Z-924b9be8/`은 suite 55/55(99.04초), 무료 Web 688/Admin 65/일반 등록 34 및 각 canonical을 통과했다. 무료 passing localized pixel 진단은 4건이다. Quest 구매에서 index 2 `quest-purchase-or-start`가 없어 2 pass/1 fail로 code 1 종료했다. 뒤 5단계와 pass 2는 미실행이다. 이전 Note/첫 블록/ColorTracker 문제는 이 fresh 무료 단계에서 통과했다.
- 원인은 캡처 지연이 아니다. 커밋 `8a8d61c4`(2026-10-03)가 `Welcome`의 Web 구매 연결/marker를 제거하고 inert 영역과 모바일 전용 안내로 바꿨다. 기존 Web 구매·시작/Token 원장 계약과 의도적인 제품 정책이 충돌한다. `docs/devs/vault-app-e2e-plan.md`의 Problem 1-Pager에 근거와 두 접근을 기록했다. 판매를 임의 재개하거나 라이선스를 Token 구매 증거로 대체해 녹색으로 만들지 않는다. 정책 결정과 이후 전체 qualification은 미완료다.
- 정책 선택을 기다리며 별도 소유 authoring 환경 `.cache/vault-e2e/2026-10-03T15-28-51-842Z-2a628564/`에서 정상 관리자 발급/일반 등록 UI로 Plus·Token을 준비했다. Market 59개 구매 fragment는 `market-independent-diagnostic.stream`에서 1,252 pass, 실패·skip 0, code 0이다. passing localized pixel 진단 1건은 별도로 보존한다. Quest 구매를 하지 않았으므로 정식 여정의 93,100 잔액/전체 canonical/qualification 성공 증거가 아니다. 기존 소유권 단계의 고정 잔액을 이 환경에서 그대로 실행하지 않는다.

## 유료 Diary 연속 삽입과 focus 표시 회귀 (2026-10-04, 부분 검증)

- 위 authoring 환경의 최초 paid fragment는 text/Highlight 저장 뒤 index 14의 두 번째 `memo-insert-block` tap에서 14 pass/1 fail로 종료했다. 실패 요약과 당시 tree는 `paid-diary-initial-insert-failure.json`, `paid-diary-initial-insert-screen.json`에 보존했다. 실제 textarea는 focused지만 두 삽입 버튼 opacity가 모두 0이었다. 커밋 `8d6c0e0b`의 카드 스타일 변경이 기존 `group-focus-within/block:opacity-100`을 제거한 것을 확인했다. 스타일·hover·focus-visible을 유지하고 focus-within 표시 조건 한 곳을 복원했다. 기대값이나 버튼 검사를 삭제하지 않는다.
- owned localhost proxy에서 열린 화면은 source 변경 이후에도 옛 class를 유지했다. 실제 URL 재진입 후 Highlight를 focus한 live recorder와 polished `paid-insert-focus-live.yaml` 2 pass로 버튼 표시를 확인했다. 코드 저장 직후 기존 화면의 wait가 실패한 증거도 보존하며 HMR 반영을 가정하지 않는다. 기존 paid 내용·Diary 재진입 검증 및 전체 fresh qualification은 계속 필요하다.
- 이어진 `paid-diary-resumed.stream`은 연속 삽입/Clock까지 통과한 뒤 구매한 컬렉션이 많아 `sticker-collection-sn26-sticker$01`이 수평 viewport 밖인 문제로 35 pass/1 fail이었다. `sticker-picker-collections`의 실제 wheel 경로를 recorder로 확인한 뒤 기존 paid flow에 해당 container의 `scroll-to direction: right`를 추가했다. fixed Sticker/종류/본문 기대값은 유지했다.
- 정상 UI로 실패 Note를 휴지통에 넣고 수정된 paid fragment 전체를 처음부터 다시 재생했다. `paid-diary-independent-diagnostic.stream` 및 `paid-diary-full-replay.json`은 72 pass, 실패·경고 0, code 0이다. 추가 authoring draft가 없어서 기존 조건부 분기 두 곳의 4개 step이 명시적으로 skip됐다. 중간 복구 없이 6종 작성·저장과 Diary 두 번 재진입 UI가 확인됐으며 정식 단계 순서의 canonical 저장 검증이나 fresh 전체 두 번 qualification을 대신하지 않는다. 이후 정책 결정이 필요해 owned authoring 환경을 정리했다.

## Web Note 문구 변경과 기존 Memo selector (2026-10-04, fresh 무료 단계 통과)

- `pnpm test:vault:all` 실행 `.cache/vault-e2e/2026-10-03T14-58-55-776Z-734695dc/`에서 주력 5개 파일/55개 테스트가 모두 통과했다. Web 첫 단계는 11 pass 뒤 index 11의 `작성된 메모가 없어요` visible 검사에서 실패했고 전체 code 1로 종료했다. 후속 8단계와 pass 2는 미실행이다. 첫 로그인·Chromium 연결은 성공했으므로 과거 CDP 연결 장애를 이번 원인으로 사용하지 않는다.
- 현재 `shared.memo.empty/choose/namedMenu/deletePermanentlyConfirm` 및 실제 Web 호출자는 노트 문구를 사용한다. 기존 흐름 7개에 남은 빈 목록·선택 안내·행 메뉴·영구 삭제 selector 23곳을 같은 의미의 현재 문구로 갱신했다. positive/negative·dialog scope·삭제·저장·재진입 검증은 유지하고 제품 copy를 되돌리지 않았다. 이 수정의 전체 통과는 아직 미확인이다.
- 실패 보고서는 위 실행의 `pass-1/web-free-memo.json`, `pass-1/app.json`, `summary.json`에 보존했다. 현재 환경 검사 JSON은 같은 실행의 `project-environment.json`이다. 다음 실행은 현재 번역 키와 호출자를 확인하고 동일한 변경이 적용되는 연결 흐름까지 점검한다. 수정 뒤에는 새 DB/계정으로 `all` 및 Web 두 번 qualification을 재검증한다.
- 문구 수정 뒤 새 `all` 실행 `.cache/vault-e2e/2026-10-03T15-02-17-472Z-7bc753b0/`은 주력 55/55였고 무료 Web에서 246 pass 뒤 index 248의 Highlight 목록 본문 검사가 실패했다. 문구 관련 앞선 경로는 실행됐지만 나머지 무료 기능·후속 8단계/pass 2는 미완료다. 정확한 passing localized pixel 진단 1건은 이 실제 실패와 별도로 보존한다.

## 첫 블록 미리보기와 아래 추가 경로 (2026-10-04, fresh 무료 단계 통과)

- 위 Highlight 실패를 fresh authoring `.cache/vault-e2e/2026-10-03T15-06-35-235Z-61e2e224/`에서 기존 녹화 prefix로 재현했다(53 pass/1 fail). 실제 tree·화면은 블록 2개와 첫 빈 텍스트의 `내용을 적어보세요.`를 표시했고, 재진입한 두 번째 Highlight의 본문은 정상 저장돼 있었다. `memo-insert-block`은 이제 아래 추가를 수행하고 목록은 rank 순 첫 블록을 미리보기로 표시한다. 저장 실패나 목록 캐시 지연으로 분류하지 않는다.
- 무료 5종 canonical verifier는 해당 종류의 블록 1개를 요구한다. 실제 빈 첫 텍스트에 `/` 입력 → 같은 종류 메뉴 → Highlight 선택 경로를 recorder로 녹화했다. 첫 블록 교체·본문 저장·목록 scoped 미리보기·재진입·휴지통 뒤 없음이 최종 진단 재생 28 pass/실패·경고 0으로 확인됐다. 기존 Highlight/Checklist/TimeTable/Sticker/ColorTracker의 첫 메뉴 진입만 녹화된 `/` 입력으로 바꿨고, 기존 내용·목록·저장·재진입·삭제 검사와 canonical 개수 기준은 유지했다.
- 진단의 반복 `memo-row` tap은 recorder가 raw 좌표로 남겼으므로 제목을 더한 semantic selector로 polish하고 완전 재생했다. 최종 진단 YAML에는 raw 좌표가 없다. raw take, 실패 재현 출력, 수정 진단 YAML/출력은 위 authoring 디렉터리에 보존하며 manifest에는 진단 흐름을 넣지 않는다. 5종 전체 재생과 새 환경 `all`/두 번 qualification은 아직 진행 중이다. 아래 추가가 첫 블록을 교체한다고 가정하거나 canonical 개수를 2개로 완화하지 않는다.

## ColorTracker 선택 버튼과 24시간 팝오버 (2026-10-04, fresh 무료 단계 통과)

- 위 authoring 환경의 5종 fragment 재생은 Highlight/Checklist/TimeTable/Sticker를 통과한 뒤 307 pass에서 ColorTracker의 `선택한 색상 #FFF1AD` image 검사에 실패했다. 현재 `ColorSwatch` 내부 아이콘은 `aria-hidden`이며 선택 의미는 `ColorSwatchButton`의 `aria-pressed`에 있다. 실제 DOM에서 기본 노랑만 true임을 확인했다. `ColorPalette`에서 같은 selected 비교로 `color-palette-selected-${color}` marker를 노출하고 기존 6곳의 선택·해제·재진입 검사를 버튼 marker로 연결했다. 기본 노랑 visible → 초록 선택 visible → 노랑 hidden을 live recorder와 resumed replay에서 확인했다.
- 이어진 실패는 옛 `시간` tabpanel/오전 selector다. 현재 ColorTracker는 24시간 시작/종료 Popover를 쓴다. 시작을 09로 먼저 바꾸면 현재 종료 02:00의 경계에 clamp되어 01:59가 되므로 기존 경계 정규화 자체는 유지해야 한다. 24시간 `00` → 종료 `10:05` → 시작 `09:05`를 실제 UI로 녹화하고 해당 normalize/parent 경로를 갱신했다. 열린 시작 팝오버는 종료 버튼 위를 덮으므로 Escape → 해당 dialog hidden → idle 뒤 다른 필드를 연다. 마지막 확인 전에도 팝오버를 닫는다. 시간/색상/목록 본문/재진입/편집/삭제 기대값은 유지한다.
- Recorder는 겹친 Popover의 combobox 좌표를 뒤쪽 swatch/동적 Base UI id로 오인했다. 실제 describe의 combobox와 dialog 이름으로 scope한 selector로 polish한다. 실패한 live waits 2개와 raw take는 `color-clock-live-raw.yaml`, 최초 실패는 `free-five-blocks-replay.stream`, `color-selection-resumed.stream`에 보존한다. 저장 이후 경로는 `color-clock-save-resumed.stream`에서 57 pass, 실패·경고·skip 0으로 확인됐으며 전체 ColorTracker와 fresh `all`/두 번 qualification은 아직 미완료다.
- 최종 ColorTracker fragment 전체는 같은 authoring 환경의 `color-tracker-full.stream`에서 171 pass, 실패·경고·skip 0, code 0으로 확인했다. 앞선 4종 성공과 합쳐 무료 5종 수정 경로는 부분 재생 검증됐으며 fresh `all`/두 번 qualification은 별도로 진행한다.

## 주력 suite 명령 coverage 누락 (2026-10-03, 전체 suite 재검증 완료)

- 현재 작업 트리에서 `pnpm test:vault:suite`를 실행했다. 실행기 소유 임시 PostgreSQL·객체 저장소에서 5개 파일, 54/55 테스트가 통과했으며 97.17초 후 종료 코드 1이다. pending/todo는 0이고 환경 정리 오류는 보고되지 않았다. 보고서: `.cache/vault-e2e/2026-10-03T06-50-38-530Z-f863c3f0/suite.json`, 같은 실행의 `summary.json`.
- 실패는 `Coverage manifest accounts for every included command and read scope`의 명령 목록 비교다. 계약의 `vaultOperationTypes`에는 `block.file-to-diary`가 있지만 suite의 actor 호출·실행 기록에는 없다. 테스트 소스에서도 해당 호출이 없음을 확인했다. 해당 명령의 제품 동작 실패를 재현한 것은 아니며, 현재 suite의 검증 누락이다.
- 사용자 요청에 따라 스킬 본문과 기본 호출 문구에 실패 수정·선택 범위 전체 재검증까지 완료하는 계약을 명시했다. 기존 태그 테스트에 실제 `block.file-to-diary` 호출을 추가하고 저장된 부모·본문·태그 보존, 원본에서 제거/대상에서 조회, canonical rank 순서, 구독 중인 Collection 반영, 태그 삭제 후 두 Block 관계 정리를 검증했다. coverage gate와 기존 검증은 유지했고 제품 코드는 수정하지 않았다.
- 보완 과정에서 요청 `order: 2`를 저장 mirror 값으로 기대한 새 검증이 실패했다. 서버는 rank 순으로 0부터 mirror를 정규화하므로 두 번째 Block은 `order: 1`이다(ADR 0011/0045). 이를 확인하고 canonical rank 순서도 검사했다. Collection은 화면 구독자가 없는 actor에서 임시 조회 구독이 종료되면 Query 변경 알림을 구독하지 않았다. 단순 polling만으로 해결되지 않았고, 이동 검증 동안 `subscribeChanges`를 유지해 실제 소비자 조건에서 반영을 기다리고 `finally`에서 해제하도록 보완했다. 실패 증거: `.cache/vault-e2e/2026-10-03T06-54-41-897Z-542d76df/suite.json`, `.cache/vault-e2e/2026-10-03T06-56-53-165Z-19ea4fee/suite.json`, `.cache/vault-e2e/2026-10-03T06-58-51-818Z-ed8071ed/suite.json`.
- 최종 새 격리 환경의 `pnpm test:vault:suite`는 5개 파일, 55/55 테스트, 98.01초, 종료 코드 0으로 통과했다. 실패·pending/todo는 0이고 환경 정리 오류는 보고되지 않았다. 최종 보고서: `.cache/vault-e2e/2026-10-03T07-01-09-153Z-e23189b0/suite.json`, 같은 실행의 `summary.json`. 다음 실행도 현재 소스의 명령 coverage와 소비자 구독 조건을 확인하고 과거 결과를 현재 통과 증거로 사용하지 않는다. Web E2E와 repo-wide fix/typecheck는 실행하지 않았다. 첫 실행의 pnpm 자동 의존성 준비 과정에서는 postinstall workspace 검사와 Prisma Client 생성이 실행됐다.

## 주력 suite 단독 실행 (2026-10-02)

- 현재 변경된 작업 트리를 보존하고 `pnpm test:vault:suite`를 실행했다. 실행기 소유 임시 PostgreSQL·객체 저장소에서 5개 파일, 55/55 테스트가 105.94초에 통과했으며 최종 종료 코드는 0이다. 실패·미실행 테스트와 환경 정리 실패는 보고되지 않았다.
- 보고서: `.cache/vault-e2e/2026-10-02T02-56-07-358Z-6706f7b9/suite.json`, 같은 실행의 `summary.json`. 새 장애나 제품 수정은 없었다. Web 전체 여정과 repo-wide fix/typecheck는 실행하지 않았다. 다음 실행도 기존 DB를 채택하지 않고 새 격리 환경을 사용하며, 이 API 결과를 Web UI 통과로 해석하지 않는다.
- 사용자 요청으로 현재 작업 트리에서 새 격리 환경을 만들어 다시 실행했다. 5개 파일, 55/55 테스트가 102.25초에 통과했고 최종 종료 코드는 0이다. 실패·미실행·환경 정리 오류는 보고되지 않았다. 보고서: `.cache/vault-e2e/2026-10-02T03-55-42-037Z-9a29e437/suite.json`, 같은 실행의 `summary.json`. 두 실행 사이 작업 트리 변경이 있으므로 변경 없는 동일 소스의 반복 검증으로 해석하지 않는다. Web E2E와 repo-wide fix/typecheck는 이번에도 실행하지 않았다.

## 현재 판정 기준 (2026-10-02)

사용자는 최종 답변에서 “픽셀 경고는 별도 보고, UI 기능·저장 검증으로 합격 판정”을 선택했다. passing idle의 정확한 localized pixel-change 진단만 분리한다. 원인을 hover로 일괄 단정하지 않는다. 다른 warning 종류/트리·캡처 증거 부족/실제 검증 실패/미확인 skip은 계속 차단한다. 기능·목적지·canonical 검증을 유지하고 경고 원문과 개수를 저장한다. 과거 strict 보고서의 code 2와 미실행 상태는 그대로 남긴다.

## Web 전용 전환 (2026-10-01)

- 사용자 결정으로 모바일 E2E를 모두 제외했다. 현재는 Web 9단계, manifest version 3, 두 번의 새 DB/일반 계정 qualification이다. 휴대폰·ADB·Metro·LAN 조건을 요구하지 않는다.
- 기존 모바일 및 Web 교차 기기 flow와 mobile verifier/adapter를 제거했다. 모바일 제품 기능은 보존한다. Google OAuth의 localhost:3000, 전체 실행당 일반 계정 하나, 선행 장애 우선 해결 및 단편 반복 최소화는 유지한다.
- 9단계 manifest 녹화·구성은 완료했으며 장식 진입 실패 수정·Quest 보상 fresh 실행과 경고 해소·전체 두 번 qualification이 남았다. API 회귀 성공을 전체 Web 성공으로 보고하지 않는다. 아래 모바일 기록은 변경 전의 증거이며 재실행 준비 지침이 아니다.

- 모바일 전용 login route와 E2E backend/run ID 조건도 제거했다. 일반 개발용 자격증명 로그인과 모바일 제품 기능 수정은 유지한다.
- Web-only preflight 회귀는 `--version`만 지원하는 가짜 Argent CLI로 검증한다. 기기 조회/ADB/Metro 요청이 있었다면 실패하는 조건이며, Chromium 9단계 manifest와 Web/CDP 포트 충돌 검사를 함께 확인한다. 실제 CLI의 app 명령은 미녹화 manifest를 종료 코드 2로 보고했다: `.cache/vault-e2e/2026-10-01T04-45-11-723Z-529f3816/summary.json`.
- 첫 회귀 실행은 52/53 통과였다. 모바일 단계 삭제 후 통합 fixture의 checkpoint 기대 범위가 이전 6단계로 남아 실패했다. 현재 Market 완료까지의 5단계로 수정했다. 단계 수 변경 시 선언뿐 아니라 순서·blocked 위치·canonical checkpoint 기대값을 함께 확인한다. 실패 증거: `.cache/vault-e2e/2026-10-01T04-44-56-144Z-25513614/suite.json`. 아래 과거 모바일 증거와 섞어 전체 Web 성공으로 보고하지 않는다.

- 수정 후 주력 suite는 53/53, 5개 파일, 99.69초, exit 0으로 통과했다: `.cache/vault-e2e/2026-10-01T04-46-53-160Z-ce979535/suite.json`. 이전 56개에서 제거한 3개는 모바일 host·ADB reverse·물리 기기 선택 테스트다. Web 계약 및 Vault/API 회귀는 유지했다. 스킬 quick validation과 `git diff --check`도 통과했다. repo-wide fix/typecheck와 Web 전체 UI qualification은 실행하지 않았다.

## 등록·구매 후 reconciliation 완료 대기 (2026-10-01)

- Plus 등록 뒤 preview가 hidden이어도 `redeem.onSuccess`의 소유권/잔액/등록 내역 갱신은 진행 중이었다. 이때 key input이 disabled라 다음 Token 입력이 모두 무시됐고 preview 검사는 실패했다. 현재 key가 비어 있고 확인 버튼이 disabled이며 Token preview 요청도 없었던 것을 관측했다.
- 실제 form의 `busy`가 끝났을 때만 `license-registration-ready`를 노출한다. 다음 key 입력 전에 이 marker를 기다린다. 정상 Token preview→등록→preview hidden→ready와 같은 일반 User의 잔액 100,000/Plus canonical을 확인했다. Enter만으로 preview 요청이 생기지 않은 경우는 실제 “키 확인하기” 클릭을 유지한다. 공유 tool-server는 CLI의 추가 secret 환경변수를 받지 않으므로 소유 secret lease 또는 hash로 복구하는 임시 0600 파일을 사용한다.
- Quest 구매 receipt의 성공 문구는 구매 API 완료를 뜻하며 Market reconciliation 완료를 뜻하지 않는다. busy 동안 ProductDialog는 닫힘을 무시한다. 기존 Quest fragment에는 `market-purchase-ready` 대기가 빠져 Escape 이후 dialog hidden 검사에 실패했다. 실제 ready→close→Quest 시작→board 도착과 1,000 Token 차감 canonical은 확인했다. 최종 fragment의 안정적 닫기 selector 검증과 fresh 두 번 qualification은 남았다.
- 공용 Dialog의 닫기 버튼에는 sr-only span만 있어 Argent가 버튼 이름을 읽지 못했다. 같은 번역 문구의 aria-label 및 `dialog-close` marker를 추가했다. 서로 겹치는 Dialog는 해당 dialog container로 scope한다. raw coordinate 복구를 최종 QA 통과로 인정하지 않는다.

## Google 이메일 입력 누락 (2026-10-01)

- 새 전용 Chrome의 관리자 OAuth에서 Google이 “이 계정을 찾을 수 없습니다.”를 표시했다. 비공개 설정은 15글자, 실제 input 값은 14글자였으며 둘 다 정상 이메일 형식/ASCII였다. 계정 자격증명이 틀렸다고 단정하지 않고 실제 입력 누락을 확인했다. origin은 localhost:3000이고 OAuth 자체가 열렸으므로 redirect mismatch가 아니었다.
- 실제 입력창 포커스와 100ms 입력 간격으로 다시 입력했더니 비밀번호 단계와 실제 Google 인증을 통과했다. 빈 이메일 입력창부터 다시 녹화해 `identifierId` focus, 입력, 비밀번호 identity, password focus를 최종 prefix에 반영했다. Google 계정 chooser의 “다른 계정 사용”은 optional setup으로만 처리하며 인증 완료 검사는 계속 필수다.
- 라이선스 발급/다운로드 22개 step 및 로그아웃 12개 step은 CLI에서 경고 없이 통과했다. 실제 Google Account·Admin role·verified email·4개 발급 키·다운로드·관리자 session 제거의 canonical 검증도 통과했다. 새 prefix의 전체 fresh qualification과 이후 7단계는 별도 증거가 필요하다. 실패·부분 복구 증거는 `.cache/vault-e2e/2026-10-01T11-23-29-904Z-f502ff9b/`에 보존한다.

## 재개 시 보존해야 하는 작성 상태 (2026-10-01)

- 이전 녹화 실행의 PID·DB·Chrome profile은 중단 정리 후 유효하지 않다. 재개할 때 포트와 secret lease 소유자를 다시 확인하고 새 전용 실행을 준비한다. 남은 단계를 이전 PID에 연결하지 않는다.
- Memo의 삽입 버튼은 hover 상태에만 의존하면 키보드로 Block을 작성한 뒤 보이지 않을 수 있다. focused Block에서도 버튼을 보이게 하는 `group-focus-within/block:opacity-100`을 적용한 뒤 실제 네 번 연속 삽입을 확인했다. IME 조합 때문에 Tab focus가 예상과 달라질 수 있으므로 Tab 횟수로 삽입 버튼을 찾는 대안을 최종 흐름으로 채택하지 않는다.
- Diary 배경 목록은 중첩 scroller다. 화면 밖 SKU를 section 제목으로 scope하면 목표 대신 바깥 스크롤이 움직일 수 있다. 실제 ScrollShadow에 `diary-block-backgrounds`를 추가했다. 해당 container를 target.within으로 지정한 재생 검증은 아직 남았으며, CDP page target이 사라져 실행하지 못한 시도를 성공으로 기록하지 않는다.
- 무작위 Sticker의 컬렉션은 slug 문자열을 추측해 만들지 않는다. 구매한 후보의 실제 Item→ItemColumn 관계를 읽어 canonical sample의 `columnSlug`에 넣고 `__VAULT_SAMPLE_STICKER_NN_COLUMN_SLUG__` selector에 바인딩한다. 새 컬렉션 바인딩 회귀를 포함해 주력 suite 53/53, exit 0: `.cache/vault-e2e/2026-10-01T11-26-23-783Z-6ce9585d/suite.json`. 실제 30개 적용과 전체 Web qualification을 의미하지 않는다.
- 재개 실행의 무료 Web 여정은 CLI에서 555개 step을 수행하고 canonical 무료 Memo 검증을 통과했다. idle 경고 6건이 남아 전체 qualification으로 인정하지 않는다. 증거: `.cache/vault-e2e/2026-10-01T11-23-29-904Z-f502ff9b/qa-vault-web-free-journey.yaml.stream`. 경고 대상 화면의 움직임과 다음 준비 상태를 확인하고 해결한 뒤 최종 두 번 검증한다.

## Web-only 여정 연결 중 확인한 원인 (2026-10-01)

- 새 실행 `2026-10-01T04-50-56-192Z-2b3cb6a0`에서 무료 Memo, 실제 Admin Google OAuth/발급/다운로드/로그아웃, 같은 일반 계정의 Plus·100,000 Token 등록, 현재 Quest 시즌 구매·시작을 정상 UI로 진행했다. Market 59개 실제 구매, 유료 Diary 6종 저장·새로고침 재진입, 전체 구매 소유권까지 순서대로 일곱 canonical checkpoint가 읽기 전용 package/API 검증을 통과했다. 녹화 중 복구가 있었으므로 이것은 전체 qualification 성공이 아니다. 부분 흐름 결과와 실패/경고는 `.cache/vault-e2e/2026-10-01T04-50-56-192Z-2b3cb6a0/web-authoring-progress.json`, 각 `*-authoring-canonical.log`에 남긴다.
- 발급 API가 성공해도 다운로드 버튼은 화면 아래에 있을 수 있다. issued 영역 존재 확인 → export로 `scroll-to` → visible 확인 → 다운로드/닫기를 사용한다. timeout 증가로 화면 밖 버튼 문제를 해결하지 않는다.
- 라이선스 activate 후 입력창은 계속 보이지만 onSuccess가 나중에 값을 지운다. 다음 키를 즉시 입력하면 이전 완료 처리와 경합한다. 이전에 visible로 확인한 preview가 hidden이 된 뒤 다음 키를 입력한다. 이 환경에서 keyboard Enter만으로 preview 요청이 발생하지 않은 경우가 있으므로 실제 확인 버튼을 클릭하고 preview를 확인한다. 등록 내역과 canonical 잔액으로 완료를 구분한다.
- `await-ui-element`의 입력 요소 text equals는 AX 라벨/값 투영 때문에 실제 올바른 값에도 실패했다. 원본 Memo의 DOM 값과 canonical 저장값은 정확한 기대값이었다. UI 본문 contains/제어된 목록 제목과 canonical 정확한 내용을 함께 검증한다. 컨테이너 text는 live tool과 runner의 집계 범위가 다를 수 있으므로 올바른 leaf selector 또는 등록 결과를 기록한다. 미충족 live wait를 성공으로 간주하지 않는다.
- Quest 구매 후 영수증 팝업은 자동으로 닫히지 않는다. 완료 문구와 `market-purchase-ready`를 확인한 뒤 Escape, dialog hidden, 시작 버튼, welcome hidden 순서로 진행하고 실제 새 Quest progress를 검증한다.
- Market 카드가 일부만 화면에 들어오거나 popup 종료와 겹치면 다음 클릭은 성공으로 보고돼도 다음 상품이 열리지 않을 수 있다. exact 이름 + button + market-screen scope, selector 기반 scroll-to와 구매 완료/ready/닫힘을 공통 fragment로 연결한다. 첫 상품 구매 후 기존 흐름은 다음 상품 진입에 실패했다. 수정 fragment로 남은 57개를 실제 구매하고 총 59개 소유권과 원장을 검증했다. MCP의 긴 nested 실행은 300초 timeout 이후에도 계속 진행했고 최종 step report를 받지 못했으므로 전체 UI 통과로 판정하지 않는다. 긴 qualification은 실행기의 CLI JSON stream과 종료 코드를 확인한다.
- 수동 녹화용 별도 CLI process의 ARGENT_SECRET 환경변수는 공유 tool-server에 전달되지 않았다. 전체 실행은 소유 secret lease의 add를 사용한다. 이번 수동 연장에만 canonical로 검증한 다운로드 키를 새 0600 임시 `.env.local`에 노출하고 파일 hash를 기록했다. 기존 파일을 덮어쓰지 않으며 소유 내용이 그대로일 때만 제거한다. 값·키 원문은 보고서나 commit에 남기지 않는다.
- 장식 표본은 canonical 소유권 checkpoint의 실행/User/seed를 확인하고 30개의 고유 ID·slug·종류별 개수를 검증한 뒤 selector slot에 바인딩한다. 부족하거나 미해결 slot이면 UI 실행 전에 실패한다. 이 코드의 회귀를 포함해 suite 53/53, exit 0: `.cache/vault-e2e/2026-10-01T05-05-28-764Z-c76c60dd/suite.json`. 30개 실제 적용 및 Quest 10보상, 전체 manifest/두 번 fresh qualification은 여전히 미완료다.


- 실제 삭제 중 scope 읽기의 불완전 graph 500을 기존 lifecycle 회귀에서 정상 영구 삭제 command와 읽기를 interleave하여 재현했다(수정 전 52/53). root scope 읽기는 RepeatableRead snapshot으로 묶고 기존 transaction caller는 재사용한다. Prisma 7 transaction 객체에도 $transaction이 있으므로 $connect 함수가 있는 root만 새 transaction을 시작한다. 수정 후 53/53, exit 0: `.cache/vault-e2e/2026-10-01T05-24-22-660Z-9f8b7df8/suite.json`. 기존 transaction 안의 실제 읽기와 Web 새로고침도 확인했으며 새 전체 qualification은 남았다.
- Diary 생성 확인 버튼은 자식 span 문구만 있고 버튼 AX 이름이 없어 text+button wait가 실패했다. 실제 문구와 같은 aria-label 및 diary-create-confirm을 추가한 뒤 생성·편집·저장·재진입을 확인했다. 두 memo-body 요소는 역할/이름으로 구분하고 checked 상태가 바뀌는 checkbox는 해당 항목의 label로 찾는다.
- 스타일 옵션의 안정적 DOM id와 기존 selected test marker를 같은 버튼에 두면 Argent AX는 DOM id만 노출한다. selected marker를 부모로 분리하고 실제 tree에서 둘 다 확인했다. SKU별 스타일/블록 배경/스티커/스티커 모음 식별자를 사용한다. 키 이름은 arrow-right이며 arrowright는 지원되지 않는다. calendar의 키 이벤트 결과만으로 날짜 이동을 성공 처리하지 않는다.


## 과거 범위의 기록

## 최신 범위와 실기기 실행 기록 (2026-10-01)

- 이미지 업로드는 주력/API와 Web·모바일 모두 제외한다. 앱 Diary는 DIARY, HIGHLIGHT, CHECKLIST, TIME_TABLE, TIME_COLOR_TRACKER, STICKER 6종이다. 이전 7종·사진 fixture 기록은 과거 증거이며 현재 준비 조건이 아니다. 업로드 제외와 Web 구매 범위 반영 후 주력 suite 56/56, 109.23초, exit 0: `.cache/vault-e2e/2026-10-01T03-55-07-226Z-e8de2a82/suite.json`.
- 최신 owned 환경에서 Android USB bootstrap과 개발 로그인, 본문 교체·재열기, Web 반영을 확인했다. Web 입력은 AX label과 값이 합쳐질 수 있고 모바일 입력은 값만 노출되므로 같은 문자열 비교 규칙을 강제하지 않는다. Android 본문 편집은 editor identity/idle 후 실제 `memo-body`에 focus를 준다.
- 실제 관리자 Google OAuth 후 관리자 화면에서 Plus 2개·100,000 Token 키 2개를 발급하고 UI 다운로드했다. 일반 계정은 계속 동일 계정이며 Plus 1개·Token 키 1개를 실제 등록했다. native HTML select의 Mac popup은 Chromium CDP keyboard로 선택되지 않아, 상품 target만 기존 공용 Select로 바꾸고 실제 UI 선택·발급을 확인했다. Base UI는 자동 DOM id가 testid보다 우선될 수 있으므로 고정 DOM id 또는 실제 label을 쓴다.
- 실제 Quest 1,000 Token 구매는 `토큰으로 구매하기` 다음 `구매 확정`의 두 단계다. overlay 아래 내용으로 자동 기록된 tap은 실제 버튼의 selector로 교정한다. 구매·발급 fragment를 현재 DB에 반복 재생하면 데이터를 중복 생성하므로 새 전체 실행에서 검증한다.
- Market 완료 문구는 `market.purchasedId`로 먼저 표시되고 `market.busy` 동안 닫기가 막힌다. 완료 문구만 확인하고 Escape를 누르면 팝업이 남을 수 있다. 구매 완료 문구와 `market-purchase-ready`를 모두 확인한 뒤 닫고, dialog hidden/idle 후 다음 상품을 연다. 처리 중 guard를 제거하지 않는다.
- Chromium keyboard는 한국어 문자를 입력하지 못했고 `/` shortcut은 검색 focus 뒤 `/` 자체가 입력되기도 했다. 실제 검색 clear 버튼으로 초기화하고 ASCII 검색어를 입력한다. 검색 결과의 product name을 읽어 실제 UI로 구매한다. 좌표는 항상 새 tree에서 얻고, 최종 흐름은 고정 label/scroll-to로 정리한다.
- 동일 일반 계정에서 표시된 Market 블록·스티커 59개를 실제 구매한 뒤 새로 연 화면에서 보유 59개·잔액 93,100 Token을 확인했다. 기록: `.cache/vault-e2e/2026-10-01T02-51-32-244Z-20883da2/commerce-recording.json`. 이것은 live authoring 증거이며 canonical 전체 단계 검증이나 fresh 두 번 qualification이 아니다. 정리한 발급·등록·Quest·Market 후보 흐름의 전체 재생은 아직 하지 않았다.
- 업로드 제외 후 동일 일반 계정의 유료 Diary에 6종 콘텐츠를 저장하고 읽기 전용 package/API 검사 및 새로고침 후 페이지 이동으로 재진입 표시를 확인했다. 시간은 시작·종료 모두 오전/오후를 명시한다. 타임트래커 시간은 설정 버튼 label 대신 실제 표시 span에서 확인하고, 스티커는 항목 선택 뒤 추가로 확정한다. 새로고침은 날짜의 첫 페이지로 돌아가므로 작성한 Diary의 제목과 본문으로 페이지 정체성을 확인한다. 콘텐츠 작성 기록은 아직 전체 재생 전 후보이며, 전체 앱 통과가 아니다. 증거와 남은 계약은 앱 계획의 “업로드 제외 후 Web Diary 콘텐츠 재진입 확인”에 있다.
- 이미지 업로드 제거 후 Quest 행동 상한에서도 Image 사용을 삭제하고 첫 Block 이동 점수를 반영했다. 최초 Diary는 같은 Daily의 기존 Memo가 시즌 시작 전에 작성됐으면 새 페이지 추가만으로 인정되지 않는다. 수령 가능한 실제 과제와 남은 점수를 확인하며, 서버 날짜·진행도나 기준 점수를 직접 변경하지 않는다.
- Web Diary 도구 이름은 패널 제목과 겹쳐 좌표 녹화가 될 수 있다. `diary-tool-design/grid/sticker/archive`로 버튼을 선택하고 실제 selected prop에 연결된 `diary-style-selected`, `diary-layout-selected`로 결과를 확인한다. HMR은 날짜의 첫 페이지로 돌아갈 수 있으므로 검사 전에 페이지 제목 정체성을 다시 확인한다. 스타일·레이아웃 변경/복원과 화면 재진입, 6종 콘텐츠 확인 fragment는 변경 없는 YAML로 50/50 두 번(15.7초, 14.9초), 실패·경고 없이 통과했다. `.argent/flows/qa-vault-journey-web-diary-presentation.yaml` 및 `.cache/vault-e2e/web-diary-presentation-pass2.txt`; 전체 launch/새 DB/User qualification은 아니다.
- Web Market은 GRID와 GRID가 포함된 상품을 숨긴다. API catalog의 모든 유료 상품을 무조건 필수 구매로 잡으면 Web UI에서 완료할 수 없는 계약이 된다. Web 기준이라는 사용자 지시에 따라 기본 선택을 알리고 Web 표시 상품 기준으로 진행했다. 공유 표시 정책과 명시적 API/Web baseline으로 API 전체 구매 회귀를 유지한다. 표본 GRID는 정상 가입 시 실제 소유한 기본 스타일만 추가한다. 별도 사용자 답변이 오면 이 선택을 반영한다.

- 모바일 Diary의 “내용 전체 편집하기”는 menu item이 있어도 onPress 분기가 빠져 있으면 메뉴만 닫힌다. 기존 페이지 편집기로 pageId를 전달하는 adapter를 연결했고, 실제 Android 본문 수정·뒤로 저장·같은 ID/6종 유지 및 Web 재조회 표시를 확인했다. Web 재조회 fragment는 12/12 1회 통과했으며 모바일 꾸미기/전체 여정 통과는 아니다. canonical 보고서 `mobile-diary-content-canonical.json`과 앱 계획의 “모바일 Diary 내용 수정과 Web 재조회”를 확인한다.
- Android `getHierarchy` 12초 timeout 뒤 legacy fallback이 `UiAutomationService already registered`로 실패했다. 앱 crash로 분류하지 않는다. scoped Argent 정리 후 helper process가 실제 남아 있으면 대상 Android의 `com.argent.androiddevtools`만 정리하고 Argent로 다시 연결한다. 정상 describe가 full runner hierarchy의 정상 상태를 보장하지 않으며, 6초 cross-tree 경고는 미확인이다. 좌표 fallback을 최종 QA로 승인하지 않고 실제 directive 재생으로 확인한다. 기기/프로세스 상태를 매번 조회하며 이 조치를 일반 앱 재설치나 모든 기기 정리로 확대하지 않는다.
- Chromium F5 keyboard 이벤트는 이번 환경에서 페이지를 reload하지 않았다. Web 재조회 검증은 이미 보고 있는 Diary route를 다시 여는 refresh와 앱의 다음 페이지 버튼을 구분해 기록했고 정확한 모바일 저장 본문으로 판정했다. 이를 새 화면으로의 UI navigation 대체에 사용하지 않는다.

최초 정리: 2026-10-01. 이후 테스트에서 새로운 근거를 얻으면 해당 항목을 갱신한다. 아래 결과는 당시 실행 범위의 증거이며 현재 환경 상태나 전체 앱 통과를 뜻하지 않는다. 상세 실행은 [앱 계획](../../../../docs/devs/vault-app-e2e-plan.md)에 남긴다.

## 환경·로그인

### Google OAuth 포트 불일치 — 3000 관리자 로그인 부분 검증 완료

- **증상:** Google 로그인 버튼 이후 `400 redirect_uri_mismatch`로 관리자 단계에 진입하지 못했다.
- **근거:** 당시 Web origin은 localhost:3100이었다. 사용자는 등록된 환경의 기준이 localhost:3000이라고 확인했다. 로컬 앱에 들어오는 일반 credential 로그인 성공은 관리자 Google OAuth 성공과 별개다.
- **조치:** 사용자 지시로 프로젝트 실행 서버를 종료하고, 자동 실행기의 Web 기본 포트를 3000으로 변경했다. 사용자가 서버를 직접 여는 방식과 자동 전용 DB 실행을 구분했다.
- **검증 한계:** 2026-10-01 사용자 서버의 3000에서 실제 Google 이메일/비밀번호 입력 → Memo 진입을 녹화했고 세션의 authenticated/admin/emailVerified가 모두 true였다. 증거: `.argent/flows/qa-vault-journey-admin-oauth-3000.yaml`. 새 전용 DB 환경의 로그인·라이선스 발급과 전체 여정은 별도 검증이 필요하다.
- **재발 방지:** [환경 가이드](app-environment.md)의 서버/OAuth 절차를 먼저 적용한다. 포트 충돌을 피하려고 별도 3100 서버를 추가하지 않는다.

### Android의 오래된 LAN IP — USB 경로 구현, 기기 재검증 대기

- **증상:** 태그 이름 편집 중 앱이 종료됐다. USB 장치와 Metro 프로세스는 당시 살아 있었다.
- **근거:** logcat의 `DebugServerException`은 개발 서버 연결 timeout이었다. 앱이 요청한 LAN IP와 실제 Mac IP가 달랐다. 태그 입력 자체가 앱 종료 원인이라는 증거는 없다.
- **조치:** Android는 localhost와 실행 소유 ADB reverse로 Web·Metro·사진 저장소에 접근하도록 변경했다. 기존 mapping 재사용/충돌 보존/부분 실패 정리와 iPhone LAN 경로를 구분했다.
- **검증 한계:** 기존 runner 회귀를 포함한 API suite는 통과했지만, 새 USB 주소의 로그인·사진 업로드·태그 교체를 실제 기기에서 끝까지 재검증하지 않았다.
- **재발 방지:** 연결 실패 시 실제 Metro URL과 backend 경로를 진단한다. Android와 iPhone의 네트워크 설정을 동일한 LAN 가정으로 처리하지 않는다.

### 장치 연결 상태와 서버 소유권 — 현재 조회 필수

- **증상:** iPhone이 `connected`에서 `paired`로 바뀌었고, 이후 Android도 Argent와 ADB에서 보이지 않는 시점이 있었다. 과거 연결 성공만으로 재개할 수 없었다.
- **조치:** 연결된 물리 기기를 매 실행에서 확인하고, 종속 작업은 연결을 기다린다. 사용자 서버와 실행 소유 Next.js·Metro·DB·CDP를 구분한다. 명시적으로 모든 프로젝트 서버 종료를 요청받은 경우에만 그 범위까지 종료했다.
- **재발 방지:** 과거 장치 조회·화면 유지 설정·PID를 현재 사실로 보고하지 않는다. 서버 종료 후에는 listen 포트와 소유 프로세스 종료를 확인한다.

## 흐름 작성·판정

### 개별 흐름 반복에 치우침 — 전체 여정 우선으로 변경

- **문제:** 무료 기능의 작은 녹화·재생을 반복하는 동안 관리자 OAuth라는 유료 여정의 선행 장애와 전체 단계 연결이 늦어졌다. 부분 성공 수치와 근거 없는 완성도 비율도 전체 진척을 설명하지 못했다.
- **수정한 방식:** 선행 환경·로그인을 먼저 확인하고, 기존 통과 흐름을 조합해 전체 여정을 연결한다. 변경·실패·새 의심이 없는 개별 검증을 반복하지 않는다. 최종 두 번 검증은 연결된 전체 여정을 대상으로 한다.
- **보고:** 완료/실패/미실행 단계, 현재 장애, 다음 조치를 쓴다. API suite나 fragment의 통과를 앱 전체 완료로 계산하지 않는다.

### 실행 계정·데이터 기준 혼동 — 전체 실행 하나에 일반 계정 하나

- **문제:** 반복 녹화의 메모가 누적된 계정은 새 전체 실행용 검증기의 정확히 하나 조건에 맞지 않았다. 단일 메모/빈 휴지통을 가정한 lifecycle은 다른 기능과 연결하기 어려웠다.
- **조치:** Web·모바일은 한 전체 실행의 동일 일반 계정과 메모 정체성을 유지한다. 독립 API 테스트의 계정 격리와 구분한다. fragment는 자신의 제목/소유 메모만 대상으로 정리하고 다른 데이터는 보존한다.
- **검증:** Web 소유 lifecycle은 같은 86단계 흐름 두 번과 canonical 검사에서 기존 137개 페이지·본문 보존 및 테스트 메모 영구 삭제를 확인했다. 증거: `.cache/vault-e2e/web-memo-owned-lifecycle-scope-race-fixed-canonical.json`.
- **한계:** 이는 당시 fixture의 부분 흐름 검증이며 새 계정 전체 여정의 증거가 아니다.

### visible과 실제 준비 상태는 다름 — 동작 가능 상태를 기다린다

- **증상:** pin의 낙관적 label 변경 직후 disabled 상태의 unpin을 눌러 클릭이 무시됐다. 일부 전환은 기능 검사를 통과해도 idle 경고를 남겼다.
- **조치:** 행의 pending/ready와 저장 완료 표시를 실제 녹화해 기다렸다. reduced-motion 환경의 CSS 전환·hydration 오류를 관측한 부분은 제품 코드를 수정했다.
- **재발 방지:** 목적지 identity와 준비 상태를 구분한다. 경고 종류와 실제 움직임/트리/캡처 경로를 확인하며 timeout 증가·검사 삭제로 원인을 숨기지 않는다. 한 경고의 원인을 모든 경고에 일반화하지 않는다.

### 모바일 이름 변경의 덧붙임 — 정확한 교체 확인 필요

- **증상:** 기존 태그 flow가 `Mobile journey tagMobile tag renamed`를 저장하고 이를 성공으로 검사했다. Web 기준 이름 교체를 증명하지 못했다.
- **조치:** 기존 값 지우기 → 정확한 새 이름 → 색 변경 → 재진입 후 이름/색 유지 계약으로 재작성 중이다.
- **검증 한계:** 재녹화 중 위 Metro IP 오류로 종료됐다. arrow/backspace 방식의 교체 성공은 아직 확인하지 않았다.
- **재발 방지:** 입력은 덧붙임과 교체를 구분하고 전체 값 비교와 재진입 저장 검사를 사용한다. 테스트 기대값을 잘못 저장된 값에 맞추지 않는다.

## 저장·권한 회귀

### 영구 삭제 뒤 늦은 scope 응답이 행을 되살림 — 공유 코드 수정·부분 검증 완료

- **증상:** DB에서 영구 삭제됐지만 Web 목록에 메모가 남았다. 모션을 제거한 후에도 삭제 전 opened-page 응답이 늦게 반영되는 경쟁이 있었다.
- **근거와 조치:** 실제 삭제 전 API 응답을 지연시킨 회귀가 수정 전에 실패했다. 수락 delta 적용 전 같은 User의 진행 중 scope Query를 revert=false로 취소하고, 동일 Query 소유자가 살아 있을 때만 재조회하도록 변경했다. 제거된 Query는 재생성하지 않는다.
- **검증:** 수정 후 당시 suite 56/56, 변경 없는 Web lifecycle 86/86 두 번 통과. 증거: `.cache/vault-e2e/2026-10-01T01-18-50-830Z-6baaab6e/suite.json`, `web-memo-owned-lifecycle-scope-race-fixed-1.ndjson`, `-2.ndjson`.
- **재발 방지:** 목록 잔류를 화면 reload로 숨기기 전에 서버 결과와 늦은 조회의 캐시 반영을 구분한다. 관련 없는 활성 조회 완료와 Query 제거/세션 전환도 함께 검토한다. 모바일 전체 runtime 검증까지 끝났다고 보고하지 않는다.

### 무료 Sticker fixture가 유료 명령을 사용함 — 제품 권한 유지하고 fixture 수정

- **증상:** 무료 Block canonical 회귀 준비에서 `block.patch.v2`가 `subscriber-access-required`로 거절됐다.
- **조치:** 실제 무료 Memo commit과 같은 `block.patch/itemId` 경로로 fixture를 수정했다. 테스트를 위해 Subscriber guard를 완화하지 않았다.
- **검증:** 당시 suite 53/53에서 등록→Web 다섯 Block→모바일 다섯 Block canonical 회귀가 통과했다. 증거: `.cache/vault-e2e/2026-10-01T00-58-35-018Z-f1fce585/suite.json`.
- **재발 방지:** 무료 기능은 실제 앱이 쓰는 명령/권한 경로로 준비한다. canonical 데이터 통과는 사진 업로드·태그·전체 UI 수명주기 실행을 대신하지 않는다.

## 미결 사항을 그대로 남기는 법

### 추천 Diary와 페이지 재진입 — 명시적 콘텐츠로 부분 검증

- **증상/원인:** 추천 생성은 Block 종류·개수를 무작위로 고르므로 기존 녹화의 Checklist·일정 입력을 고정된 6종으로 간주할 수 없었다. Diary 재진입은 첫 페이지로 돌아오며, Memo에서 보관한 페이지도 Diary 탐색에는 남았다.
- **조치:** 같은 일반 User의 실제 Memo 편집기로 6종을 하나씩 작성하고 정상 Diary 이동으로 준비했다. 재진입 후 목표 페이지 제목까지 실제 탐색을 녹화했다. 검증기의 정확한 6종 조건과 제품 추천 정책을 유지했다.
- **검증:** 실행 `2026-10-01T11-23-29-904Z-f502ff9b`에서 유료 Diary canonical 저장·표시 검증과 별도 재진입 선택이 통과했다. 조합된 fresh 전체 재생은 남았다. authoring 진단 페이지에 의존하는 선택을 기본 경로로 고정하지 않는다.

### 중첩 scroll-to의 컨테이너 지정 — 첫 배경 실제 재생 통과

- **증상:** `sk26-block-004`는 실제 소유 카탈로그에 있지만 배경 목록의 scrollTop=0 상태로 “end of scroll” 실패했다. 컬렉션 누락 가설은 전체 AX와 읽기 전용 DOM 진단에서 기각됐다.
- **원인/조치:** `scroll-to.target.within`은 대상의 관계 선택자이고 스크롤 앵커를 설정하지 않는다. 중첩 스크롤은 `scroll-to: {target: {id: ...}, within: {id: diary-block-backgrounds}, direction: down}`으로 컨테이너를 별도 지정한다. 실제 AX 컨테이너 중심의 wheel로 이동 가능함을 확인한 뒤 후보 YAML을 고쳤다.
- **검증:** 동일 실행의 `qa-vault-web-decoration-sample-background-01.yaml.stream`에서 6 step, 경고·실패 없이 통과했다. 다른 배경과 Sticker picker 적용 및 전체 fresh qualification은 각각 별도 확인한다.

Web 사진 file chooser 자동화 방식처럼 계약/도구 선택이 미결이면 해결 기록에 그 상태와 필요한 답을 남긴다. Argent 도구로 부족한 경로를 요청하지 않은 script로 우회하거나, Web에 없는 신규 생성 기능을 검증기에서 몰래 제외해 통과시키지 않는다. 결정 후에는 해당 항목과 환경/계약 가이드를 함께 갱신한다.

## 과거 Web 생성 메뉴 기준 — 이후 업로드 제외로 앱 검증은 6종

사용자는 Web 실제 신규 Block 메뉴의 7종을 앱 E2E 기준으로 승인했다. 앱 Diary 콘텐츠 검증기와 기존 runner 회귀 fixture를 이 계약에 맞췄다. PROGRESS의 생성·저장은 주력 Vault/API suite에 유지한다. Web에 없는 생성 기능을 모바일에 새로 추가하지 않는다. 수정 후 주력 suite 58/58, 실패·생략 0, 종료 코드 0을 확인했다. 보고서: `.cache/vault-e2e/2026-10-01T02-22-46-032Z-f7143b55/suite.json`.

## 서버 소유 실행과 새 환경 진단 (2026-10-01)

- 사용자 지시로 3000 Web/8082 Metro와 전용 임시 DB·사진 저장소를 실행했다. 실행 기록: `.cache/vault-e2e/server-owned-3000.log`, run `2026-10-01T02-21-07-040Z-b2533f02`. 녹화 환경이며 전체 qualification 통과가 아니다.
- 새 환경의 Google 로그인에는 이메일/비밀번호 뒤 추가 “계속” 확인 화면이 나타났다. 이를 실제 녹화하고 Memo 진입 및 authenticated/admin/emailVerified=true를 확인했다. 원래 OAuth fragment의 timeout은 callback mismatch가 아니라 이 추가 화면 누락이었다. `.argent/flows/qa-vault-journey-google-consent.yaml`은 진단 fragment이며 최종 흐름은 분기 포함 전체 재검증이 필요하다.
- Next.js 요청-stage `connection()` 취소가 인증 복구 catch에 들어가 getSession 실패처럼 기록됐다. App layout에서 요청 대기를 catch 밖으로 옮겼으며 이후 실제 Admin/일반 Memo 진입에 재발하지 않았다. 진짜 auth 실패의 복구 및 권한 guard는 유지했다.
- 로그아웃 후 홈페이지에서 “headers() inside use cache” 오류를 확인했다. 캐시 Home의 서버 섹션들이 locale을 요청 헤더에서 추론했으므로 URL params를 검증해 locale을 명시적으로 전달하도록 수정했다. /ko·/en 요청 모두 200과 main/h1을 확인했다. 후속 warm 요청은 200이었지만 cold restart의 첫 요청에서 invalid element 500이 재현됐다. 명시적 Page use-cache 제거 시 첫 /ko 요청 200, 원래 캐시 복원 후 첫 /ko 요청 500을 확인했다. 원인은 페이지 출력 캐시 경계까지 좁혔으며 내부 undefined component의 정체는 확인하지 못했다. 명시적 페이지 캐시 제거 후 새 서버 첫 /ko는 200이고 /en도 main/h1을 포함한 200이었다. 이후 PublicLayout params가 사전 렌더링을 막는 로그를 확인해 홈의 서버 route adapter에 instant=false를 설정했다. 홈→Memo 재진입에서 새 오류는 없었으며 최종 owned 실행 `2026-10-01T02-51-32-244Z-20883da2`의 cold /ko, /en, Web 로그인·Memo·관리자 이동에서 앱 오류 재발이 없었다. 최종 로그는 `.cache/vault-e2e/server-owned-3000-final.log`에 있다. 비교 로그: server-owned-3000-cache-diagnostic.log, server-owned-3000-cache-control.log.
- Android 일반 로그인은 Web과 동일한 실행 계정으로 성공했고 빈 무료 Memo 화면을 확인했다. `memo-list-screen`은 live AX describe에서 드러나지 않아 로그인 성공에도 wait가 실패했다. 트리 projection 차이를 제품 인증 실패로 판단하지 않는다.
- Android에서 개발/프리뷰 설치가 같은 expo/exp+senanote scheme을 처리하면 open-url은 연결 앱 chooser를 띄운다. Argent open-url의 bundleId는 Android에서 무시된다. 실행기 환경 준비 intent에만 ADB -p로 선택한 패키지를 지정했다. 실제 package/MainActivity, Status: ok를 확인했다. 앱 조작·검증은 Argent로 유지하며 이후 자동 fresh-run bootstrap에서도 chooser 없이 선택한 개발 앱의 MainActivity와 Metro 연결을 확인했다. 사진 업로드·태그 교체와 전체 여정은 별도 검증이다.
- 앱 reload와 debugger component-tree 요청이 겹친 뒤 __argent_callback 오류가 생겼다. 앱 제품 코드에서 이 tooling callback을 수정하지 않는다. device-scoped Argent 서비스 정리 후 새 debugger 연결을 준비했고 현재 검증은 native describe로 진행한다.

사용자는 이후 이미지 업로드 테스트 자체를 삭제하도록 변경했다. Web/모바일 photo flow, Android 사진 fixture 준비, 업로드 검증 두 개와 앱 upload-recovery 계약을 제거했다. 앱 Diary 생성은 업로드가 필요한 IMAGE를 제외한 6종이며, IMAGE Block 생성·저장 API 계약과 마켓 자산 표시는 유지한다. 파일 선택이나 Computer Use를 다음 실행의 선행 조건으로 요구하지 않는다.


## Diary 준비 명령의 Quest 집계 누락 — 회귀 수정 및 보상 canonical 검증 완료

- 실제 6종 Diary 저장 뒤 Block 사용은 집계됐지만 일일 완료는 증가하지 않았다. 공유 활동 수집기가 `page.prepare-diary`의 pageId를 수집하지 않아 QUICK → DEFAULT 전이를 놓쳤다. 기존 User 잠금과 Daily 전체 전후 비교를 유지하고 대상 명령만 추가했다.
- 기존 Quest 회귀에 정상 준비와 반복 준비를 추가했다. 최초 fixture의 grid token `100`은 무효였으므로 해당 resource-unavailable 실패를 제품 재현으로 해석하지 않는다. 유효한 `68`로 고친 후 완료/수령 가능 횟수 0의 실패(52/53)를 확인하고, 수정 후 53/53을 확인했다. 보고서는 앱 계획의 현재 증거에 연결했다.
- 실제 Web 새 Diary 본문 작성 후 first_daily·daily_done 각 1회 완료를 확인했다. 이어 같은 User가 정상 UI로 9월 1–21일 본문/완료 Checklist를 작성·Diary 준비했고, 실제 포인트 수령과 보상 1–10의 상품 선택·수령 후 canonical 검증까지 통과했다. 현재 9단계 모두 canonical 통과다. 수동 복구가 포함되어 fresh 전체 두 번의 증거는 아니다. 보고서는 앱 계획의 Quest 후속 증거를 따른다.
- Quest 포인트 수령 직후 카드가 재배치된다. 이전 프레임의 좌표로 다음 카드를 누르면 다른 Quest가 수령되거나 클릭이 무시될 수 있다. 새 ready/Quest slug 마커로 상태와 대상을 구분하고, 동작 전에 최신 트리 및 안정 상태를 확인한다. 보강된 전체 흐름 재생은 남았다.
- 화면 밖의 포인트 버튼은 트리에 있어도 visible 대기를 통과하지 않는다. 먼저 해당 컨테이너에서 실제 스크롤을 녹화하고, 목적 버튼의 positive check → 수령 → 동일 버튼 hidden 순서로 확인한다. 실패한 원본은 ignored 진단 경로에 보존하고 최종 manifest에 넣지 않는다.
- 포인트 claim은 같은 버튼이 여러 번 유지될 수 있다. 연속 claim 사이 ready 상태와 최신 위치를 확인하며, 아직 수령 가능한 횟수가 남은 버튼에 hidden을 요구하지 않는다. 고정 21일 활동에서 일일 21회·Checklist 20회·3일 연속 7회·7일 연속 3회로 7700P를 확보해 다른 임의 활동의 포인트에 의존하지 않는다.
- 미수령 Quest가 있으면 Hero는 `포인트 받으러 가기`를 우선하므로 충분한 보상 포인트가 있어도 `보상 받기`는 없다. 보상 로드의 단계 제목을 직접 열고 같은 Dialog의 실제 수령·지급 검증을 유지한다. 수령 상태 표시 뒤 닫기 버튼이 준비될 때까지 기다려 `dialog-close` ID를 사용한다.
- Diary 달력은 이전 표시 달을 기억할 수 있다. 무조건 이전 달을 누르면 이미 9월인 달력이 8월로 이동한다. 달 상태를 확인하는 optional setup 후 필수 목표 날짜 검사를 수행한다. 현재 후보의 2026년 10월/9월 날짜 범위는 한계로 명시하고 다른 달을 통과했다고 주장하지 않는다.
- Chromium live AX가 Base UI의 DOM id를 우선 출력해도 기존 data-testid가 DOM/runner에는 있을 수 있다. `diary-current-day`가 이 사례다. 잘못된 컴포넌트에 신규 marker를 추가하기 전에 실제 route의 소유 컴포넌트와 runner 선택자를 확인한다.

## 추천 Sticker 정리와 재생 경고 — 부분 검증

- 추천 Page Sticker는 준비 시 최대 5개가 무작위로 배치돼 Block 클릭을 가릴 수 있다. Web PageCanvas가 공유 renderer의 접근성 callback에 카탈로그 이름을 전달하도록 보강했다. 배경 적용 전 테스트가 만든 보드의 추천만 실제 선택/삭제 UI로 정리한다. 다른 페이지를 지우거나 pointer event를 우회하지 않는다.
- Sticker sidebar의 변경/삭제는 미리보기 hover/focus 때 나타난다. 수령 창과 달리 선택 후 삭제를 바로 기다리면 보이지 않는다. 미리보기에 실제 focusable 버튼을 연결해 클릭/키보드로 해당 동작을 드러내도록 했다. 별도 정상 Memo/Diary에서 추천 2개를 삭제하고 같은 이름 및 편집 창의 소멸을 실제 확인했다.
- `qa-vault-web-decoration-clear-recommendations`는 새 정상 추천 2개에서 28 pass, 6 optional skip, 실패/오류/경고 0으로 1회 재생됐다. optional 분기는 준비 단계이고 최종 빈 상태는 분기 밖의 필수 검사다. 전체 fresh qualification이나 모든 추천 개수의 두 번 검증은 아니다.
- 기존 report classifier는 모든 skip을 거절해 정상 `when` false도 증거 부족으로 처리했다. 명시적 조건 불충족 when과 그 깊이 안의 정확한 `when block skipped`만 구분하는 회귀를 추가했다. 이전 실패 뒤 skip, 고아/잘못된 깊이 skip, 실행된 필수 검사가 없는 보고서, 경고는 계속 거절한다.
- 모션 감소가 켜져 있어도 Sticker 컬렉션 wheel은 Embla duration 20을 고정해 약 1초간 이동했다. reduceMotion에서 duration 0을 사용하도록 수정한 후 실제 wheel 뒤 1.1초간 transform 고정을 확인했다. 기본 환경의 관성과 저장·선택 경로는 유지한다. 192단계 재생의 동작은 모두 통과했지만 작은 화면 변화 경고 11개가 남아 있다. 이 수정 하나로 모든 idle 경고가 해결됐다고 판단하지 않는다.
- UI 소스 수정 후 HMR이 Diary page index를 첫 페이지로 되돌릴 수 있다. 예전 target 이름이 없는 경우 새 트리를 확인하고 정상 탐색으로 진단 페이지를 복구한다. 실패 wait와 수동 복구는 전체 통과 증거에 포함하지 않는다.

## 첫 fresh 전체 재생 — 기능 통과와 안정화 증거 부족을 구분

- `pnpm test:vault:app`의 첫 새 DB/계정 실행 `2026-10-01T14-48-27-421Z-e8ae0e12`에서 무료 Memo는 555 pass, 실패/오류/skip 0이었다. 목록 전환 3곳·시간 listbox 2곳·스티커 저장 1곳의 idle 경고 6건으로 종료 코드 2였다. 뒤의 8단계와 pass 2는 실행되지 않았다. 보고서: `.cache/vault-e2e/2026-10-01T14-48-27-421Z-e8ae0e12/pass-1/web-free-memo.json`, `summary.json`.
- 기존 stage 상태 `unexecuted`만 보고 무료 기능이 실행되지 않았다고 판단하지 않는다. 해당 flow의 steps와 warning을 함께 읽는다. 실행기 요약에 passedSteps·failedSteps·warningSteps를 추가하여 실제 행동 실패와 판정 증거 부족을 구분한다. 경고 거절 정책은 그대로다.
- UI 보고서의 유일한 미완료 사유가 경고이면 canonical 저장 상태도 읽기 전용으로 검증하도록 보강했다. 최초 strict code 2는 유지하고 기본 실행에서는 후속 단계를 차단한다. UI 실패·오류·임의 skip·실제 검사가 없는 보고서를 이 경로로 승인하지 않는다. 이후 fresh 무료 Memo에서 실제 canonical 성공과 code 2 유지를 확인했다.
- 별도 녹화 환경 `2026-10-01T14-54-38-388Z-4fff3bc9`에서 lifecycle 86 step은 모두 통과했지만 작은 변화 경고 2건이 재현됐다. 시간 listbox와 휴지통 전환 뒤 연속 전체 크기 캡처 각각 3장·5장에는 픽셀 차이가 없었다. 시간 listbox의 별도 idle는 통과했지만 1.5초/6.9초로 달랐다. 이 캡처는 최초 경고가 발생한 같은 순간의 증거가 아니며, 원인이 scrollbar·caret·제품 애니메이션·Argent capture라고 확정하지 않는다. 제품 CSS나 Argent 설치 파일을 추측으로 바꾸지 않는다.
- optional setup skip 분류 수정 후 주력 suite는 53/53, 종료 0이었다. 보고서: `.cache/vault-e2e/2026-10-01T14-39-43-881Z-9be67013/suite.json`. 9개 stage/81개 참조 source의 manifest 사전 검사도 통과했다. manifest 연결·부분 기능 재생·주력 suite 통과를 전체 Web qualification 완료로 표현하지 않는다.
- 단계 수 보고 보강 후 주력 suite도 53/53, 종료 0이었다: `.cache/vault-e2e/2026-10-01T15-06-27-337Z-b17ce966/suite.json`. 이후 추가한 경고-only canonical 읽기는 이 실행의 runtime 검증에 포함되지 않는다. 직접 vitest 호출은 전용 DB guard로 거절되므로 루트의 `pnpm test:vault:suite`를 사용한다.

## 자정 이후 Color Tracker 시간 선택 실패 — 실행 시간에 의존한 준비 수정

- fresh 재실행 `2026-10-01T15-13-29-936Z-1fb51749`는 시작 시간을 `09`로 선택한 뒤 `시작 시간 변경, 09:` 검사가 실제 실패했다(code 1). 경고만의 실패나 키보드 type-ahead 문제로 해석하지 않는다.
- 첫 Time Color Tracker는 현재 시각을 다음 정시로 올려 시작하고 한 시간 뒤에 끝난다. `getTimeColorPickerBounds`는 시작을 종료-1분 이하로 제한한다. 자정 이후 실제 UI 초기값 `01:00–02:00`를 확인했다. 이 상태에서 09시를 먼저 선택하면 정상 도메인 규칙이 clamp하므로 기존 시나리오의 준비 순서가 잘못됐다.
- 같은 정상 UI로 AM의 12시(00시) → 종료 10:05 → 시작 필드 복귀를 녹화했다. 기존 09:05–10:05 설정·색상·본문 수정·저장·재진입·휴지통 검사는 유지한다. 시계·시간 데이터 주입, bounds 완화, 기대 시간 변경은 없다. 실제 시간 옵션의 안정 ID와 중첩 scroll-to로 준비한다.
- 뒤의 기존 행동 92 step은 무경고 통과했고, 정리된 `qa-vault-journey-web-color-tracker` 전체도 151 pass, 실패/오류/skip/경고 0으로 1회 통과했다. 증거: `.cache/vault-e2e/2026-10-01T15-19-50-501Z-5c38fc62/color-normalized-full-replay.json`. 이는 부분 flow 증거이며 fresh 전체 두 번을 대체하지 않는다. manifest는 이 준비 단편을 포함한 9 stage/82 source로 재구성했다.
- 수정 후 fresh 실행 `2026-10-01T15-27-07-932Z-a7d5a558`는 무료 Memo 587 pass, 실패/오류/skip 0이며 canonical 저장 상태도 통과했다. 시간 선택 순서 실패는 재발하지 않았다. 목록 전환 5건·Time table listbox 3건의 경고로 strict code 2, 후속 8단계/pass 2는 미실행이다. `.cache/vault-e2e/2026-10-01T15-27-07-932Z-a7d5a558/pass-1/web-free-memo.json`, `web-free-memo-canonical.log`에 UI/저장 증거가 각각 있다.
- Time table 경고 중 두 개는 hold가 712ms/239ms 남은 채 7500ms 예산이 끝난다는 정확한 원인을 보고했다. 기존 stableFor 1000ms는 유지하고 timeout만 15000ms로 늘렸다. 세 번째 지속 움직임 경고의 원인이 같은지 아직 확인하지 않았다. 목록 Nav row CSS에 빠진 `motion-reduce:transition-none`도 추가했다. 이 변경이 모든 목록 경고를 해결했다는 runtime 증거는 후속 fresh 결과로 확인해야 한다.
- 후속 fresh `2026-10-01T15-34-01-189Z-aa9ad783`는 587 pass와 canonical 저장 검증 통과, 실패/오류/skip 0이었다. 기존 목록/Time table 경고는 없었으며 Checklist 신규 editor와 Color tracker 옵션 스크롤 뒤의 작은 변화 경고 2건으로 code 2였다. 실제 reduced motion에서 전체/휴지통 필터 transitionProperty=none을 확인했다. `.cache/vault-e2e/2026-10-01T15-34-01-189Z-aa9ad783/pass-1/web-free-memo.json`, `app.json`에 UI 수치와 strict 판정이 함께 있다. 남은 두 경고의 원인을 caret/scrollbar라고 확정하지 않는다.

## 실행기 회귀가 실제 앱을 시작하는 주변 환경 의존 — 수정

- manifest/OAuth가 완성되고 3000 포트가 비어 있자, launcher의 code 2 보존 회귀가 실제 Web을 시작해 30초 timeout으로 52/53 실패했다. 과거의 53/53은 이미 열린 녹화 환경의 포트 충돌로 우연히 조기 차단된 경우였다. 증거: `.cache/vault-e2e/2026-10-01T15-45-31-797Z-00fb97b4/suite.json`.
- timeout 뒤 부모가 종료되어도 회귀가 spawn한 앱 자식은 살아 있었다. secret lease의 소유 PID와 `run.mjs app` 프로세스를 확인해 해당 소유 자식에만 SIGINT를 전달했다. 기존 사용자 서버를 종료하지 않았다.
- 회귀 child 환경에 명시적으로 무효 포트 0을 전달하여 환경 사전 검사에서 반드시 code 2가 나도록 했다. spawn에도 10초 timeout/SIGINT를 설정했다. manifest 부재·OAuth 누락·이미 열린 포트를 회귀 fixture로 가정하지 않는다. 수정 후 포트가 비어 있는 상태에서 주력 suite 53/53, 종료 0을 확인했다: `.cache/vault-e2e/2026-10-01T15-49-25-078Z-d5b28e5e/suite.json`.

## idle 경고를 남기는 후속 기능 진단 — strict qualification과 분리

- 내부 옵션 `VAULT_E2E_DIAGNOSTIC_CONTINUE_IDLE_WARNINGS=1`은 성공한 UI 보고서의 경고가 idle에만 있고 canonical도 성공한 경우에 후속 기능을 진단한다. 상태는 incomplete/code 2, 전체 실행도 code 2이며 두 번 qualification 성공으로 세지 않는다. UI 실패·오류·미확인 skip·다른 경고·canonical 실패는 계속 차단한다. 기본 세 명령은 strict 판정을 유지한다.
- 기존 classifier 회귀에 기본 경고 거절, 진단 시 idle만 허용, 실제 검사 없는 보고서·tap 경고·고아 skip·실제 실패 거절을 추가했다. 최초 실행에서는 이 회귀가 통과했지만 별도 launcher 환경 의존 때문에 전체 suite는 실패했다. 진단 옵션의 실제 9단계 실행 결과는 이후 보고서로 확인한다.

## fresh 기능 진단 7단계 통과와 장식 진입 실패

- `2026-10-01T15-51-30-647Z-2b35e10b/pass-1`에서 무료 Memo 587, 관리자 Google/발급 62, 등록 34, Quest 구매 20, Market 구매 895, 유료 Diary 72, 소유권 8개의 pass를 기록했다. 모두 canonical 검증도 통과했다. 무료/관리자/Market/소유권에는 idle 경고가 남아 strict incomplete다.
- 장식 root는 launch 다음의 `diary-saved-page` 검사에서 실제 실패했다. 이전 단계는 Market이다. 동일 환경에서 root launch 이후에도 실제 pathname=/app/market, market-screen=true, diary-saved-page=false임을 재현했다. 재사용 Chrome의 launch는 args의 URL로 활성 탭을 이동시키지 않았다. Market의 실제 nav-app-diary-daily 클릭→저장 페이지 identity를 새 fragment로 녹화하고 root에 연결했다. 전체 장식 재생·fresh qualification은 별도로 확인한다. Quest 보상은 미실행이며 authoring 성공을 fresh 통과로 대체하지 않는다.

## OAuth callback의 일회성 코드가 서버 로그에 포함되는 문제 — 마스킹 보강

- Next의 GET callback 로그는 URL query의 code와 state를 포함했다. 해당 callback 값을 출력하거나 저장 flow에 옮기지 않는다. 보고서 공통 redactor에서 URL query의 code/state/access_token/refresh_token/id_token을 마스킹하고, 기존 실행 로그 5개에서도 값을 제거했다. 프로세스별 stdout/stderr를 완성된 줄로 전달하여 chunk 경계로 마스킹이 누락되지 않게 했다. 합성 분할 출력과 개행 없는 마지막 출력 보존도 직접 확인했다.
- 기존 redaction 회귀를 synthetic URL로 확장했다. 숫자 종료 code 0은 유지하고 callback credential은 제거한다. 주력 suite 53/53, 종료 0: `.cache/vault-e2e/2026-10-01T16-13-31-605Z-6b45b130/suite.json`. 진행 상태 검색은 `[vault-e2e]` 행에 한정한다. 일반적인 `code=` 검색은 OAuth callback도 잡으므로 사용하지 않는다.

## 장식 30개 진입 복구와 실제 적용 검증

- Market→Diary 실제 navigation fragment 연결 뒤 `2026-10-01T16-06-51-105Z-13ec187f`의 장식 전체는 461 pass, 실패 0이었다. GRID 3/BLOCK 10/STICKER 17 모두 저장되어 누락 0, canonical도 통과했다. `.cache/vault-e2e/decoration-full-replay.log`와 해당 실행의 `diary-apply-thirty-authoring-canonical.log`를 확인한다. 기존 실패 후 유지한 환경의 부분 복구 증거이며 fresh qualification은 아니다.
- 7개 idle 경고는 Sticker 적용 후 app-save-idle 다음의 추가 stillness에서 나왔다. 적용은 화면 전환이 아니므로 17개 표본 fragment의 마지막 불필요한 idle을 제거했다. collection 전환의 identity/idle 및 실제 저장 완료 검사는 유지한다. 이 정리의 fresh 실행 결과는 별도로 확인한다.

## Quest 날짜 버튼의 자동 ID 우선 노출 — 안정 ID 수정

- 장식 다음 Quest root는 `diary-current-day` tap에서 실패했다. 실제 화면의 날짜 버튼은 visible이지만 Base UI의 자동 id만 AX에 노출됐다. data-testid가 있어도 DOM id가 우선이면 Argent의 id selector로 찾을 수 없다.
- CurrentDay의 Popover Trigger에 명시적인 id=diary-current-day를 추가했고 실제 describe에서 stable id를 확인했다. 해당 Diary 준비 fragment는 14 pass/실패·경고 0이었다. 같은 UI에서 기록된 calendar action을 유지하며 dialog identity/idle 뒤에 optional month setup을 평가하도록 정리했다.
- 이미 준비한 첫 실제 활동은 유지하고 기존 calendar/claim/reward fragment를 묶어 남은 단계부터 이어간다. `.cache/vault-e2e/quest-prepare-replay.log`, `quest-resume-replay.log`를 확인한다. 복구 재생을 변경 없는 fresh 전체 성공으로 표현하지 않는다.

## 점수 청구 뒤 보상 제목이 고정 헤더 뒤에 가려진 문제

- 21일 정상 UI 활동과 51회 실제 점수 청구는 완료되어 7,700점이 저장됐다. 이후 첫 보상 제목 tap은 pass였지만 Dialog 검사가 실패했다. AX 제목 frame은 화면 상단 0.032였으며 고정 앱 헤더에 가려졌다. tree의 양수 frame만으로 실제 클릭 가능 영역을 확정하지 않는다.
- 보상 로드 header로 정상 scroll→header identity→첫 제목 identity를 실제 녹화했고 semantic scroll-to로 정리했다. 보상 제목 자체만 scroll target으로 쓰면 부분 노출을 충분하다고 판단할 수 있다. 최초 두 보상 fragment는 25 pass, 실패·경고 0으로 실제 수령했다: `.cache/vault-e2e/quest-first-rewards-replay.log`.
- 남은 보상은 같은 환경에서 이어서 수령 검증한다. 이미 받은 포인트를 다시 청구하거나 앞의 날짜 활동을 중복 생성하지 않는다. 이 부분 복구를 root 전체 pass로 세지 않는다.

## 9단계 authoring 저장 검증 완료, fresh 전체 재검증

- `2026-10-01T16-06-51-105Z-13ec187f`의 같은 일반 계정·DB에서 앞의 7단계, 장식 30개, Quest 7,700점·보상 10단계의 canonical 체크포인트가 모두 통과했다. 첫 두 보상은 25 pass/경고 0, 나머지 8개는 107 pass/실패 0/idle 경고 2였다. 보고서: `quest-ten-rewards-authoring-canonical.log`, `.cache/vault-e2e/quest-remaining-rewards-replay.log`.
- 중간 수정·실패 후 부분 재개가 있으므로 전체 qualification으로 세지 않는다. 실제 수령/entitlement/ledger·중복 지급 방지는 canonical이 확인했다. 새로운 증거 없이 기존 기능 단편을 다시 반복하지 않는다.
- 보상 Dialog 닫기 뒤 같은 quest-board-ready/idle이 연속 중복된 7쌍을 제거했다. Dialog hidden 및 복귀 identity/idle 한 쌍은 유지한다. 최종 manifest는 9 stage/85 source, semantic target 감사 weakTargets 0이다.
- 소유 임시 license 환경 파일을 hash 확인 후 제거하고 recording runner만 SIGINT로 종료했다. 수정된 전체 여정을 새 DB·계정에서 다시 실행한다. 경고 진단 모드여도 실제 실패는 차단하고 전체 경고 판정/code 2는 유지한다.

## 첫 fresh 9단계 전체 기능·저장 검증 — qualification 미완료

- `2026-10-01T16-49-09-606Z-1aaaeb70/pass-1`은 중간 수정/재개 없이 9단계를 모두 실행했다. 3,245 pass, 실제 fail 0, canonical 체크포인트 9개 모두 통과했다. 새 일반 계정/DB의 장식 30개와 실제 Quest 7,700점·보상 10단계가 검증됐다. `app.json`, `journey-canonical.json`, 각 stage JSON/canonical log를 확인한다.
- idle 경고 29건으로 종료 2였고 pass 2는 미실행이다. 경고를 삭제한 보고서를 성공 증거로 쓰지 않는다. functional diagnostic과 strict qualification을 구분한다.
- 남은 무료 경고는 Clock form의 Select option 상태를 바꿀 때 추가된 stillness, 표본 경고 대부분은 collection viewport를 스크롤한 뒤의 stillness였다. 같은 form의 option 상태나 같은 collection viewport 스크롤은 화면 navigation이 아니다. 해당 추가 idle 8/33개와 option 스크롤 뒤 idle 2개를 제거했다. listbox/option identity·선택 시간 hard check·저장/재진입/canonical 검사는 유지하며 Editor/Dialog/route 전환의 identity/idle은 유지한다. 수정한 시간 선택부터 targeted UI 재검증한다.
- OAuth 구조체의 camelCase idToken과 snake_case access_token/refresh_token/id_token도 common redactor에 추가했다. 기존 회귀를 확장한 주력 suite는 53/53, 종료 0: `.cache/vault-e2e/2026-10-01T17-08-43-780Z-372606b4/suite.json`.


## 구매 영수증 종료와 Market 데이터 준비 — 부분 검증

- `2026-10-01T17-25-33-755Z-9b34d517`의 전용 녹화 환경에서 개발 로그인 9개, Time table 77개, Color tracker 145개가 실패/경고 없이 통과했다. 타이밍 문제를 재현한 Select/스크롤 뒤의 중복 idle만 제거했으며 시간 값·저장·재진입·휴지통 검사는 유지했다. `.cache/vault-e2e/readiness-clock-replay.log`.
- 같은 일반 User를 유지한 채 실제 관리자 Google OAuth·UI 발급 62개와 UI 일반 계정 등록 34개가 실패/경고 없이 통과했다. UI 다운로드에서 키를 읽고 DB 조회로 발급 소유자·상품·미사용 상태만 검증했다. grant/session/balance를 주입하지 않았다. 진단의 임시 `.env.local`은 소유 내용 hash 검사 후 제거했다.
- Market 구매 영수증 visible → 실제 reconcile 준비 → Dialog 닫힘 → 같은 영수증 hidden → Market identity/idle 순서로 녹화했다. 당시 Sonner 알림이라고 해석했지만 후속 소스 확인으로 잘못된 해석임을 확인했다. 영수증은 ProductDialog 내부 PurchaseReceipt의 role=status 문구이며 Dialog와 함께 제거된다. 자동 소멸을 기다리는 절차가 아니다. 기존 의미가 확인된 구매 버튼 타깃은 유지했다. 두 번째 실제 상품 구매는 12 pass, 실패/경고 0이고 읽기 전용 조회로 올바른 두 상품과 잔액 99,800을 확인했다. `.cache/vault-e2e/readiness-market-buy-replay.log`.
- Recorder가 실제 Dialog의 클릭을 임시 base-ui ID 또는 뒤쪽 상품 이름으로 변환하기도 했다. 녹화 요약의 tap 타깃을 반드시 검토한다. AX에서 클릭 가능한 버튼을 골라야 하며 이름이 같은 Dialog 부모를 클릭 위치로 쓰지 않는다. 잘못 변환된 tap 원본은 진단 증거로 보존하고 검증된 기존 semantic tap을 조합한다.
- Market 첫 진입의 `market-screen`은 catalog query 완료 전부터 존재한다. 데이터·fetch·mutation·sync 상태에 연결한 `market-catalog-ready`를 Masthead에 붙이고, 실제 route 진입에서 visible을 녹화했다. Masthead는 스크롤 후 화면 밖으로 나가므로 초기 진입 전에만 사용한다. 스크롤 후 기다린 첫 진단의 zero-frame 실패는 gate 부재로 해석하지 않는다.
- 데이터 준비 gate를 추가한 진입 단편은 4 pass/실패 0이지만 작은 pixel 변화 idle 경고 1개가 남았다. 준비 gate만으로 모든 이미지/폰트/렌더 변화까지 해결됐다고 주장하지 않는다. 당시 이후 DOM 조회에서는 reduce motion=true, animations=[], visible images complete=true, fonts loaded였지만 경고 시점 원인은 아직 미확인이다. `.cache/vault-e2e/readiness-market-entry-replay.log`.
- Quest 포인트 claim은 같은 보드 내 mutation이므로 중복 idle 52개를 제거했다. 실제 Quest route의 identity/idle와 각 claim 버튼·board-ready·canonical 원장 검사는 유지한다. Dialog 진입/닫기와 Diary 날짜 전환의 실제 navigation idle은 보존한다.
- manifest는 신규 receipt/entry fragment를 포함한 9 stage/87 source, weak target 0으로 다시 조립했다. 이 변경을 포함한 fresh strict 실행은 별도 확인한다. 단편 통과는 두 번 전체 qualification 통과가 아니다.


## 새 입력란의 커서 깜빡임 — 관측 및 모션 감소 수정

- 기본 strict 실행 `2026-10-01T18-08-36-738Z-26b3c9a8/pass-1`은 무료 Memo 577 pass/실패 0, canonical 성공, idle 경고 1개로 종료 2다. 경고는 신규 Color tracker의 `트래커 내용` identity 뒤이며 후속 8단계/pass 2는 미실행이다. HTTP 500은 없었다. `.cache/vault-e2e/web-readiness-strict.log`.
- 전용 새 진단 `2026-10-01T18-14-00-960Z-ae2c5a76`에서 정상 개발 로그인과 기존 recorded prefix로 같은 화면을 열었다. activeElement는 `트래커 내용` INPUT이며 memo-editor 안에 있고 caret-animation=auto, reduce motion=true, CSS/Web animations=0이다. 새 Memo editor 진입의 idle도 같은 작은 변화 경고가 간헐적으로 나왔다. 실제 route/navigation readiness를 삭제하지 않았다.
- 수정 전 두 PNG의 읽기 전용 픽셀 비교는 16 pixels, bounds x817/y184–199만 달랐다. 이는 실제 focus 입력 rect x809.53/y176.5/w196.34/h31 안의 세로 한 줄이다. 커서 원인을 이 화면에서 확인했으며 모든 과거 경고로 일반화하지 않는다.
- `globals.css`에서 prefers-reduced-motion 범위에 inherited caret-animation: manual을 적용했다. 커서는 같은 불투명 색으로 계속 표시되고 focus/input 동작도 유지한다. 지원 브라우저는 깜빡임을 줄이고 미지원 브라우저는 기본 CSS fallback이다. [MDN 정의](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/caret-animation)를 확인했고 owned Chromium CSS.supports=true, focused computed manual을 실제 확인했다.
- CSS HMR 시 Next가 `No link element found for chunk`를 보고해 처음 computed style은 auto였다. 정상 route를 다시 열자 root·모든 input이 manual로 렌더됐다. HMR 오류 상태를 성공 증거로 사용하지 않는다. 최종 qualification은 소스 변경 없이 fresh 서버/프로필로 실행한다.
- 수정 후 같은 focus 상태의 두 PNG는 변경 pixel 0이었다. 녹화한 focus/identity + idle stableFor1000/timeout15000 단편은 3 pass/실패·경고 0이다. PNG 4개와 `caret-pixel-evidence.json`은 진단 실행 폴더에 보존했다. Argent screenshot-diff는 Chromium unsupported였으므로 macOS AppKit으로 saved PNG를 읽기만 하여 비교했다. 이미지 편집이나 픽셀 좌표 기반 tap은 없다.
- 진단용 flow 2개는 evidence 폴더로 옮겼으며 manifest에 포함하지 않는다. 소유 recording root와 secret lease를 정리하고 기본 `pnpm test:vault:app`을 다시 시작했다. 전체 두 번 qualification 결과는 후속 보고서가 결정한다.


## 커서 수정 후 새 Memo 진입 준비 — 부분 검증

- `2026-10-01T18-20-01-468Z-4102dfee/pass-1` 기본 strict 결과는 무료 577 pass/실패 0, canonical 성공, 경고 1개(code 2)다. 이전 Color tracker 커서 경고는 재발하지 않았고 새 Memo lifecycle의 memo-editor identity 다음 idle에서 다른 작은 변화 경고가 발생했다. 모든 과거 경고가 커서라고 설명하지 않는다.
- 새 소유 진단 환경에서 기존 creation/identity를 재생하고 `memo-editor`와 실제 `app-save-idle`을 live 녹화했다. reusable editor-entry-ready fragment는 이 두 구조 검사 뒤 stableFor1000/timeout15000의 navigation idle을 유지한다. lifecycle 첫 진입에만 이 단편을 연결했다. 새 편집기의 정확한 경고 pixel 원인은 아직 미확인이며 준비 gate가 실제 pending 상태를 기다렸다는 별도 타이밍 증거는 없다.
- 수정된 lifecycle 전체는 88 pass/실패·경고 0으로 1회 재생됐다. 생성·본문·저장·재진입·고정·해제·보관·복원·휴지통 acceptance를 유지한다. `.cache/vault-e2e/editor-ready-lifecycle-replay.log`. 이는 전체 qualification이 아니며 원인이 최종 해결됐는지는 fresh strict 실행으로 판단한다.
- manifest는 9 stage/88 source, weak target 0으로 조립했다. 진단용 prefix는 증거 폴더에 옮기고 소유 recording root를 정리했다. 다음 실행에 모든 source와 CSS 변경을 고정한다.


## Tag 재진입의 footer target — 실제 실패 및 부분 보강

- 기본 strict `2026-10-01T18-29-26-010Z-22980dac/pass-1`은 58 pass/실제 fail 1/경고 0, 종료 1이다. 저장된 태그 이름 검사 후 `block-tags-open`에 no visible element가 발생했다. HTTP 500/browser unhandled error는 없었다. 픽셀 경고로 분류하지 않는다.
- 새 전용 진단 환경의 같은 recorded prefix 56 step은 실패·경고 없이 해당 재진입까지 실행됐다. 현재 DOM/AX는 편집기 footer의 버튼이 실제 보이고 enabled이며 404×37 frame임을 확인했다. 숨겨짐·이동의 정확한 원인은 재현되지 않았으며 제품 고장이나 사용자 조작 때문이라고 확정하지 않는다.
- footer를 향한 실제 editor wheel → 버튼 visible → 실제 클릭 → manager identity를 녹화하고 semantic scroll-to/visible/tap을 memo-editor 범위로 정리했다. 태그 manager 진입 3곳에 재사용한다. 기존 이름/색상/선택/해제/삭제/저장·재진입 검사는 유지한다.
- 재진입 태그 chip 검사에는 memo-editor scope를 추가해 같은 이름의 목록 미리보기 chip이 대신 통과하는 것을 막았다. frame 기반 scope가 실제 footer를 포함함을 DOM/AX로 확인했다. 정리 tail 36 step 뒤, 수정된 전체 Tag cycle 81 pass/실패·경고 0을 재생했다: `.cache/vault-e2e/tag-ready-cycle-replay.log`.
- 이는 부분 flow 증거이며 fresh 전체 qualification은 다시 실행한다. manifest 9 stage/89 source, weak target 0. 진단 prefix/tail은 실행 manifest 밖의 증거 폴더에 보존하고 소유 서비스와 secret lease를 정리했다.


## 공통 editor 진입 준비의 적용 범위

- `2026-10-01T18-39-08-142Z-c2e1d587/pass-1`은 새 Tag 보강을 포함해 무료 588 pass/실제 실패 0, canonical 성공이었다. Tag 첫 재진입과 신규 Checklist editor의 작은 변화 경고 2건으로 기본 strict 종료 2, 후속 미실행이다. Tag 버튼 실제 실패는 재발하지 않았다.
- 녹화한 editor-entry-ready 단편을 memo-editor identity/idle 진입과 Tag cycle의 body 재진입 idle에 공통 적용했다. 실제 identity, app-save-idle, stableFor1000/timeout15000을 유지한다. manifest의 transitive source 조회에서 89 source/11 entry, 원래 editor identity 바로 뒤의 ungated idle pair 0을 확인했다. 기능 acceptance를 제거하지 않는다.
- flow 디렉터리에는 현재 manifest가 참조하지 않는 과거 파일도 있다. 두 디렉터리에 같은 basename 원본이 항상 있다고 가정한 스크립트가 과거 qa-web-block-tags 파일에서 ENOENT를 보고했다. 이후 manifest에서 run 관계를 재귀 조회하여 현행 89 source의 변경/조립을 확인했다. 다음 자동 수정은 디렉터리 전체보다 실제 manifest 그래프를 기준으로 한다.
- 기본 strict 전체 실행을 계속 검증한다. 픽셀 안정화 경고만으로 후속 기능을 차단할지에 대한 사용자 선택 질문도 남겼다. 답변 전에는 기존 통과/실패 판정을 변경하지 않는다.


## inline Block 저장 및 text identity 재진입 준비

- `2026-10-01T18-47-39-205Z-111c05ce/pass-1`은 무료 610 pass/실제 실패 0, canonical 성공, 경고 1개(code 2)다. Time table 신규 일정 내용 input 뒤의 idle에서 작은 pixel 변화가 보고됐다. inline 결과 검사와 idle을 삭제하지 않고 녹화한 shared editor-entry-ready의 실제 save 준비 대기를 Time table/Color tracker 입력 뒤에 연결했다.
- 이후 기본 strict `2026-10-01T18-54-10-549Z-bf06a066/pass-1`은 무료 616 pass/실패 0, canonical 성공이었다. 추가한 inline 준비 대기가 입력을 막지는 않았고 해당 경고는 없었다. 대신 Color tracker 재진입의 text-in memo-editor identity 다음 기존 idle에서 경고 1개가 남았다. `visible memo-editor` pair만 수정한 이전 자동화가 text identity 진입을 놓쳤다.
- manifest의 run graph만 재귀 방문해 text-in memo-editor 및 memo-row 클릭 직후 memo-body identity의 실제 재진입 10곳에도 같은 준비 단편을 연결했다. 기존 본문/제목 hard check는 유지한다. 현재 source는 89개이며 selector audit weak target 0이다.
- 반복되는 초반 warning 때문에 후반 기능이 계속 미실행되는 것을 줄이려고, 최신 변경은 내부 진단 옵션으로 새 9단계를 실행한다. actual UI/canonical 실패는 계속 차단하고 passing-step idle warning은 최종 code 2로 보존한다. 사용자의 통과 정책 답변이 없으므로 기본 3명령의 strict 판정은 변경하지 않았다. 최신 진단 로그는 `.cache/vault-e2e/web-current-journey-diagnostic.log`이며 결과 완료 전 성공으로 기록하지 않는다.


## 구매 영수증 가설 정정 및 최신 전체 진단 진행

- PurchaseReceipt 소스에서 구매 완료 문구는 Dialog 내부 상태임을 확인했다. 최신 실행의 hidden 대기는 18ms였다. hidden 검사는 실제 Dialog 종료 후 상태 제거를 확인하지만 추가 애니메이션 대기나 경고 해결 근거가 아니다. 두 상품 targeted pass를 전체 Market 경고 해결로 일반화하지 않는다.
- `2026-10-01T19-01-51-590Z-a54e234c/pass-1`은 최신 shared editor 준비를 포함하여 무료 Memo 646 pass/실패·경고 0이었다. 관리자 62 pass/경고 1, 일반 등록 34/0, Quest 구매 20/0, Market 1,015/13, 유료 Diary 72/0, 소유권 11/1, 장식 439/2까지 실제 실패와 canonical 실패는 없었다. Quest도 1,085 pass/실패 0/경고 1로 완료했다. 전체 3,384 pass/실제 실패 0/idle 경고 18, canonical 9단계 모두 통과, 최종 종료 2다. pass 2는 미실행이다.
- Market 경고 13개 중 1개는 최초 catalog-ready 뒤의 idle, 12개는 구매 Dialog 종료 후 market-screen 뒤의 idle이다. 관리자 경고는 Google 이메일 input visible 뒤이며 실제 입력 전이다. 위치는 확인했지만 해당 pixel 변화의 원인은 캡처로 확정하지 않았다. 앱 커서 CSS가 외부 Google 화면까지 고친다고 주장하지 않는다.

- 최종 `summary.json`과 `pass-1/app.json`은 내부 diagnostic=true 및 종료 2를 보존한다. 실제 Quest 7,700점과 1–10단계 사용권을 확인했다. HTTP 500은 0건이었다. 실행 종료 후 localhost:3000/CDP:9223의 소유 listener가 없음을 확인했다. 영수증 fragment의 잘못된 auto-dismiss 설명만 in-dialog removal로 정정하고 다시 조립했으며 executable step은 변경하지 않았다.
- 후속 전체 실행을 무조건 반복하지 않는다. 남은 pixel 경고를 별도 기록할지에 대한 사용자 선택이 아직 없으므로 기본 strict 정책은 유지한다. 기능·저장 검증 성공과 두 번 무경고 qualification 완료를 분리해 보고한다.


## Google 자동 포커스 커서 및 Market 이미지 준비 — 새 진단

- `2026-10-01T19-35-37-205Z-27eefe6f`의 새 소유 환경에서 개발 로그인 9개가 무경고 통과했다. 실제 Google identifierId 입력 이전에 정상 UI를 검사했다. input은 empty/focused, caret-animation=auto, CSS animation=[]이며 연속 PNG의 변경은 20픽셀 x640/y492–511로 input rect 안의 세로 커서였다. 앱 CSS나 OS 전역 설정을 외부 Google 화면에 주입하지 않았다.
- 실제 headingText 클릭으로 BODY focus를 확인했고 identifier visible/idle을 live 녹화했다. 새 google-identifier-ready 단편은 3 pass/실패·경고 0이다. 이후 기존 실제 credential 입력/consent/관리자 발급 33개 및 로그아웃 12개가 모두 무경고 통과했고 UI 다운로드 4개를 읽기 전용 발급 membership/미사용 상태와 확인했다. PNG/evidence와 raw 기록은 해당 실행에 보존한다. 전체 fresh OAuth 단계 재검증은 아직 필요하다.
- 진단용 regular 재생의 첫 키 입력은 도구 서버에 secret이 전달되지 않아 입력 전에 차단됐다. CLI child 환경만으로 이미 떠 있는 Argent 서버의 secret source가 바뀌지 않는다. 소유/hash guarded 임시 .env.local 전달 방식을 적용하고 같은 regular User의 정상 UI를 재진입해 23 pass/실패·경고 0으로 등록했다. 검사나 session/balance를 주입하지 않았다. root 종료 전에 임시 파일을 hash 검사 후 반드시 제거한다.
- Market 초기 데이터 준비 뒤의 live 캡처와 idle 뒤 캡처 사이 상품 카드 x295–1153/y876–1164에 44978픽셀이 바뀌었다. 예전처럼 모든 경고를 커서 또는 알림이라 단정하지 않는다. 진입은 artwork decode/crop/React DOM paint를 별도로 기다려야 한다. 임시 screenshot 쌍을 추가한 진단은 6 pass/경고 0이지만 캡처 자체의 시간을 정식 readiness 대체로 채택하지 않는다.
- useArtworkReady는 기존 trim 작업·fonts·보이는 img decode·paint 경계 완료를 확인한다. data/선택 Dialog/view 세대가 달라지면 이전 준비 결과를 무효화한다. 기존 CSS/표시 이미지를 숨기거나 idle을 제거하지 않는다. masthead 준비와 sticky toolbar의 artwork-ready 표식에 연결했고 marker exists를 실제 UI에서 녹화했다. 수정된 진입은 4 pass, marker fragment 연결 뒤 6 pass 모두 실패·경고 0이다. 소유 DB에서 먼저 구매한 두 상품 이후 남은 57개를 정상 구매하며 수정된 복귀 gate를 검증 중이다. 이 진단은 전체 qualification이 아니다.

- 남은 57개 정상 구매 재생은 1089 pass/실제 실패 0, 복귀 idle 경고 10개였다. 읽기 전용 원장에서 전체 59개 구매와 잔액 94100을 확인했다. 이 진단에서는 Quest 구매를 하지 않았으므로 qualification의 93100 잔액과 다르며 정상 전체 순서/성공으로 세지 않는다. marker가 초기 진입을 개선한 사실과 복귀 경고 미해결을 구분한다.
- 보유한 새나곰 스티커 07을 열고 닫는 실제 경로를 별도 녹화했다. Recorder의 bare text가 다른 요소를 잡아 좌표를 남겼으므로 원본을 증거로 보존하고 실제 source button에 text/role/market-screen scope를 적용했다. 단편 10 pass 및 변경 없는 8회 재생 72 pass 모두 실패·경고 0이었다. 구매 없이 닫기만으로 항상 재현되는 문제는 아니며, 실제 구매 뒤의 추가 반영 원인은 아직 확정하지 않았다.
- 이미지 gate는 incomplete img의 실제 load/error 이벤트를 먼저 기다리고, viewport 이미지 decode 뒤 첫 layout에서 요청된 fonts.ready도 다시 기다린다. unmount/version 취소는 AbortController로 image listeners를 정리한다. search 문자열 변경도 준비 세대를 무효화하며 기존 scroll-reset 조건은 변경하지 않는다. 수정된 Market 진입은 6 pass/실패·경고 0이다. 이 추가 보강의 fresh 구매 뒤 경고 결과는 별도 확인한다.
- 13개 임시 진단 flow를 소유 evidence-flows로 옮기고, hash guarded license .env.local을 제거한 뒤 recording root만 SIGINT로 정리했다(130은 의도한 authoring 종료). 저장된 진단 stream/log를 common redactor와 실행의 private values로 마스킹하고 임시 redaction input도 제거했다. 최신 executable helper는 Google/Market ready 2개이며 정식 manifest 그래프에만 조립한다. 다음 전체 실행은 소스를 변경하지 않고 주력 suite와 Web 진단을 이어서 검증한다.


## Quest 첫 진입 CDP 타임아웃 — 컴파일 준비 보강

- `2026-10-01T20-12-50-198Z-6e22a6a6`의 test:vault:all은 주력 53/53/미실행 0으로 성공했다. Web 무료 646 pass/실제 실패 0/idle 경고 1(최초 새 Memo), 관리자 65/실패·경고 0, 일반 등록 34/실패·경고 0까지 저장 검증도 성공했다. Google 자동 포커스 보강은 전체 단계에서 검증됐다.
- 다음 Quest의 open-url은 Page.navigate timeout으로 error(code 2)이었고 이후 5단계 및 pass 2는 미실행이다. 로그는 같은 시점에 `Compiling /app/quest ...`이며 Quest HTTP 응답 완료는 없다. HTTP500은 0이다. 이 증거로 브라우저 crash를 확정하지 않고 cold compilation 중 navigate budget이 끝난 경로를 우선 수리한다.
- 기존 login/Memo/Diary anonymous GET compile 준비를 Quest/Market/사용하는 Settings/Admin license route까지 확장했다. 인증 cookie를 넣지 않고 GET만 수행하며 UI 로그인·구매·저장·canonical 검사는 그대로 실행한다. 기존 60초 request/120초 startup bound와 중단 신호를 유지한다. 실패한 CDP 명령을 같은 창에서 반복하지 않고 정리된 새 runner/프로필로 한 번 재검증한다. 준비 완료와 실제 Quest navigation 결과를 각각 확인한다.
- 최초 Memo 경고는 editor/save marker 뒤의 작은 변화였으며 646개 내용/저장 검사는 모두 성공했다. 앱 커서 CSS가 있으므로 이번 경고도 커서라고 단정하지 않는다. 첫 editor의 별도 픽셀/상태 진단이 아직 필요하다.

## 2026-10-02: 사전 컴파일 확장 후 무료 태그 재진입 실패

- 실행 `2026-10-01T20-26-10-848Z-9dc3d652`: 주력 suite 53/53, 무료 Memo 68개 통과 후 태그 재진입 검사에서 1개 실패, 경고 0, 전체 code 1. 보고서는 `.cache/vault-e2e/2026-10-01T20-26-10-848Z-9dc3d652/pass-1/web-free-memo.json`. 후속 8단계와 두 번째 pass는 미실행이다.
- 첫 실패 index 69는 `block-tag-chip within memo-editor`의 `#Journey renamed` 검사다. `within`은 DOM ancestry가 아닌 frame containment이므로 실제 저장 누락과 화면/선택 문제를 분리해야 한다. 원인은 아직 확인되지 않았다.
- 새 녹화 환경 `2026-10-01T20-32-35-468Z-62283d20`에서 동일 무료 흐름의 태그 구간은 통과하고 후속 블록 검사로 진행했다. 재현되지 않은 문제를 제품 저장 실패나 해결 완료로 기록하지 않는다. 정상 화면에서 실제 칩 위치를 확인하는 별도 재현이 필요하다.

- 동일 무료 여정 authoring 재생: 646개 기능 검사 통과, 실패 0, Color Tracker normalize-clock index 511 idle 경고 1. 별도 태그 재진입 prefix 44개 통과(편집기 entry idle 경고 1), suffix 43개 통과/경고 0. 실제 편집기 DOM에 `#Journey renamed` 칩이 있고 frame도 memo-editor 안에 있었다. 기존 태그 검사/제품 코드는 바꾸지 않았다.
- footer button의 text 직접 검사 대안은 live AX label이 “태그 편집”이라 실패했다. 이 실패 take는 실행 dependency에 넣지 않고 ignored evidence로 보존했다. AX label과 DOM descendant text를 혼동하지 않는다.

## Quest 날짜의 재사용 범위

녹화 경로의 2026년 9월 날짜/10월 캡션을 controlled fixture 토큰으로 바꿨다. 실행기의 호스트 local month 기준 직전 달 1–21일을 선택하며 실제 UI 이동·작성·보상 행위는 유지한다. `quest-calendar.json`으로 실행 기준을 남기고 미해결 토큰과 실행 중 월 변경을 거부한다. 연도 경계/윤년/누락 binding은 기존 runner 테스트에서 확인한다. 다른 달의 실제 브라우저 검증은 아직 하지 않았다.

- binding 변경 후 주력 suite는 55/55, 실패·미실행 0으로 통과했다: `.cache/vault-e2e/2026-10-01T20-42-42-118Z-ce139969/suite.json`. 동일 실행의 Web 결과는 별도 보고서로 판단한다.

- 다음 fresh 실행 `2026-10-01T20-42-42-118Z-ce139969/pass-1`에서는 무료 646 pass/실패 0/최초 Memo entry 경고 1, Admin 65·등록 34·Quest 구매 20은 실패/경고 없이 통과했다. Quest 첫 진입의 cold compile 타임아웃은 확장한 사전 컴파일 준비 이후 이번 실행에서는 발생하지 않았다. 전체 반복 안정성은 후속 단계와 두 번째 fresh pass가 끝나야 판단한다.

- 동일 실행 Market 전체 구매 1135 pass/실패 0/idle 경고 15, 유료 Diary 72 pass/실패·경고 0, 소유권 15 pass/실패 0/idle 경고 1이었다. 각 단계 canonical 저장 검증은 성공했다. load/error 및 font/paint 대기 보강은 구매 복귀 경고를 해결하지 못했다. 이미지 변경의 기존 entry 증거와 실제 purchase-return 경고를 같은 원인으로 간주하지 않으며 추가 추측 패치 대신 실패 구간의 픽셀 변화 증거를 수집한다.

- 장식 단계는 453 pass/실패·경고 0으로 완료했고 canonical 30개 저장 검증도 성공했다. 이전 Sticker library 경고는 이번 표본/실행에서는 발생하지 않았다. Quest 날짜 토큰은 현재 Asia/Seoul 기준 실제 직전 달 라벨로 materialize됐으며 unresolved 토큰은 없었다.

- 사전 준비의 익명 `/admin/entitlement/license-key` GET은 HTTP 200 스트림 안에서 인증 redirect를 발생시켰고, Next가 `Could not validate instant` / `An error occurred while attempting to validate instant UI`와 `NEXT_REDIRECT` cause를 출력했다. 실제 Admin 65개 UI 검사는 통과했다. 준비 단계에서 이 익명 Admin 요청을 제거하고, 실제 Google 인증 이후의 UI 진입으로 해당 경로를 검증한다. 관리자 권한 검사를 완화하거나 로그를 일괄 숨기지 않는다.

## 월 고정 제거 이후 전체 기능 증거

`2026-10-01T20-42-42-118Z-ce139969`: 주력 suite 55/55, Web 9단계 3525 pass/기능 실패 0, canonical checkpoint 9개 성공. 59개 Market 구매 후 잔액 93100, 장식 30개, 실제 Quest 7700포인트와 보상 1–10 수령을 검증했다. 날짜 토큰은 9월 1–21일 실제 UI 작성 경로에서 실행됐다. 다른 달 runtime은 미검증이다.

경고는 최초 Memo entry 1, Market purchase-return 15, Market ownership entry 1, Quest remaining rewards index 1061의 1로 총 18이다. default strict 기준에서 code 2이고 두 번째 pass는 미실행이다. 보고서: `.cache/vault-e2e/2026-10-01T20-42-42-118Z-ce139969/pass-1/app.json`, root `summary.json`. 최초 Memo/Market의 짧은 fresh 재현으로 픽셀 변화 영역을 수집하며, 동일 전체 여정을 원인 수정 없이 반복하지 않는다.


## 최초 Memo 경고의 픽셀 범위와 hover 진단

- `2026-10-01T21-15-08-723Z-1231c627`의 수동 첫 진입 이후 PNG 5장은 인접 쌍 모두 변화 0이었다. 늦은 수동 관찰로 자동 진입 경고가 해결됐다고 주장하지 않는다.
- 새 소유 환경 `2026-10-01T21-19-49-455Z-2d321de6`의 짧은 자동 first-entry 흐름은 19 pass/기능 실패 0/idle 경고 1로 재현됐다. 자동 캡처 전후 3277픽셀(x530–599/y319–1177)이 변했다. 본문 caret 영역이 아니라 Memo create 버튼과 목록 오른쪽 경계다. 버튼 안의 RGB 차이는 최대 30이며 경계는 대체로 1이다.
- 추가 Memo를 실제 UI로 만든 뒤 readonly DOM 검사에서 create 버튼 hover=true였고, 잠시 후에도 hover=true/disabled=false였다. 추가 클릭 없이 screenshot/idle fragment만 실행한 뒤 hover=false로 바뀌고 gradient도 hover 색상에서 기본 색상으로 돌아갔다. 이 짧은 warm 재생은 5 pass/경고 0이다. 캡처/대기 도구가 hover 상태 변화와 함께 발생한다는 증거이며 첫 cold 경고의 모든 원인이나 Market 경고 원인까지 확정한 것은 아니다.
- 버튼의 reduced-motion transition-none은 이미 실제 computed style에 적용돼 있었다. 커서/로딩/잘못된 스타일로 단정해 제품 코드를 고치지 않는다. 캡처의 idle 경고와 기능·저장 실패를 구분하는 정책은 사용자 답변 전까지 바꾸지 않는다. Market 실제 purchase-return 캡처를 추가로 수집한다.


## E2E 프로필 잔여물과 디스크 공간 부족

- `2026-10-01T21-19-49-455Z-2d321de6`의 실제 Google Admin 발급 65개는 성공했다(로그아웃 후 login idle 경고 1). 발급 다운로드를 읽고 handoff를 저장할 때 `ENOSPC`가 발생했다. 이때 사용 가능한 공간은 117MiB였다. 발급 UI/DB 실패로 혼동하지 않는다.
- 프로세스 인자를 읽어 현재 실행에서 쓰지 않는 과거 `vault-e2e-*` Chrome/OAuth 임시 프로필 3개를 확인한 뒤 해당 테스트 산출물만 제거했다. 사용자 파일·현재 프로필·전용 DB·저장된 보고서는 유지했다. 이후 667MiB가 사용 가능했고 같은 handoff 저장은 성공했다.
- 다음 실행 전 디스크 여유와 소유 프로세스를 확인한다. 공간을 확보한다고 사용자 캐시나 전역 Argent 산출물을 무차별 삭제하지 않는다. 실행 중인 프로필은 보존하고 잔여 테스트 프로필도 활성 프로세스가 없음을 확인해야 한다. 현재 저용량 디스크에서 전체 두 pass를 완료했다고 주장하지 않는다.


## hover 변화에 대응하는 실제 입력 포커스 — 수정 후보

- 같은 fresh authoring 환경에서 최초 Market 상품 2개는 자동 캡처를 포함해 각각31/21 pass, 경고0, PNG차분0이었다. 3개째는 캡처를 추가하지 않은 원래 구매 fragment로14 pass/경고0, 4개째 복귀도8 pass/경고0이었다. 이것만으로 전체15건의 Market 경고가 해결됐다고 판단하지 않는다.
- 최초 readonly observer의12초 식은 CDP 응답 예산을 초과했다. 실제 구매는 성공했으므로 앱 freeze의 근거가 아니다. 관측 시간을 줄이고 긴 evaluate를 반복하지 않는다.
- Market 복귀 후 기존 검색 input을 실제 UI로 클릭하는 fragment를 live 녹화했다. 검색값26을 바꾸지 않았고 caret=manual, hover button=0이었다. 생성ID는 관측한 text/role input으로 polish했다. 다음10개 상품을 이 focus와 기존 receipt/artwork/idle checks로 구매한 결과210 pass/실패·경고0이었다. readonly 원장은100 Token 상품14개와1000 Token의2026 다꾸 퀘스트1개, 총2400 지출/잔액97600을 확인했다. Quest 역시MARKET source/catalog에 속하므로 총transaction15개를 장식 상품15개로 보고하지 않는다.
- Market receipt 복귀에 이 focus를 추가했다. 최초 Memo는 실녹화한 title click을 editor identity 후·기존 save/idle 전에 두는 전용 fragment를 추가했다. title을 바꾸지 않고 focus=memo-title/create hover=false/caret=manual을 확인했다. 공통 editor 전체에는 적용하지 않고 최초 신규 Memo만 대상으로 한다.94 source/9 stage/weak selector0으로 재조립했다.
- 이 결과는 짧은 수정 후보의 증거다. 새 fresh 최초 Memo 및 변경 없는 전체2pass는 아직 완료되지 않았다. idle 삭제나 warning 성공 변환을 하지 않았다. license env lease를 hash 확인 후 해제하고 소유 recording root를130으로 종료·정리했다.


## 실행별 Next.js 빌드 캐시 누적 — ENOSPC 원인 수정

- `2026-10-01T21-49-04-596Z-b30c4c7c`의 최초 Memo21개는 성공했지만 이후 Turbopack flush가 `apps/nextjs/.cache/vault-e2e/<runId>/dev/cache/turbopack/...sst`에서 ENOSPC를 냈고 recording root가134로 종료됐다. 정식all은 로그 파일 생성도 실패하여 시작하지 못했다. 이 실패를 전체qualification 실패나 최초Memo 기능 실패로 혼동하지 않는다.
- 실제 실행별 Next.js distDir 캐시30GB가 누적돼 있었다. runApp cleanup은 Chrome profile만 정리하고 Next.js cache를 정리하지 않았다. cwd-relative distDir는 repo root `.cache/vault-e2e` 보고서 디렉터리와 다르므로 위치를 정확히 확인한다.
- dead root의 PID/명령/프로필로 확인한 orphan Next/Chrome process group만 종료하고 Argent는chromium-cdp-9223만 정리했다. 전용cache 중 기존 보고서와 이름이 일치하고 현재 Next가 없는 종료된 실행의 build cache만 제거했다. 보고서·실녹화·사용자파일은 보존했다.
- 실행기는 app cache path와 환경runId 일치를 검증하고, 자기 실행cache directory를 새로 생성해 소유권을 확보한다. 이미 있는directory는 채택·삭제하지 않는다. 자신이 생성한cache만 프로세스 정리 성공 후 삭제한다. 정상종료·중단에서 실제cache 삭제를 별도recording 환경으로 확인하고 주력suite를 재검증한다.

- cache cleanup 수정 후 새 recording `2026-10-01T21-55-19-450Z-b16fa577`은 ready가 됐고 SIGINT 후130으로 종료했다. cache는 종료 전exists=true/종료 후exists=false였다. 증거는 같은run의`build-cache-cleanup.json`. 종료된 기존build cache30개를report ownership 확인 후 정리해 여유 공간이 약29GiB로 회복됐다. 주력suite 재실행 종료0.


## 최초 Memo 보강 후 strict 결과와 Clock normalization의 준비 누락

- `2026-10-01T21-57-28-297Z-cc1e4fdc`의 정식all은 주력55/55, 무료Memo650 pass/실제실패0이었다. 최초Memo 경고는 없었지만 ColorTracker normalize-clock index527에서 idle경고1로 code2가 됐고 나머지8단계/두 번째pass는 미실행이다. 실제원장은 무료단계 canonical을 통과했다. HTTP500/ENOSPC0이며 종료 후 해당pass build cache가 제거됐음을 확인했다.
- 경고는 종료시간의 오전선택→시간combobox→clock-picker-options identity 뒤였다. 기존 ColorTracker 본 시나리오에는 실관측한 native overlay scrollbar fade의clock-popup-ready 단편이 있었지만 normalize-clock의options open3곳에는 연결이 빠졌다. 기존 단편을3곳에 재사용하며 identity·idle·선택시간 acceptance를 유지한다.
- 새 소유환경 `2026-10-01T22-05-00-976Z-91fa40e9`에서 ColorTracker만 검증한다. 다른 무료기능650개를 불필요하게 반복하지 않는다. 진단의 최초Memo 준비만 녹화된first-memo entry로 설정하며 전체qualification으로 간주하지 않는다.

- 같은 recording의 ColorTracker 진단은181 pass/실제실패·경고0으로 완료했다. clock-popup-ready를normalize3곳에 재사용했고 기존09:05–10:05 선택·저장·재진입 검사를 유지했다. 전체9단계2pass는 별도로 실행한다.


## 새 Memo의 입력 준비를 같은 생성 경로에 적용

- `2026-10-01T22-08-59-861Z-fce508a0`의 정식all은 주력55/55, 무료659 pass/실제실패0/idle경고1로 code2였다. normalize-clock 경고는 없었고 경고index274는 신규Checklist Memo 생성 다음 editor-entry-ready였다. 이후 제목Web checklist memo 입력과 나머지기능/canonical은 모두 성공했다.
- 최초Memo에만 입력focus를 적용했던 누락을 보완한다. 실녹화된같은title focus를 새Memo identity/save/pixel readiness의 전용new-memo-entry-ready에 사용한다. manifest graph에서 memo-create 바로뒤editor-entry-ready인6곳(lifecycle/Highlight/Checklist/TimeTable/Sticker/ColorTracker)과 기존최초Memo를 연결했다. inline입력후·일반재진입 readiness는 바꾸지 않는다. 원래내용·저장·목적지·idle 검사를 유지한다.94 source/9stage/weak target0으로재조립했다.
- 새실행의 결과를 확인하기전 이확장을전체성공으로기록하지않는다. 원인이확인된최초Memo hover 증거의같은 UI 생성경로에적용하는수정이며 모든 pixel경고를hover라고일괄분류하지않는다.

- `2026-10-01T22-17-31-002Z-cc9d3441/pass-1`의 정식전체실행에서 무료Memo683 pass/실패·경고0, canonical 성공을 확인했다. 새Memo생성7경로와 clock normalization보강이전체무료여정에서도실행됐다. 관리자·유료후속단계와두 번째pass는아직진행중이므로전체완료가아니다.

- 같은 정식 실행의Admin65/등록34/Quest구매20은실패·경고0이었다. Market59개구매는1253 pass/실제실패0/canonical성공이지만복귀idle경고5개로code2이며뒤4단계/두 번째pass는미실행이다. 검색focus는이실행에서경고를없애지못했다. 경고상품은새나노트26스티커02, 소프트키치26스티커04/09/11/14였다. 준비/포커스추측패치를더하지않고해당상품의실제구매복귀를짧게재현한다.

## Market 연속 구매 경고의 픽셀 증거 (2026-10-02, 조사 중)

- `2026-10-01T22-36-36-086Z-a22d7ef3`의 fresh authoring 환경에서 기존 경고 상품 5개는 부분 재생 시 모두 경고 없이 구매됐다. 나머지 54개 연속 구매는1148 pass/기능 실패0/idle 경고3이었다. 개별 재생 성공을 전체 안정화 해결로 보고하지 않는다.
- CLI는 이미 실행 중인 공유 Argent 서버에서 flow를 처리하므로 CLI의 preload만으로 내부 capture를 수집할 수 없다. 설치된 도구 파일과 공유 서버는 변경하지 않았다. 별도 owned 진단 runtime에서 기존 idle PNG를 비동기로 복사했고 settle 알고리즘/경고 기준은 그대로였다. 추가 파일 기록의 timing 영향은 남으므로 정식 qualification은 일반 runner로 수행한다.
- 이미 보유한52개 상품의 녹화된 열기/닫기 경로만 반복한 진단은780 pass/idle 경고5를 재현했다. 구매·receipt의 성공 증거가 아니며 같은 User/DB를 유지한 진단이다.실제 idle 프레임 및 `pixel-intervals.json`, `warning-frame-map.json`, `owned-closures-before-isolation.stream`을 보존했다.
- 경고 interval에서 변한 픽셀은 rotated/multiply-blended ‘보유 중’ 도장 주변에 모여 있었다. 검색 caret이나 spinner로 단정하지 않는다. 카드 mat에 `isolate`를 추가하는 작은 합성 범위 변경을 실험 중이며, 실제 computed isolation59개/reduced-motion=true/running animation0을 확인했다. 결과가 없거나 외관이 바뀌면 이 단일 변경을 되돌린다. 정확한 브라우저 원인과 정식 두 번 qualification은 아직 미확인이다.
- 진단용 별도 Argent 서버는 해당 copied bundle/PID를 확인하고 그 서버의 Chromium9223 서비스만 정리한 뒤 종료한다. 일반 공유 tool-server 또는 사용자 Chrome을 종료하지 않는다. 진단 flow는 최종 manifest에 포함하지 않는다.

- Product mat isolation 후 같은52-dialog 진단은780 pass/경고4였다. 도장 영역의 localized pixel 변화는0으로 줄었고 남은 작은 차분은 캡처의 마지막 raster row에만 있었다. 정확한 원인으로 확정하지 않고, quarter-scale 경계가 반 픽셀인 현재 viewport1182px를 재검증했다.
- 전용 Chrome startup에 `--window-size=1200,1271`을 추가한 fresh geometry preflight `2026-10-01T23-23-33-893Z-d6a9483c`는 실제 inner1200×1184/outer1200×1271/DPR1/reduced-motion=true를 확인했다. ¼ 캡처가300×296 정수 경계가 되었다. 9개 launch도 같은 live startup args로 묶고94 source/weak target0 재조립했다. source를 고정하고 일반 `pnpm test:vault:all`로 재검증한다. snapshot 크기나 실제 viewport는 OS/Chrome 상태에 따라 달라질 수 있으므로 startup flag 존재만으로 안정화 해결을 주장하지 않는다.
- 이전 owned recording과 별도 진단 Argent runtime을 종료했고 root/diagnostic evidence는 보존했다. private 진단 log/stream16개를 자격증명·OAuth·license 마스킹/0600으로 정리했다. viewport preflight도130으로 정상 소유 정리했다. 현재 정식 전체 두 번 qualification은 아직 완료되지 않았다.

- 정수 viewport 수정 후 정식 실행 `2026-10-01T23-25-26-423Z-8a4210d7`: main55/55, 무료Memo683/경고0, Admin Google65/경고0. 일반 등록34/기능 실패0/canonical 잔액100000은 확인했지만 공통 development-login 진입 idle(index4)에 localized 경고1건으로 code2 종료했다. Quest 이후/두 번째 pass는 미실행이다. 실제 viewport1200×1184/DPR1을 다시 확인했다. 이번 정식 실행에서는 Market 수정 후 구매를 아직 검증하지 못했다. 경고를 무시해 전체 성공으로 보고하지 않는다.

## 빈 로그인 페이지의 작은 배경 paint 차이 (2026-10-02, 미해결)

- fresh authoring `2026-10-01T23-34-51-450Z-1acf68ed`에서 자격증명을 입력하지 않고 `/login` 진입을20번 반복해60 pass/idle 경고1을 재현했다.61개 실제 idle PNG를 보존했다. 경고 프레임의33 changing pixels는 오른쪽 정적 sky background와 glass preview 사이(x161–169/y126–157, scale¼)에 모여 있다. 커서·spinner·인증값 변화의 증거는 아니다. 정확한GPU/filter 원인은 미확인이다.
- preview aside의 isolation을 실험한 동일20-entry 진단은60 pass/경고3으로 개선되지 않았다. 똑같은33-pixel 영역이 반복돼 해당 실험의단일class를 되돌렸다. production login behavior를 바꾸거나 애니메이션/blur를 제거하지 않았다. `login-entry-before-isolation.stream`, `login-warning-pixels.json`, `pixel-intervals.json`, PNG를 보존한다.
- 이 경고는 기본 strict qualification을 계속 막는다. 기능/canonical 성공과 pixel readiness 부족을 구분한다. 동일전체여정을추측패치뒤무작정반복하지 않는다.

- 같은 fresh authoring User의 로그인9/Admin발급65/등록34/Quest구매20은 경고0으로 통과했다. Market전체 구매1253 pass/실제 실패0을 확인했다. readonly application catalog/원장은 59×100 Token 상품과1×1000 Token Quest, 지출6900/잔액93100을 검증했다. 구매 복귀 idle 경고는0이었지만 상품 dialog 진입 idle6건이 남아 Market 전체 안정화 해결은 아니다. source가 같아도 픽셀 경고가 다른 scene에서 발생할 수 있음을 기록한다.
- license 임시 `.env.local` lease를 정상 삭제하고 private 진단 log/stream9개를 마스킹/0600으로 정리했다. recording은130으로 소유 정리했다. 미해결 pixel 경고 기준을 사용자에게 확인 중이며, 답변 전 기본 strict 판정을 바꾸지 않는다. 소프트웨어 렌더링 비교는 별도 진단이며 하드웨어 렌더링 정확성을 검증하지 않는다.

## 소프트웨어 합성으로 capture 안정화 비교 (2026-10-02)

- fresh recording `2026-10-01T23-57-00-187Z-0ca48e3b`의 owned Chrome에 진단 wrapper로 `--disable-gpu`를 전달했다. Argent에서 `chrome://gpu`를 읽어 Canvas/Compositing/Rasterization이software-only/hardware disabled인 것을 확인했다.
- 같은 빈 로그인 진입을100번 반복해300 pass/실패·경고0, 실제311 idle PNG의 localized interval0을 확인했다. 하드웨어 합성과의 관련성을 지지하지만 특정driver/shader 원인을 확정한 것은 아니다.
- 기본 E2E startup과9개 live launch args에 `--disable-gpu`를 명시하고 window/reduced-motion·strict warning·모든 기능/저장 checks를 유지했다. UI 제품 코드의 mat isolation 실험은 되돌렸다.94 source/9 stage/weak target0 재조립했다. suite는software-rendering 기능 검증이며 GPU 하드웨어 검증은 아니다. 일반 공개명령으로 두번 qualification은 아직 남았다.
- 별도진단 runtime과recording을 소유범위로 종료하고 증거를 보존했다. qualification에는copy runtime/preload/wrapper를 사용하지 않는다.

- software-rendering 정식 `2026-10-02T00-06-35-144Z-d1d0e798`: main55/55, 무료Memo683/기능 실패0/canonical성공이지만 tag re-entry의 공통 editor-entry-ready idle(index72)에 localized 경고1로code2 종료했다. software rendering으로 모든 scene 경고가 해결됐다고 주장하지 않는다.
- 이 재진입은 exact Memo body 뒤 generic editor/save/idle를 먼저 수행하고 저장된 `#Journey renamed` 표시를 나중에 검사했다. 기록된 동일scoped tag check를 generic readiness 앞으로 이동해 read-side Tag 표시를 먼저 기다리게 했다. 기능/저장/absence checks는 삭제하지 않았다. full fresh qualification과 이 순서변경의 경고해소는 아직 미검증이다.

- Tag check 순서 수정 후 fresh recording `2026-10-02T00-19-11-944Z-d13429f7`에서 동일 일반 User의 실제 첫 Memo 작성·Tag 전체cycle prefix115 pass/실패·경고0을 확인했다. 생성/이름·색상변경/재진입 membership·color/해제·재부착·삭제/없음 확인을 유지했다. 부분 여정이며 전체/두 번째pass는 아니다. 증거를 보존하고recording은130으로 정리했다. 새로운소스로strict 전체qualification을 다시 실행한다.

## Checklist 구조 추가의 저장 readiness (2026-10-02, 검증 중)

- 정식 실행 `2026-10-02T01-02-37-016Z-80743035`: main55/55, 무료Memo683/기능 실패0/canonical성공. Tag 재진입 경고는 없었지만 Checklist 첫 input 표시 직후 idle(index296)에 localized 경고1로 code2 종료했다. 후속8단계와 pass2는 미실행이다.
- 이 지점은 구조 추가 후 input 존재만 확인하고 저장 완료 전에 pixel idle을 호출했다. 이미 기록된 `qa-vault-web-editor-entry-ready`를 재사용해 Memo identity와 `app-save-idle` 확인 뒤 기존 pixel readiness를 수행하도록 변경했다. 타이핑·체크/해제·저장·재진입·삭제 검증은 유지했다. 경고의 정확한 pixel 원인은 미확인이고, 이 변경의 해결 여부는 부분/정식 재실행으로 검증한다.

- 수정 후 fresh recording `2026-10-02T01-10-22-653Z-8bf787ad`에서 같은 일반 계정의 Checklist 전체cycle을 두 번 이어 실행했다.135 pass/실패·경고0. 첫 입력/두 항목/체크·해제/재진입 상태/휴지통 뒤 없음 확인을 유지했다. 부분 여정 증거이며 정식 전체 qualification은 아니다. 진단 YAML과 마스킹 stream을 보존하고 recording은130으로 소유 정리했다.

- `2026-10-02T01-13-09-431Z-2aae1eba`: 주력55/55, 무료Memo686/Admin65/등록34/Quest20은 실패·경고0. Market1253/기능 실패0/canonical성공이나 복귀 pixel warning8로 과거 strict code2 종료했다. 뒤4단계와 pass2는 미실행이다. software rendering으로 모든 capture warning이 해결되지는 않았다. 사용자 판정 변경 후에도 이 과거 보고서를 통과로 바꾸지 않고 새 환경에서 두 번 검증한다.

- 픽셀 정책 변경 후 `2026-10-02T01-29-12-035Z-24707bb5`: 주력55/55(허용 범위/다른 경고/실패·skip 차단 회귀 포함). Chrome launch는 성공했지만 첫open-url이`CDP not connected`로 실행되지 않아 Web code2이며 모든 후속 단계/pass2는 미실행이다. 이를 로그인 기능 실패나 전체 합격으로 분류하지 않는다. 실행 종료 뒤 scoped Chromium 서비스 정리는stopped=[]를 반환했다. 다음 실행에서 연결을 재검증한다.
