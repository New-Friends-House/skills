---
name: ota-compatiblity
description: 미커밋 변경을 포함한 현재 Sena Note 작업 디렉터리와 대상 EAS 빌드의 fingerprint를 비교해 OTA 런타임 호환성을 판단한다. 불일치하면 빌드 커밋부터 현재 작업 상태까지의 변경을 추적해 원인과 근거를 설명한다.
---

# OTA Compatibility

**현재 리포 상태의 기준은 미커밋 변경까지 반영된 실제 작업 디렉터리다.** 그 상태에서 fingerprint를 생성하고, 사용자가 지정한 EAS 빌드에 저장된 fingerprint와 비교한다. HEAD는 변경 이력을 설명하는 기준점으로만 사용한다. 불일치한 source를 Git 변경에 연결해 **무엇이 달라졌고 왜 fingerprint에 영향을 주는지** 설명한다.

## 실행 범위

사용자가 이 스킬로 검사를 요청하면 빌드 메타데이터 조회, fingerprint 생성·비교, Git 이력 분석을 수행한다. 스킬 작성이나 열람만으로 검사를 실행하지 않는다. 기존 source-only 모드에서도 명시적으로 요청된 이 검사 범위만 실행한다.

현재 브랜치와 작업 디렉터리를 유지한다. 진단을 위해 checkout, stash, reset, worktree 생성, 의존성 재설치, codegen, prebuild, 네이티브 빌드 또는 OTA 배포를 수행하지 않는다. 코드·fingerprint 제외 규칙·runtimeVersion 정책의 수정은 별도 요청의 범위다.

표준 EAS fingerprint 명령은 해당 EAS 프로젝트에 fingerprint와 source 메타데이터를 등록할 수 있다. 실행 안내에 이 동작을 포함한다. 사용자가 업로드 없는 검사를 지정하면, 대상 JSON을 조회하고 설치된 `expo/fingerprint` API로 로컬에서 생성·비교한다. 이 경우에도 아래 플랫폼·환경·workflow 조건을 재현해야 한다.

## 1. 비교 대상 확정

- 빌드 URL 또는 ID가 있으면 해당 빌드를 사용한다. 없다면 대화에서 플랫폼과 profile을 확인하고, `eas build:list`로 후보를 좁힌다. 사용자가 명시한 “최신 production iOS”처럼 선택 기준이 명확하면 그대로 선택한다. 여러 후보가 남으면 ID·플랫폼·profile·생성일·커밋을 보여 주고 대상을 묻는다.
- `apps/expo`에서 설치된 EAS CLI의 `--version`과 관련 명령의 `--help`를 확인한다. 아래 예시와 지원 옵션이 다르면 설치된 버전의 동작을 따른다. CLI나 인증이 없으면 그 경계를 보고한다.
- 대상의 프로젝트, 플랫폼, 빌드 상태, profile, channel, runtimeVersion, fingerprint hash, `gitCommitHash`, 생성일을 확보한다. 값이 없는 항목은 추정해서 채우지 않는다. 현재 앱의 EAS 프로젝트와 대상 프로젝트가 일치하는지 확인한다.
- 리포 루트에서 `git rev-parse HEAD`, `git status --short`를 기록한다. staged·unstaged 수정, 삭제·이름 변경, 새 파일을 반영한 디스크의 최종 상태를 사용한다. 같은 파일의 index와 작업 파일 내용이 다르면 실제 작업 파일이 기준이다. untracked·ignored 파일의 hash 포함 여부는 현재 fingerprint 규칙을 따른다.

완료 기준: 한 플랫폼의 특정 빌드와 현재 작업 상태가 식별되었다. 여러 플랫폼이면 빌드와 fingerprint를 각각 다룬다.

## 2. 같은 조건으로 현재 fingerprint 생성

`apps/expo/app.config.ts`, `apps/expo/eas.json`, `apps/expo/fingerprint.config.js`와 관련 ignore 파일을 읽는다. 대상 profile의 `extends`, 플랫폼별 설정, EAS environment와 env를 구분한다. 특히 `APP_VARIANT`와 진단·profiling 플래그처럼 앱 설정에 영향을 주는 입력을 확인한다.

