# Flux Conductor

이 문서는 [English](README.md)로도 제공됩니다.

여러 GitHub 저장소에 걸친 AI 에이전트 작업을 계획하고 조율하기 위한 지침 문서
모음입니다. 작업 상태의 영구적인 기준은 GitHub 이슈와 중앙 GitHub Project이며,
나머지는 모두 파일 하나를 통해 사용자의 환경에 맞춰집니다.

## 이 저장소가 아닌 것

이 저장소는 문서만 담고 있습니다. 제품 코드도, 봇도, 스케줄러도, GitHub
Actions도 들어 있지 않습니다. 여기 있는 어떤 것도 스스로 실행되지 않습니다.

## 의존성

사용하기 전에 아래 항목을 설치하십시오. 템플릿은 이들이 설치되어 있다고
가정합니다.

**필수**

| 의존성 | 이유 |
|---|---|
| [git](https://git-scm.com/) | 이 저장소와 대상 저장소의 버전 관리 |
| GitHub 계정 | 이슈와 중앙 프로젝트가 존재하는 곳 |
| [GitHub CLI](https://cli.github.com/) | 이슈, 프로젝트, 풀 리퀘스트 조회와 갱신 |
| 에이전트 CLI 하나 | 이 지침을 읽고 실제로 작업을 수행하는 에이전트 |

**권장**

| 의존성 | 이유 |
|---|---|
| [Superpowers](https://github.com/obra/superpowers) | 설계, 계획, TDD, 리뷰, 검증 워크플로 |
| [Ponytail](https://github.com/DietrichGebert/ponytail) | 요구사항을 만족하는 가장 작은 구현 선택 |
| [Karpathy Guidelines](https://github.com/multica-ai/andrej-karpathy-skills) | 가정 점검, 최소 범위 변경, 검증 가능한 목표 |

오케스트레이터 가이드의 디스패치 게이트는 권장 베이스라인이 설치되어 활성화되어
있는지 확인합니다. 먼저 설치하시기를 강력히 권장합니다. 설치하지 않으면 그
게이트는 적용되지 않으며, 워크플로가 전제하는 리뷰와 검증 규율을 잃게 됩니다.

**선택**

실행 배치, 작업, 디스패치, 워커 수명 주기를 관리하는 오케스트레이터입니다.
오케스트레이터가 없으면 에이전트 하나가 의존성 순서대로 전부 실행합니다.
`orchestrator.name`을 `none`으로 설정하면 문서가 그에 맞게 동작합니다.

설치 명령은 각 업스트림 프로젝트의 최신 안내에서 확인하십시오. 명령은 바뀌기
때문에 이 README에서는 반복하지 않습니다.

## 시작하기

1. 이 저장소를 포크합니다.
2. 포크한 저장소를 클론합니다.
3. 위의 의존성을 설치합니다.
4. 클론한 디렉터리에서 에이전트를 실행합니다.
5. 에이전트에게 `setup`이라고 지시합니다.

에이전트는 [SETUP.md](SETUP.md)의 인터뷰를 진행하고
`docs/env/ENVIRONMENT.md`를 작성합니다. 그 밖에는 아무것도 바뀌지 않습니다.

## 언어

지침 문서는 영어로 작성되어 있습니다. 설정 인터뷰에서 어떤 언어를 사용할지
묻습니다. 다른 언어를 선택하면 영어 원문을 `archive/en/`으로 옮기고 원래 경로에는
번역본을 씁니다. 영어 원문은 기준 사본으로 남습니다.

## 문서

| 문서 | 역할 |
|---|---|
| [AGENTS.md](AGENTS.md) | 진입점. 설정 확인과 문서 라우팅 |
| [SETUP.md](SETUP.md) | 초기 설정 인터뷰와 언어 전환 |
| [docs/core/OPERATING-MODEL.md](docs/core/OPERATING-MODEL.md) | 모든 환경에 공통으로 적용되는 운영 원칙 |
| [docs/integrations/TRACKER-GITHUB.md](docs/integrations/TRACKER-GITHUB.md) | GitHub 이슈와 프로젝트 규칙 |
| [docs/integrations/ORCHESTRATOR.md](docs/integrations/ORCHESTRATOR.md) | 실행 수명 주기 책임과 오케스트레이터 없는 모드 |
| [docs/workflows/PLAN-AND-DISPATCH.md](docs/workflows/PLAN-AND-DISPATCH.md) | 실행 계획에서 디스패치까지 |
| [docs/workflows/REVIEW-AND-CLOSE.md](docs/workflows/REVIEW-AND-CLOSE.md) | 리뷰에서 완료까지 |
| [docs/workflows/LOOP-ENGINEERING.md](docs/workflows/LOOP-ENGINEERING.md) | 로드맵 주기와 연속 실행 트리거 계약 |
| [docs/env/ENVIRONMENT.example.md](docs/env/ENVIRONMENT.example.md) | 환경 변수 계약 |

`docs/projects/<owner>--<repo>.md`는 온보딩한 저장소 하나에서 달라지는 점을
기록합니다. 온보딩할 때 만들고, 그 전에는 만들지 마십시오.

## 라이선스

MIT 라이선스를 따릅니다. [LICENSE](LICENSE) 파일을 참고하십시오.
