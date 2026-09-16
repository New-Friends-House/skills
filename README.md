# New Friends House Skills

`pnpx skills add`로 설치하는 공유 에이전트 스킬 저장소입니다. 스킬은 `.agents/skills/<skill-name>/`에 관리합니다.

## 다른 프로젝트에 설치하기

설치할 프로젝트의 루트에서 실행합니다.

```sh
# 설치할 스킬 선택
pnpx skills add New-Friends-House/skills

# 설치 가능한 스킬 목록 확인
pnpx skills add New-Friends-House/skills --list

# 특정 스킬을 Codex용으로 설치
pnpx skills add New-Friends-House/skills --skill expo-animation --agent codex
```

프로젝트 단위 설치가 기본이며, 전역 설치에는 `--global`을 추가합니다.

## 이 저장소 관리하기

Node.js 22.20.0 이상과 pnpm 10.33.4를 사용합니다.

```sh
pnpm install

# 현재 로컬 저장소에서 배포할 스킬 목록 확인
pnpm skills:list

# 외부 저장소의 특정 스킬을 이 저장소에 추가
pnpm skills add owner/repo --skill skill-name --agent codex
```

외부 스킬을 추가하면 `.agents/skills/`의 파일과 생성·갱신된 `skills-lock.json`을 함께 커밋합니다. `pnpm-lock.yaml`은 CLI 패키지 버전을, `skills-lock.json`은 CLI로 추가한 스킬의 출처를 기록합니다.

직접 작성하는 스킬은 `.agents/skills/<skill-name>/SKILL.md`를 만들고 YAML frontmatter에 `name`과 `description`을 작성합니다.

```md
---
name: my-skill
description: 이 스킬이 하는 일과 사용하는 상황
---

# My Skill

에이전트가 따를 지침을 작성합니다.
```

스킬에 필요한 `scripts/`, `references/`, `assets/`는 해당 스킬 폴더 안에 둡니다. 새 스킬을 추가한 뒤 `pnpm skills:list`로 검색되는지 확인합니다.

```text
.agents/
  skills/
    expo-animation/
      SKILL.md
      references/
    ...
package.json
pnpm-lock.yaml
README.md
```

GitHub를 통한 설치는 원격 저장소의 파일을 사용하므로 변경 사항을 커밋하고 푸시해야 반영됩니다. npm 패키지 배포는 필요하지 않습니다. 로컬 변경 사항은 다른 프로젝트에서 `pnpx skills add /absolute/path/to/skills --skill expo-animation --agent codex`로 설치할 수 있습니다.

명령 옵션과 검색 경로는 [skills CLI 공식 문서](https://github.com/vercel-labs/skills#skill-discovery)를 참고하세요.
