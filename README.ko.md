# Skill Wizard Harness — v2.0.0

Anthropic의 [Harness Design for Long-Running Application Development](https://www.anthropic.com/engineering/harness-design-long-running-apps) (Prithvi Rajasekaran, 2026) 문서의 **Planner → Generator → Evaluator** 하네스 패턴을 적용한 Claude Code 스킬을 생성하는 메타 스킬입니다.

## 무엇을 하는가

이 위자드를 통해 새 스킬을 만들면, 사용자가 주제나 요구사항을 입력했을 때 자동으로 멀티 에이전트 루프가 실행됩니다:

1. **Planner** — 짧은 요청(1~4문장)을 상세한 제품/콘텐츠 스펙으로 확장
2. **Generator** — 협상된 스프린트 계약에 따라 반복적으로 구현
3. **Evaluator** — 실제 증거와 루브릭 기반 채점으로 스펙 대비 검증
4. 품질 임계값을 통과하거나 반복 상한에 도달할 때까지 루프

각 에이전트는 별도의 서브에이전트(`Agent` 도구)로 실행되며, 서로의 추론을 볼 수 없고 **파일로만** 소통합니다.

## 왜 하네스를 사용하는가

이 패턴이 구조적으로 방지하는 두 가지 실패 모드:

- **자기평가 편향** — LLM에게 자신의 작업을 평가하라고 하면 "자신 있게 평범한 결과물을 칭찬"합니다. 생성과 평가를 분리하는 것이 핵심 레버입니다.
- **컨텍스트 불안** — 컨텍스트가 차오르면 모델이 조기에 작업을 마무리합니다. 컨텍스트 리셋(파일 핸드오프 + 새 세션)이 이를 해결하며, 컴팩션은 해결하지 못합니다.

## 설치

`skill-wizard-harness/` 디렉토리를 Claude Code 스킬 폴더에 복사합니다:

```bash
cp -r skill-wizard-harness/ ~/.claude/skills/skill-wizard-harness/
```

## 사용법

Claude Code에서 호출:

```
/skill-wizard-harness
```

또는 자연어로 트리거:
- "마케팅 리포트 생성용 하네스 스킬 만들어줘"
- "블로그 포스트용 플래너/이밸류에이터 스킬이 필요해"
- "코드 리뷰용 멀티에이전트 스킬 만들어줘"

위자드가 7단계로 안내합니다:

```
Phase 1: 도메인 탐색          → 어떤 도메인? 품질 기준은?
Phase 2: 하네스 아키텍처       → Full / Simplified / Single-session?
Phase 3: 메타데이터 설계       → 이름 + 설명
Phase 4: 역할 프롬프트 설계    → Planner, Generator, Evaluator 프롬프트
Phase 5: 루브릭 & 계약 설계    → 평가 기준 + 스프린트 계약 템플릿
Phase 6: 오케스트레이션 로직   → 생성될 스킬의 제어 흐름
Phase 7: 생성 & 검증          → 조립, 검증, 테스트
```

## 생성되는 결과물

완전한 스킬 디렉토리:

```
<스킬-이름>/
├── SKILL.md                    # 오케스트레이션 로직 + 원칙
└── references/
    ├── planner-prompt.md       # 독립형 Planner 프롬프트
    ├── generator-prompt.md     # 독립형 Generator 프롬프트
    ├── evaluator-prompt.md     # 독립형 Evaluator 프롬프트
    └── rubric.md               # 도메인별 채점 기준
```

## 원문의 핵심 개념과 적용 방식

| 개념 | 적용 방식 |
|------|----------|
| **GAN 스타일 분리** | Generator와 Evaluator를 별도 서브에이전트 프로세스로 실행 |
| **파일 기반 핸드오프** | `spec.md`, `sprint_contract.md`, `generator_report.md`, `critique.md`, `handoff.md` |
| **스프린트 계약** | Generator가 범위 + 체크 제안 → Evaluator 승인 후 구현 |
| **컨텍스트 리셋** | 컴팩션이 아닌 `handoff.md`와 함께 새 세션 시작 |
| **루브릭 채점** | 1~5점 + 증거 필수; Claude가 약한 축에 2배 가중치 |
| **Evaluator 튜닝** | few-shot 캘리브레이션, 회의적 기본 태도, 관대함 방지 규칙 |
| **스타일 자석 경고** | 루브릭에 참조어(museum-quality) 대신 품질어(coherence, depth) 사용 |
| **V1 → V2 진화** | 복잡한 작업엔 Full 하네스, 강한 모델엔 Simplified |
| **하나씩 제거하며 측정** | 모델 업그레이드 시 각 컴포넌트를 개별 스트레스 테스트 |
| **늦은 반복의 창의적 도약** | 반복 상한 도달 시 조용히 중단하지 않고 사용자에게 에스컬레이션 |
| **중간 반복이 최종보다 나을 수 있음** | Evaluator의 "Iteration Quality Note"가 이전 반복의 장점 퇴행을 추적 |
| **stub-feature 감지** | Evaluator가 "UI 존재" vs "인터랙션이 end-to-end로 작동"을 구분 |
| **design_memo.md** | Generator가 방향 전환 시 근거를 문서화; 컨텍스트 리셋 후 기억상실 피벗 방지 |

## 하네스 단계

| 단계 | 사용 시점 |
|------|----------|
| **Full** (V1) | 복잡한 산출물, 5개 이상 기능, Sonnet급 모델 |
| **Simplified** (V2) | 단일 일관된 산출물, Opus급 모델 |
| **Single-session** | 작은 범위, 강한 모델, 속도 우선 |

## 모델별 가이드

| 모델 | 컨텍스트 불안 | 권장 단계 |
|------|-------------|----------|
| **Sonnet 4.5** | 강함 — 조기 마무리 경향 | Full 하네스 (V1) — 작은 스프린트, 공격적 Evaluator |
| **Opus 4.5** | 대부분 해소 | Simplified 하네스 (V2) — 수 시간 연속 세션 가능 |
| **Opus 4.6** | 해소됨; 계획/디버깅 개선 | Simplified 또는 Single-session |

## 반복 지혜 (원문 실험에서 얻은 교훈)

- **늦은 반복의 창의적 도약은 실재합니다.** 원문의 네덜란드 미술관 실험에서, 반복 1~9는 평범했지만 반복 10에서 3D 공간 경험으로의 breakthrough가 일어났습니다. 반복 상한 도달 시 조용히 중단하지 말고 사용자에게 에스컬레이션해야 합니다.
- **중간 반복이 최종보다 나을 수 있습니다.** Evaluator의 "Iteration Quality Note"가 이를 위해 존재합니다 — "반복 6이 반복 9보다 공간 리듬이 더 좋았다"는 유효한 피드백입니다.
- **피벗은 근거가 있어야 합니다.** Evaluator의 명시적 `REDIRECT` 또는 Generator의 `design_memo.md` 중 하나가 필요합니다. 컨텍스트 리셋으로 인한 기억상실은 인사이트가 아닙니다.

## 파일 구조

```
skill-wizard-harness/
├── SKILL.md                                 # 메인 위자드 (479줄)
├── README.md                                # 영문 README
├── README.ko.md                             # 이 파일
└── references/
    ├── planner-prompt-template.md           # Planner 프롬프트 뼈대 + 도메인별 적용
    ├── generator-prompt-template.md         # Generator 프롬프트 뼈대 + 도메인별 적용
    ├── evaluator-prompt-template.md         # Evaluator 프롬프트 뼈대 + 튜닝 워크플로우
    └── rubric.md                            # 루브릭 설계 가이드 + 도메인별 템플릿
```

## 라이선스

MIT