기본 비교는 **미커밋 변경을 포함한 현재 작업 디렉터리에 대상 빌드와 같은 profile을 적용한 결과**다. fingerprint 생성은 해당 작업 디렉터리에서 직접 수행한다. 사용자가 예정 OTA의 환경이나 명령을 지정했다면 그것을 우선하고, 빌드 profile 비교와 예정 OTA 환경 비교가 다를 때는 결과를 구분한다. 현재 profile/env가 빌드 당시와 달라졌는지도 원인 분석에 포함한다. 원격 환경의 과거 값이 없으면 동일하게 재현했다고 단정하지 않는다.

아래 명령의 변수는 확인한 메타데이터에서 채운다. 아티팩트는 리포 밖의 임시 디렉터리에 저장한다. JSON 전체에는 설정 내용이나 민감한 값이 포함될 수 있으므로 출력·커밋하지 않고, 최종 보고에는 해시·경로·필요한 메타데이터만 사용한다.

```sh
# apps/expo에서 실행. OTA_BUILD_ID, OTA_PLATFORM, OTA_PROFILE은 확인된 값.
umask 077
OTA_ARTIFACT_DIR="$(mktemp -d "${TMPDIR:-/tmp}/ota-compatiblity.XXXXXX")"
eas build:view "$OTA_BUILD_ID" --json > "$OTA_ARTIFACT_DIR/build.json"
eas fingerprint:generate --platform "$OTA_PLATFORM" --build-profile "$OTA_PROFILE" --json --non-interactive > "$OTA_ARTIFACT_DIR/current-fingerprint.json"
```

- `--build-profile`은 profile 환경을 적용하는 경로다. `--environment`만 주는 경로와 같다고 가정하지 않는다. 두 옵션은 현재 CLI에서 상호 배타적이다.
- 의도적으로 environment 기준으로 검사할 때는 `--build-profile` 대신 `--environment`를 사용하고, 필요한 profile 전용 입력이 실제 OTA 실행에도 적용되는지 확인한다.
- 사용한 EAS CLI·Expo/fingerprint 버전, 플랫폼, profile/environment, 생성 시각을 기록한다. 필요한 환경 값을 얻지 못했다면 근사 비교임을 표시한다.
- 로컬 API로 생성한다면 앱에서 `expo/fingerprint`를 resolve하고, EAS와 같은 managed/bare workflow 및 native-directory 제외 동작을 적용한다. 편의를 위해 별도의 ignorePaths나 임의 환경 값을 추가하지 않는다.

완료 기준: 비교 조건과 출처를 설명할 수 있는 현재 fingerprint의 `hash`와 `sources`가 확보되었다.

## 3. 대상 fingerprint와 비교

빌드 메타데이터의 fingerprint hash를 `OTA_TARGET_HASH`, 방금 생성한 JSON의 hash를 `OTA_CURRENT_HASH`로 파싱한다. 원본 JSON 구조를 확인하고 값을 추출하며, stdout 텍스트에서 해시처럼 보이는 문자열을 골라 쓰지 않는다.

```sh
eas fingerprint:compare "$OTA_TARGET_HASH" "$OTA_CURRENT_HASH" --json --non-interactive > "$OTA_ARTIFACT_DIR/comparison.json"
```

두 해시를 명시하면 2단계에서 생성한 결과를 그대로 비교할 수 있다. `--build-id`만 주는 명령은 현재 코드를 다시 생성하며 대상 build profile의 env까지 적용한다고 보장하지 않는다. 또한 미등록 로컬 해시를 원격 해시 인자로 넘기지 않는다. 등록에 실패했으면 확보한 대상 JSON과 로컬 JSON을 `expo/fingerprint`의 `diffFingerprints`로 비교한다.

