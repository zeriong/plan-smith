<p align="center">
  <strong>plan-smith</strong>
</p>

<p align="center">
  <strong>대장간처럼 플랜을 벼린다 — 메인이 의도를 증류하고, 깨끗한 컨텍스트의 집필자가 플랜을 벼리고, 원문 그대로 전달한다.</strong>
</p>

<p align="center">
  <a href="https://github.com/zeriong/plan-smith/releases"><img src="https://img.shields.io/badge/version-1.0.0-blue" alt="Version"></a>
  <a href="https://opensource.org/licenses/MIT"><img src="https://img.shields.io/badge/License-MIT-green.svg" alt="License: MIT"></a>
  <a href="https://docs.claude.com/en/docs/claude-code/plugins"><img src="https://img.shields.io/badge/Claude%20Code-Plugin-orange" alt="Claude Code Plugin"></a>
</p>

<p align="center">
  <a href="#설치">설치</a> &bull;
  <a href="#무엇을-하는가">무엇을 하는가</a> &bull;
  <a href="#파이프라인">파이프라인</a> &bull;
  <a href="#프레임과-스타일">프레임과 스타일</a> &bull;
  <a href="#faq">FAQ</a>
</p>

<p align="center">
  <a href="README.md">English</a> &bull;
  <a href="README.ko.md">한국어</a>
</p>

---

긴 세션은 구조적인 이유로 나쁜 플랜을 낳습니다: 당신의 의도를 가장 잘 아는 에이전트가, 동시에 컨텍스트가 가장 오염된 에이전트이기 때문입니다 — 수십 번의 도구 출력, 반복된 파일 덤프, 막다른 시도들. 거기서 플랜을 쓰면 잡음을 통과해 쓰는 것이고, 신선한 서브에이전트에서 쓰면 의도를 잃습니다.

**plan-smith는 일을 쪼개서 이 트레이드오프를 거부합니다.** 메인 에이전트는 자신의 유일한 자산인 세션 컨텍스트를 전부 *의도 증류*에 소진해 구조화된 컨텍스트 패킷을 만듭니다. 깨끗한 컨텍스트의 `plan-writer` 에이전트가 그 패킷으로부터, 실증 검증된 추론 프레임 라이브러리와 두 가지 집필 스타일의 지도를 받아 플랜을 벼립니다. 완성된 플랜은 **요약 없이 원문 그대로** 전달됩니다.

## 무엇을 하는가

- **메인은 집필하지 않고 의도만 증류** — 스킬이 대화 전체에서 목표, 하드 제약, **기각된 대안**, 이미 결정된 사항, 관련 파일을 패킷으로 추출합니다. 추출(신호 골라내기)은 집필보다 컨텍스트 잡음에 훨씬 강건합니다.
- **확인 게이트 1회** — 집필 전에 패킷을 보여줍니다: *"제가 파악한 의도와 제약입니다 — 맞습니까?"* 추측한 필드는 전부 `⚠guess`로 표시되어 함께 확인됩니다. 잘못된 의도로 깨끗하게 쓴 플랜보다, 의도를 교정할 기회가 있는 파이프라인이 항상 이깁니다.
- **격리 집필** — `plan-writer`는 세션 잡음 없이 패킷 + 프레임 스펙 + 스타일 지시만 들고 작업합니다. 코드베이스에는 read-only이며, 지정된 출력 파일(플랜, relay 패스 2에서는 audit.md 포함)만 쓰고 경로만 반환합니다.
- **제값 하는 추론 프레임 라이브러리** — 26개 플랜 실증 실험에서 증류하고 100플랜 검증 코퍼스로 스트레스 테스트한 25개 프레임(인수조건 역산, 프리모템, 봉투 계산, 제약 우선, 인센티브 회계 등). 각 프레임에는 **필수 부품**(없으면 프레임이 장식으로 퇴화하는 요소)과 자동 선택용 라우팅 술어가 딸려 있습니다.
- **검증된 집필 스타일 2종 + relay** — `opus` 스타일(망라 우선의 규율형 초안 + 정직한 자백 로그)과 `fable` 스타일(구조를 감사하는 개정 + 판단의 규칙화). **relay 모드**는 둘을 직렬 연결합니다: 초안 → 자백 → T1~T6 오염 회계를 동반한 적대적 개정 — 원 실험에서 관측된 가장 강한 파이프라인입니다.
- **무손실 전달 + 회고 데이터** — 플랜 파일 전문이 그대로 제시되고, 결과(채택/수정/기각)가 패킷에 축적되어 프레임 라우팅을 다듬는 데이터가 됩니다.

## 설치

### Claude Code 플러그인 마켓플레이스로

1. Claude Code에서 `/plugin` 실행.
2. Marketplaces → Add Marketplace.
3. URL 입력: `https://github.com/zeriong/plan-smith.git` (또는 이 레포의 로컬 경로).
4. `plan-smith` 설치.

### 또는 CLI로

