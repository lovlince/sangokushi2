# 삼국지 2 (3DS) 한글패치 작업 문서

이 저장소는 **작업 인수인계와 재현 가능한 검수**를 위한 Markdown 문서 보관소입니다.
게임 원본, ROM, 추출물, 패치 바이너리, 리빌드·배포 산출물은 포함하지 않습니다.

## 다음 작업을 시작할 때

아래 순서로 읽으면 현재 기준과 금지 사항을 빠르게 파악할 수 있습니다.

1. [`docs/PATCH_HANDOFF.md`](docs/PATCH_HANDOFF.md) — 현재 기준 revision, 직전 작업, 다음 검증 항목
2. [`docs/PROJECT_RULES.md`](docs/PROJECT_RULES.md) — 원본/패치 경계, 빌드 및 안전 규칙
3. [`docs/WORK_LOG.md`](docs/WORK_LOG.md) — revision별 작업·검증 이력
4. [`docs/번역.md`](docs/%EB%B2%88%EC%97%AD.md) — 번역 권위, 용어 및 문자열 작업 지침

## 현재 기준

- 최종 작업 기준: **v220-intermediate**
- 마지막 문서 갱신: **2026-09-08**
- 실제 작업 루트: `E:\Codex_01\Sangokushi2`

문서에 기록된 패치 기준과 실제 작업 폴더의 상태가 다를 수 있으므로, 수정 전에는 `PATCH_HANDOFF.md`의 검증 절차를 먼저 실행합니다.

## 문서 역할

| 문서 | 용도 |
| --- | --- |
| `PATCH_HANDOFF.md` | 새 작업자·에이전트용 최신 인수인계 |
| `PROJECT_RULES.md` | 수정 범위와 검증 규칙 |
| `WORK_LOG.md` | 결정과 결과의 누적 이력 |
| `번역.md` | 번역 작업의 기준 문서 |

## 주의

- `Sangokushi 2 Original`은 절대 수정하지 않습니다.
- 실제 수정은 `Sangokushi 2 Patch`에만 적용합니다.
- 리빌드·패키징·배포는 사용자 요청이 있을 때만 다룹니다.
- GitHub 이슈 작업은 해당 이슈의 로컬 HTML과 첨부 PNG를 함께 확인합니다.
