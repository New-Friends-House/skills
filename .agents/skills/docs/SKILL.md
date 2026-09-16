---
name: docs
description: /docs 또는 $docs로 작업일지·아이디어·백로그·버그 기록, 주간 보관, 치트시트를 관리한다.
disable-model-invocation: true
---

# Docs

호출 뒤 첫 토큰으로 작업을 고르고 본문에서 그 토큰을 뺀다. 선택한 작업의 참조만 읽는다. 대상 토큰이 없거나 알려진 값이 아니면 대상을 묻는다.

| 요청 | 작업과 참조 |
| --- | --- |
| `daily <일> <번호>` | 나머지가 정확히 두 토큰이고 일은 1–31, 번호는 `2` 또는 `2-1` 형태이면 [기존 항목 토글](references/daily-toggle.md) |
| `daily <내용>` | [오늘 작업일지 추가](references/checklists.md) |
| `idea` · `아이디어` / `backlog` · `백로그` / `bug` · `버그` | [해당 체크리스트에 추가](references/checklists.md) |
| `daily-archive` | [지난 작업일지 주간 보관](references/daily-archive.md) |
| `sheet <내용>` | [치트시트 페이지 하나 생성](references/sheet.md) |

문서 루트는 `apps/nextjs/content/docs/general/`이다. 참조의 경로는 이 루트 기준이며, 날짜는 `Asia/Seoul`을 사용한다.

사용자가 제공한 내용만 기록한다. 기록할 내용이 필요한 요청에 본문이 없으면 내용을 묻는다. 선택한 문서와 필요한 인덱스만 수정하고 기존 본문·메타데이터를 보존한다. 커밋은 요청받았을 때만 한다.

요청한 변경과 필요한 인덱스 갱신이 끝나면 문서 경로와 추가·토글한 줄, 보관한 날짜 또는 생성한 페이지 제목을 알린다. 중복·누락으로 처리하지 못한 항목도 구분한다.