- 비교 명령의 종료 코드 0은 호환성 판정이 아니다. JSON에 들어 있는 두 fingerprint의 정체와 hash를 확인한다. CLI 버전에 따라 JSON은 diff 목록 대신 `fingerprint1`과 `fingerprint2`를 반환한다.
- 대상 source 아티팩트를 확보했으면 설치된 `expo/fingerprint`의 `diffFingerprints`로 added/removed/changed source를 조사한다. 원본 source 배열의 정렬을 유지한다. `filePath` 또는 contents의 `id`, 전후 hash, `reasons`를 정리하고, 디렉터리의 debug 정보가 있으면 하위 파일까지 좁힌다.
- 대상 source 아티팩트가 만료·누락되어도 신뢰할 수 있는 두 해시가 있으면 일치 여부는 판단할 수 있다. 이 경우 상세 원인은 Git 기반 추정으로 표시한다. 대상 해시 자체가 없으면 fingerprint 판정은 불가다. 로컬에서 과거 커밋을 재구성한 값을 실제 빌드 아티팩트라고 대체하지 않는다.
- `hash: null`은 제외된 source일 수 있다. 이것만으로 Nitro 미빌드나 오류라고 판단하지 않는다.
- fingerprint 일치와 실제 OTA runtimeVersion 일치를 구분한다. 대상 빌드의 runtimeVersion과 현재 정책에서 산출되는 runtimeVersion도 확인한다. 현재 정책이 fingerprint가 아니면 fingerprint 차이만으로 OTA 수신 가능 여부를 단정하지 않는다.

완료 기준: fingerprint 일치/불일치/판정 불가와 runtimeVersion 기준 호환성, 상세 source 비교의 가능 범위가 각각 명확하다.

## 4. 불일치 원인을 빌드 커밋과 연결

빌드의 `gitCommitHash`를 기준점으로 사용한다. 생성 시각 근처 커밋이나 merge-base로 대신하지 않는다. 커밋이 로컬에 없으면 기존 origin에서 해당 이력을 fetch하여 읽을 수 있으며, 작업 브랜치와 파일은 바꾸지 않는다. 이력을 얻지 못해도 확보한 fingerprint 결과는 보고하고 Git 분석만 미확인으로 남긴다.

확인된 커밋 SHA를 `OTA_BUILD_SHA`로 두고 리포 루트에서 **빌드 커밋 → 현재 작업 디렉터리의 최종 차이**를 먼저 확인한다. 그 차이를 커밋된 변경과 미커밋 변경으로 나누어 원인을 추적한다.

```sh
# 빌드 커밋 → 현재 작업 파일: 미커밋 변경을 포함한 최종 차이
git diff --find-renames --name-status "$OTA_BUILD_SHA"
git diff --find-renames "$OTA_BUILD_SHA" -- <관련-경로>
git ls-files --others --exclude-standard

# 빌드 커밋 → 현재 HEAD: 두 endpoint의 실제 차이
git diff --find-renames --name-status "$OTA_BUILD_SHA" HEAD
git diff --find-renames "$OTA_BUILD_SHA" HEAD -- <관련-경로>
git log --format='%h %s' "$OTA_BUILD_SHA..HEAD" -- <관련-경로>

# HEAD → 현재 작업 파일: staged와 unstaged를 합친 차이
git diff HEAD -- <관련-경로>

# 과거 설정·파일은 checkout 없이 읽기
git show "$OTA_BUILD_SHA:apps/expo/eas.json"
git show "$OTA_BUILD_SHA:<관련-경로>"
```

`git diff`에 나오지 않는 untracked 파일과 fingerprint가 지목한 ignored 파일도 함께 조사한다. 커밋에서 변경했더라도 미커밋 수정으로 되돌려진 내용은 최종 차이에 없을 수 있으므로 현재 불일치의 원인으로 지목하지 않는다. 빌드 커밋이 HEAD의 조상이 아니면 직접 endpoint diff는 유지하고, `git log --left-right --oneline "$OTA_BUILD_SHA...HEAD" -- <관련-경로>`로 양쪽 이력을 구분한다.

