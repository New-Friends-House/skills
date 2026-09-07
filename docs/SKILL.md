---
name: docs
description: Append or toggle a numbered work-log item, append ideas/backlog/known bugs, archive past work logs by week, or create a cheatsheet. Use when the user invokes /docs with daily|idea|backlog|bug|daily-archive|sheet, including /docs daily 13 2 to toggle that day's item.
disable-model-invocation: true
---

# Docs → 작업일지 / 아이디어 / 백로그 / 알려진 버그 / 과거 작업일지 / 치트시트

`/docs` 직후 **첫 토큰**으로 대상을 고른다. 토큰은 본문에서 뺀다. 첫 토큰이 없거나 아래 표에 없으면 중단하고 대상을 묻는다.

| 첫 토큰              | 대상                           |
| -------------------- | ------------------------------ |
| `daily`              | 오늘 작업일지                  |
| `daily-archive`      | 과거 작업일지 (주차별 보관)    |
| `idea` · `아이디어`  | 개요 아이디어 페이지           |
| `backlog` · `백로그` | 개요 백로그 페이지             |
| `bug` · `버그`       | 개요 알려진 버그 페이지        |
| `sheet`              | 개요 치트시트 하위 페이지 하나 |

예: `/docs backlog 알림 정리` → `backlog.mdx`. `/docs idea 검색 엔진` → `idea.mdx`. `/docs bug 메모 저장이 안 됨` → `known-bugs.mdx`. `/docs daily 리포: 정리` → 오늘 작업일지에 추가. `/docs daily 13 2` → 이번 달 13일 2번 토글. `/docs daily-archive` → 지난 작업일지를 주차별 과거 작업일지로 보관. `/docs sheet eas whoami` → `cheatsheet/<slug>.mdx`.

---

## Checklist targets (`daily` · `idea` · `backlog` · `bug`)

할일·버그를 `- [ ]` 한 줄로 추가한다. `daily`와 `backlog`와 `bug`는 번호가 붙는다. `## 할일` 같은 섹션 헤딩은 쓰지 않는다.

### Toggle (`/docs daily <일> <번호>`)

`daily` 뒤 나머지가 **정확히 두 토큰**이고, 첫째가 일(1–31), 둘째가 항목 번호(`2` 또는 `2-1`)이면 **추가하지 않고 토글**한다.

- 날짜: `Asia/Seoul` **이번 달**의 그 일. 파일 `work-log/YYYY-MM-DD.mdx` (일은 두 자리).
- 대상 줄: `- [ ]` / `- [x]` 바로 뒤 번호가 둘째 토큰과 같다 (`2.` ↔ `2`, `2-1.` ↔ `2-1`).
- 동작: `[ ]` ↔ `[x]`. 문구·번호·카테고리는 그대로.
- 파일이 없거나 번호가 없으면 중단하고 말한다. 일지만 만들지 않는다.

예: `/docs daily 13 2` → `2026-08-13.mdx`의 `2.` 토글. `/docs daily 13 2-1` → 같은 날 `2-1.` 토글.

### Paths

#### 작업일지 (`daily`)

- Dir: `apps/nextjs/content/docs/general/work-log/`
- Note: `YYYY-MM-DD.mdx` (timezone `Asia/Seoul`)
- Index: `work-log/meta.json` (`pages` 배열)

#### 아이디어 (`idea`)

- Note: `apps/nextjs/content/docs/general/idea.mdx`
- Index: `apps/nextjs/content/docs/general/meta.json` (`pages`에 `"idea"`)

#### 백로그 (`backlog`)

- Note: `apps/nextjs/content/docs/general/backlog.mdx`
- Index: `apps/nextjs/content/docs/general/meta.json` (`pages`에 `"backlog"`)

#### 알려진 버그 (`bug`)

- Note: `apps/nextjs/content/docs/general/known-bugs.mdx`
- Index: `apps/nextjs/content/docs/general/meta.json` (`pages`에 `"known-bugs"`)

### Line shape

아이디어:

```markdown
- [ ] <item>
```

작업일지 · 백로그 · 알려진 버그. 번호는 체크박스 뒤, 카테고리 앞:

```markdown
- [ ] 1. <item>
- [ ] 1-1. <item>
```

카테고리가 붙으면:

```markdown
- [ ] <라벨> (<이모지>): <나머지>
```

작업일지 · 백로그 · 알려진 버그면:

```markdown
- [ ] 1. <라벨> (<이모지>): <나머지>
```

- 새 할일: 대상 파일의 마지막 **상위** 번호 + 1 (`1.`, `2.`, …).
- 이어 붙이기: 부모 `N` 아래 다음 `N-M.` (`1-1.`, `1-2.`). 사용자가 부모를 지정했을 때만.

### Category (이모지)

대상(작업일지 · 아이디어 · 백로그 · 알려진 버그)과 관계없이, 줄이 아래 **여섯 카테고리 중 하나**에 해당하면 `라벨 (이모지):` 형태로 쓴다. 목록에 없는 영역은 이모지를 만들지 않는다.