```bash
claude plugin marketplace add https://github.com/zeriong/plan-smith.git   # 또는 로컬 경로
claude plugin install plan-smith@plan-smith-marketplace
```

### 또는 `~/.claude/settings.json`에 직접 연결

```json
{
  "extraKnownMarketplaces": {
    "plan-smith-marketplace": {
      "source": { "source": "git", "url": "https://github.com/zeriong/plan-smith.git" }
    }
  },
  "enabledPlugins": { "plan-smith@plan-smith-marketplace": true }
}
```

## 사용법

```
/plan-smith:plan-smith 세션 스토어를 Redis에서 Postgres로 이전
/plan-smith:plan-smith frame=premortem style=relay 유료 뉴스레터 런칭 계획
/plan-smith:plan-smith style=fable 배포 런북 점검·보강
```

자연어로 요청해도 됩니다 — *"X 플랜 짜줘"*가 스킬을 트리거합니다. 인자:

| 인자 | 값 | 기본 |
|---|---|---|
| `frame` | [frames.md](plugins/plan-smith/skills/plan-smith/references/frames.md)의 아무 프레임 | 4개 술어로 자동 라우팅, 근거 기록 |
| `style` | `opus` \| `fable` \| `relay` \| `auto` | `auto` — 태스크 신호로 라우팅, 근거 기록 |

산출물은 `plans/<slug>/`에 저장됩니다: `packet.md`, `plan.md` (relay 모드에서는 + `draft.md`, `audit.md`).

## 파이프라인

```
1단계 — 의도 증류 (메인 에이전트)
  대화 전체 ──▶ 컨텍스트 패킷 ──▶ 사용자 확인 게이트 («⚠guess» 필드 해소)

2단계 — 격리 집필 (plan-writer, 신선한 컨텍스트)
  패킷 + 프레임 스펙 + 스타일 지시 ──▶ plans/<slug>/plan.md
  relay: opus 스타일 초안(+자백 로그) ──▶ fable 스타일 개정(+T1~T6 오염 회계)

3단계 — 무손실 릴레이 (메인 에이전트)
  플랜 전문 제시 ──▶ 사용자 판정 ──▶ 회고 1줄을 패킷에 축적
```

분업이 작동하는 이유: *원하는 플랜*은 세션 컨텍스트를(1단계의 자원), *오염 없는 집필*은 격리를(2단계의 자원), *뭉개지지 않는 전달*은 파일 규약을(3단계의 규칙) 요구합니다. 한 에이전트는 셋을 다 가질 수 없지만, 파이프라인은 가질 수 있습니다.

## 프레임과 스타일

- **[frames.md](plugins/plan-smith/skills/plan-smith/references/frames.md)** — 6개 계열(역산 / 부정·실패 / 정량·제약 / 진단 / 다관점 / 형태) 25개 프레임. 각각 시작점·필수 부품·실패 모드·코퍼스가 남긴 watch-outs 포함. 라우팅은 4개 술어로: *불확실성의 위치 / 자원의 경직성 / 실행자의 인지 여유 / 척도 합의 가능성*.
- **[styles.md](plugins/plan-smith/skills/plan-smith/references/styles.md)** — 두 집필 규율과 relay 프로토콜. 스타일은 프롬프트 수준의 규율이지 **모델 선택이 아닙니다** — 어떤 모델에서든 동작하도록 설계되었습니다(모델 간 이식성은 회고 데이터로 검증 중인 가설입니다).

## FAQ

**왜 메인이 그냥 플랜을 쓰면 안 되나요?** 의도를 알게 해준 바로 그 세션이 컨텍스트를 오염시켰기 때문입니다. 추출은 잡음을 견디지만 집필은 못 견딥니다. plan-smith는 오염된 컨텍스트를 그것이 여전히 잘하는 유일한 일에만 씁니다.

**서브에이전트가 대화의 뉘앙스를 잃지 않나요?** 그래서 패킷이 있습니다 — 목표, 제약, *기각된 대안*(집필자가 다시 제안하지 않도록), 결정 사항, 파일별 요점까지, 집필 전에 사용자가 게이트에서 직접 확인합니다.

**relay는 언제 2배 비용의 값을 하나요?** 되돌리기 어렵거나 폭발 반경이 큰 결정, 몇 달을 실행할 플랜. 감사 기록(`audit.md`)이 개정이 무엇을 바꾸고 무엇을 유지했는지, 왜인지를 남기며 — 최종 병합 권한은 사용자에게 있습니다.

**스타일의 근거는 무엇인가요?** 통제 실험입니다: 동일한 26개 플래닝 과제를 서로 다른 두 모델 지문으로 수행하고 비교 감사했습니다. 감사가 각 지문을 재현 가능한 집필 지시로 증류했고, 둘의 직렬 연결(relay)이 그 실험에서 가장 강한 산출물을 냈습니다 — 단 1회 관측 근거라, 파이프라인은 relay를 '입증'이 아닌 '유망'으로 취급하고 회고를 축적해 검증합니다.

## License

MIT