**모든 불일치 source에 대해** 연결된 변경을 찾거나 원인 미확인으로 남긴다. 관련 커밋의 제목만 읽지 말고 diff 내용을 확인한다. 디렉터리 hash만 달라졌다면 그 안의 변경 파일은 우선 원인 후보이며, 개별 파일의 hash/debug 증거가 있을 때 해당 입력의 변경을 확인된 사실로 말한다.

Sena Note에서는 다음 경계를 특히 구분한다.

- workspace native 패키지는 디렉터리 단위로 hash될 수 있다. `native-reshape` 안의 TypeScript/worklet 변경도 해당 디렉터리 fingerprint를 바꿀 수 있다. 패키지 hash 변경이 곧 Nitro C++ 구현·바인딩 변경이라는 뜻은 아니다.
- C++/Swift/Kotlin, Nitro spec·생성 바인딩, podspec/Gradle, 의존성·패치, config plugin, 네이티브 설정의 실제 변경은 그 경로와 커밋으로 설명한다.
- 진단 기능이 production에서 꺼져 있어도 `extraSources`가 진단 소스 자체를 포함하면 fingerprint에는 영향을 줄 수 있다. 현재 코드와 빌드 커밋의 설정을 모두 확인한다.
- 캐시·설치 후 생성 파일·환경·도구 버전의 차이는 Git diff에 없을 수 있다. `.gitignore`만 보고 fingerprint에서도 제외된다고 가정하지 않는다. Git에서 원인이 안 나오면 해당 source의 생성 경로와 환경 차이를 조사한다.

EAS 빌드는 당시 미커밋 파일을 포함했을 수 있으므로 빌드 커밋이 업로드된 전체 트리를 완전히 나타낸다고 단정하지 않는다. 하나의 커밋을 유일한 원인으로 지목하려면 그만큼의 증거가 필요하다. 누락된 과거 환경·아티팩트 때문에 끝내 좁히지 못한 항목은 그대로 표시한다.

완료 기준: 각 불일치 source에 파일/설정, 관련 커밋 또는 미커밋 변경, fingerprint에 포함되는 이유, 근거 수준이 연결되었다.

## 5. 보고

결론부터 사용자의 언어로 답한다. 불일치하면 단순 해시 목록으로 끝내지 않고 “빌드 커밋 이후 X가 Y로 바뀌었고, 이 입력이 Z source에 포함되어 불일치 원인으로 보인다”처럼 설명한다.

- 대상 빌드 링크·플랫폼·profile·빌드 커밋, 이력 기준인 현재 HEAD, 실제 비교 대상이 미커밋 변경을 포함한 작업 디렉터리라는 점.
- 대상/현재 fingerprint hash와 일치 여부, runtimeVersion 기준 호환 여부, 적용한 비교 환경.
- 불일치 항목별 `source/파일 | 전후 변경 | 관련 커밋 또는 미커밋 | 근거 수준` 표. **확인된 hash/파일 차이**, **커밋 diff 기반 원인 추정**, **미확인**을 구분한다.
- 현재 코드 그대로는 새 빌드가 필요한지, 환경 재현이 먼저 필요한지 등 근거에 맞는 다음 조치. 자동으로 수정하거나 배포하지 않는다.
- 실행한 명령과 남은 한계. 검사 도중 HEAD나 fingerprint 입력이 바뀌었으면 현재 값을 재생성하고, 일관된 snapshot을 얻지 못하면 완료 시점의 현재 코드에 대한 판정으로 제시하지 않는다.

일치 결과는 검사한 입력의 runtime 호환성 근거다. channel/branch 매핑, rollout, 실제 다운로드·재실행 또는 앱 동작까지 확인한 결과로 확대하지 않는다.

## 참고

- [EAS CLI: fingerprint 생성·비교](https://docs.expo.dev/eas/cli/#eas-fingerprintgenerate)
- [Expo Fingerprint API](https://docs.expo.dev/versions/latest/sdk/fingerprint/): 실제 사용 시 설치된 SDK/API 정의를 우선한다.
- [EAS Update runtime versions](https://docs.expo.dev/eas-update/runtime-versions/)