| 라벨 | 이모지 | 명시 접두사 | 맥락으로도 인정하는 말 (예) |
| --- | --- | --- | --- |
| `리포` | 📦 | `리포:` | 모노레포·패키지 구조, `apps/`·`packages/` 정리처럼 저장소 자체 작업 |
| `문서함` | 📚 | `문서함:` | 어드민 문서함·개요·치트시트·docs 페이지 작성 |
| `다이어리` | 📅 | `다이어리:` | 다이어리 화면·일정 UI |
| `캘린더` | 🗓️ | `캘린더:` | 캘린더 화면·월/주 뷰 |
| `메모` | 📝 | `메모:` | 메모 편집·메모 편집창·메모 섹션·메모 블록 편집 |
| `DB` | 🗄️ | `DB:` | 스키마·마이그레이션·page table·컬럼·DB 테이블 작업 |

#### 적용 순서

1. **이미 `라벨 (이모지):` 형태**면 그대로 둔다. 이모지를 바꾸거나 중복으로 붙이지 않는다.
2. **명시 접두사** (`리포:` / `문서함:` / `다이어리:` / `캘린더:` / `메모:` / `DB:`)로 시작하면 표의 고정 이모지로 바꾼다. 접두사 뒤 문구는 그대로 남긴다.
3. 접두사가 없어도 **문장만으로 카테고리가 분명하면** 같은 표로 붙인다.  
   예: `메모 편집에서 ⌘+Enter로 …` → `메모 (📝): ⌘+Enter로 …`  
   “메모 편집창”, “메모 섹션”도 메모로 본다. 접두사를 붙인 뒤 앞쪽에 중복된 “메모 편집/창/섹션” 수식은 빼서 한 번만 말하게 한다.  
   “page table”, “스키마”, “마이그레이션”도 DB로 본다.
4. **어느 칸인지 애매하면** 이모지 없이 쓴다. 추측으로 새 라벨·이모지를 만들지 않는다.

### 아이디어 · 백로그 · 알려진 버그 — Rewrite (not paste)

대상이 아이디어·백로그·알려진 버그일 때. 사용자 말을 **의도**로 읽고 짧은 한 줄로 **다시 쓴다**. 원문 복붙 금지. 발명 금지. 아이디어·백로그는 할일, 알려진 버그는 증상·조건을 한 줄로.

다시 쓴 뒤에도 Category 규칙을 적용한다. 카테고리가 보이면 `라벨 (이모지):`를 붙인 최종 한 줄이 체크리스트에 들어간다.

### Workflow (checklist)

1. Parse after `/docs`: required target token (`daily` / `idea` / `아이디어` / `backlog` / `백로그` / `bug` / `버그`), then the rest.
2. **작업일지 토글:** `daily` 뒤가 Toggle 패턴이면 해당 일지 그 번호만 뒤집고 끝낸다. 추가 워크플로로 가지 않는다.
3. **작업일지 추가 (`daily`):** Resolve today as `Asia/Seoul` `YYYY-MM-DD`. Category 규칙을 적용한 뒤, Line shape대로 번호를 붙여 쓴다. Ensure note exists:

```mdx
---
title: YYYY-MM-DD
description: 작업일지
---
```

Ensure `work-log/meta.json` `pages` includes today's date at the front. Keep `"title": "오늘 작업일지"` and `"defaultOpen": true`.

4. **아이디어 / 백로그 / 알려진 버그:** Rewrite each line, then Category 규칙을 적용한다. 백로그·알려진 버그는 Line shape대로 번호를 붙인다. Ensure the note exists:

```mdx
---
title: 아이디어
description: 나중에 해볼 아이디어 메모
---
```

```mdx
---
title: 백로그
description: 나중에 할 일 백로그
---
```

```mdx
---
title: 알려진 버그
description: 확인된 버그 기록
---
```

Ensure `general/meta.json` `pages` includes `"idea"` / `"backlog"` / `"known-bugs"` (after `"cheatsheet"`, keep existing order) if missing.

5. Append each item after the last `- [ ]` / `- [x]` in the target body (or right after the frontmatter blank line if none).
6. Reply with the note path and the lines added, or the toggled line. No commit unless asked.

### Rules (checklist)

- Edit only the chosen target note and its index (`work-log/meta.json` or `general/meta.json`).
- Never invent items; only what the user supplied (plus category emoji or rewrite).
- Duplicate in the same target file: skip and say it already exists. Compare after rewrite/category.
- Multiple items in one invocation → multiple `- [ ]` lines (토글은 한 번호만).
- 작업일지·백로그·알려진 버그 추가는 번호를 빠뜨리지 않는다. 토글은 번호를 바꾸지 않는다.
- Do not add section headings. Do not touch non-checklist content.
- Category 이모지는 위 표 여섯 개만. 맥락이 분명할 때만 붙이고, 애매하면 생략한다.

---

## Archive target (`daily-archive`)

지난 작업일지를 주차별(예: `8월 2주차`)로 묶어 **과거 작업일지**에 보관한다. 월단위 폴더나 페이지로 묶지 않고, 주차별 페이지로 직접 보관한다.

### Paths

- Folder: `apps/nextjs/content/docs/general/work-log-archive/`
- Folder index: `work-log-archive/meta.json` (`pages` 배열, `"title": "과거 작업일지"`, `"defaultOpen": false`)
- Parent index: `apps/nextjs/content/docs/general/meta.json` (must include `"work-log-archive"` after `"work-log"`)

### Page shape

- Note: `work-log-archive/YYYY-MM-wN.mdx` (예: `2026-08-w2.mdx`)
- Title: `M월 N주차` (예: `8월 2주차`)
- Description: `과거 작업일지`

```mdx
---
title: 8월 2주차
description: 과거 작업일지
---

## 2026-08-13

- [x] 1. ...

## 2026-08-12

- [x] 1. ...
```

- 주차 내에서는 최신 날짜(`## YYYY-MM-DD`)가 위로 오도록 내림차순 정렬한다.

### Workflow (`daily-archive`)

1. `apps/nextjs/content/docs/general/work-log/`에서 오늘 이전(또는 보관 대상)의 작업일지 파일들을 읽는다.
2. 각 날짜가 속한 주차(월요일~일요일 기준 `M월 N주차`, slug `YYYY-MM-wN`)를 계산한다.
3. `work-log-archive/YYYY-MM-wN.mdx`가 없으면 생성하고, 이미 있으면 해당 날짜 섹션을 날짜 내림차순 순서에 맞춰 추가/갱신한다.
4. 보관된 날짜 파일(`YYYY-MM-DD.mdx`)은 `work-log/`에서 삭제하고 `work-log/meta.json`의 `pages`에서 제거한다.
5. `work-log-archive/meta.json` `pages`에 최신 주차 slug가 앞에 오도록 등록한다.
6. `general/meta.json` `pages`에 `"work-log-archive"`가 없으면 `"work-log"` 뒤에 추가한다.
7. 보관된 주차 페이지 경로와 날짜 목록을 알린다. No commit unless asked.

---

## Sheet target (`sheet`)

어드민 문서함 **개요 → 치트시트**에 CLI·개발 명령 하위 페이지를 만든다. `/docs sheet` 호출마다 **새 MDX 페이지 하나**. Category 체크리스트 규칙은 적용하지 않는다.

### Paths

- Folder: `apps/nextjs/content/docs/general/cheatsheet/`
- Folder index: `cheatsheet/meta.json` (`pages` 배열)
- Parent index: `apps/nextjs/content/docs/general/meta.json` (must include `"cheatsheet"`)

### Page shape

```mdx
---
title: <짧은 제목>
description: <한 줄 요약>
---

\`\`\`bash <명령어> \`\`\`

<한 문장 설명>
```

한 페이지에 관련 명령이 여러 개면 `##` / `###`로 단계·플랫폼을 묶는다. 호출 단위가 페이지 단위다 — 항목마다 파일을 쪼개지 않는다.

### Rewrite (not paste)

사용자 말을 **의도**로 읽고, 제목·설명을 **치트시트용으로 다시 쓴다**. 원문 복붙 금지.

- **제목** — 무엇을 하는 묶음인지 한 줄로. 군더더기·구어체 빼기.
- **명령** — 사용자가 준 문자열 **그대로**. 플래그·경로·환경변수 추가·삭제·추정 금지.
- **설명** — 언제·왜 쓰는지 한 문장. 명령이 이미 말하는 내용은 반복하지 않기.
- 명령만 오면 설명 생략. 설명만 오면 코드 펜스 없이 본문만.

완료 기준: 모르는 사람이 제목+설명만 보고 언제 치면 되는지 알고, 코드 펜스 문자열은 사용자 제공과 byte-for-byte 같다. 새 파일이 `cheatsheet/meta.json` `pages`에 들어 있다.

### Workflow (sheet)

1. Parse `/docs sheet` 뒤 내용을 하나의 주제(페이지)로 정리. 제목·slug·본문(명령 verbatim + 설명)을 만든다.
2. slug는 제목의 kebab-case ASCII (`eas-cli`, `agents-skills-update`). 같은 slug 파일이 있으면 스킵하고 이미 있다고 말한다.
3. Ensure folder + `cheatsheet/meta.json` exist. If missing:

```json
{
  "title": "치트시트",
  "defaultOpen": true,
  "pages": []
}
```

4. Write `cheatsheet/<slug>.mdx`.
5. Append `"<slug>"` to `cheatsheet/meta.json` `pages` (keep existing order).
6. Ensure `general/meta.json` `pages` includes `"cheatsheet"` after `"toc"` if missing.
7. Reply with the new page path and title. No commit unless asked.

### Rules (sheet)

- Edit only under `cheatsheet/` plus, when needed, `general/meta.json`.
- Do not rewrite unrelated pages.
