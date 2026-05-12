# QUASAR — AI Agent SaaS for SME Workforce Automation
### 중소기업 인력난 해결을 위한 AI 에이전트 SaaS

[![Python 3.12+](https://img.shields.io/badge/python-3.12%2B-blue)](https://www.python.org/)
[![Tests](https://img.shields.io/badge/tests-51%20passed%20%E2%9C%94-brightgreen)](#reliability-proof)
[![LangGraph](https://img.shields.io/badge/LangGraph-StateGraph-orange)](https://github.com/langchain-ai/langgraph)
[![License](https://img.shields.io/badge/license-Proprietary-red)](#)

---

## The Problem — 대한민국 중소기업의 구조적 인력난

> **"중소기업 10곳 중 6곳이 채용 인력을 못 구한다."**  
> — 중소벤처기업부, 2024 중소기업 인력실태조사

대한민국 중소기업(300인 미만)은 전체 고용의 **81%** 를 담당하지만, 만성적 인력 부족에 시달립니다.

| 부서 | 현실 | QUASAR의 해법 |
|------|------|--------------|
| 법무 | 외부 법무법인 의뢰 비용 **건당 300만~500만원** | LegalAgent: 계약서 조항별 위반 탐지 + 위약금 자동 정량화 |
| 회계 | 결산 시즌 회계사 구인난, 야근 연속 | CPAAgent + CTAAgent: 가결산 → 세무조정 → 이연법인세 자동화 |
| 재무 | 공시·법령 모니터링 인력 부재 | FinancialDataPipeline: DART 공시 + 법제처 법령 실시간 수집 |
| 감사 | 내부 감사 시스템 전무 | AuditGraph: Zero-Tolerance ₩0.01 불균형 탐지 |

QUASAR는 이 8개 부서 업무를 **LangGraph 기반 멀티에이전트**로 자동화합니다.

---

## Feasibility — 현재 구현 상태 / Implementation Status

> Phase 1~5 완료 · 68개 파일 · **51개 테스트 100% 통과**

| Phase | 영역 | 상태 |
|-------|------|------|
| Phase 1 | 오케스트레이션 코어 (JWT, HOTL, CentralControlManager) | ✅ Complete |
| Phase 2 | 법무 에이전트 (LegalAgent, CoT, ToT, CitationValidator) | ✅ Complete |
| Phase 3 | 회계 멀티에이전트 (GL / Tax / Treasury / AR-AP) | ✅ Complete |
| Phase 4 | 금융 엔진 (DART 파이프라인, PII 익명화, LLMRouter) | ✅ Complete |
| Phase 5 | CPA-CTA 협업 시스템 (Debate Loop, ToT 중재, Phoenix OTEL) | ✅ Complete |
| Phase 6 | 법령 자동 갱신 · PostgreSQL 마이그레이션 | 🔄 Roadmap |
| Phase 7 | LoRA Fine-tuning 자동화 (axolotl) | 🔄 Roadmap |

```
Total:  68 source files   ·   51 automated tests   ·   0 failures
Stack:  FastAPI · LangGraph · Pydantic v2 · SQLAlchemy async · Arize Phoenix
```

---

## Architecture Overview — 시스템 아키텍처

```
┌─────────────────────────────────────────────────────────────────┐
│                        QUASAR v3.0.0                            │
│              Multi-Agent Orchestration Platform                  │
├──────────────┬──────────────┬─────────────────┬────────────────┤
│  Legal Layer │  Accounting  │  Financial      │ Observability  │
│              │  Layer       │  Engine         │                │
│ LegalAgent   │ CPAAgent     │ DARTClient      │ Arize Phoenix  │
│ ├ CoT/4-step │ CTAAgent     │ DataCleaner     │ OTEL Spans     │
│ ├ ToT/3-path │ GLAgent      │ PIIAnonymizer   │ agent_span()   │
│ ├ RAG Search │ TaxAgent     │ LLMRouter       │ CPA=Blue(#2196F3)
│ └ Citation   │ TreasuryAgent│ FineTuneLake    │ CTA=Red(#f44336)
│   Validator  │ ARAPAgent    │                 │ ToT=Orange     │
├──────────────┴──────────────┴─────────────────┴────────────────┤
│                    LangGraph StateGraph                          │
│  DebateOrchestrator  ·  AuditGraph  ·  Conditional Routing      │
├─────────────────────────────────────────────────────────────────┤
│           FastAPI  ·  JWT Auth  ·  HOTL Approval Loop           │
│           SQLAlchemy Async  ·  SQLite → PostgreSQL (Phase 6)    │
└─────────────────────────────────────────────────────────────────┘
```

**→ Full architecture diagrams:** [`architecture/`](./architecture/)

---

## Technical Moat — 기술적 해자

### 1. Zero-Tolerance Audit Engine
₩0.01 불균형도 `AUDIT_HOLD_CRITICAL`로 즉시 에스컬레이션. 타협 없음.
```python
_IMBALANCE_TOLERANCE = 0.01   # ₩0.01 — 절대 기준선
# 시산표 불균형 ≥ ₩0.01 → AUDIT_HOLD_CRITICAL 발동
```

### 2. Korean Tax Law Automation — 손금불산입 자동 처리
법인세법 §25(접대비) · §34(대손충당금) · §23(감가상각비) 세무조정을 코드로 구현.
인간 세무사와 동일한 판단 로직이 테스트로 검증된 함수로 존재합니다.

### 3. CPA × CTA Mutual Verification Loop
회계사(CPA)와 세무사(CTA)가 서로의 판단을 교차 검증. 이견 시 Tree of Thoughts 엔진이 K-IFRS 1012 조문을 RAG로 검색해 중재합니다.

### 4. DeferredTaxLedger — 유보 추적 원장
`자본금과 적립금 조정명세서 을`(국세청 별지 3호 서식) 전산화.  
유보 발생 → 부분 추인 → 전액 추인의 생애주기를 코드로 추적합니다.

### 5. RAG + Citation Validator — 환각 방지
LLM이 인용한 법령 조문을 실제 원문과 대조하는 `CitationValidator`.  
환각 탐지 시 overall_risk를 자동으로 `high`로 상향합니다.

---

## Key Workflows — 핵심 워크플로우

### A. Legal Contract Review (법무 계약서 검토)
```
Contract Text
    → Clause Split (조항 분리, 제N조 / 번호 목록 / 문단 분할)
    → CoT × 4 Steps per Clause (병렬 처리)
        Step 1: 법적 도메인 키워드 추출
        Step 2: LawRepository RAG 검색 (BM25 + Sector Boost ×1.5)
        Step 3: Claude API tool_use 위반 판단
        Step 4: 위약금 정량화 (손금 인정 여부 + VAT + 원천징수)
    → ToT Overall Risk Resolution (3-Path)
    → CitationValidator (환각 탐지)
    → FinanceTaxExport → TaxAgent 자동 연동
```

### B. CPA-CTA Tax Audit Debate (세무 감사 토론)
```
Journal Entries (분개장)
    → CPAAgent: Draft FS 작성 (가결산)          ← 파란색 노드
    → CTAAgent: 세무조정 계산서 (§25/§34/§23)   ← 빨간색 노드
    → CPAAgent: 검증 + 충돌 감지
    → [충돌 시] ToT Arbitration Engine            ← 주황색 노드
        Path A: CPA 수용 / Path B: CTA 수용 / Path C: 절충
        → K-IFRS 1012.15 RAG 검색 → Path 채택
    → FinalAuditReport (합의 결론 + 법령 인용)
```

**→ Full diagram:** [`architecture/cpa_cta_debate.md`](./architecture/cpa_cta_debate.md)

---

## Observability — AI 판단 투명성

모든 에이전트 추론 과정은 **Arize Phoenix** OpenTelemetry 스팬으로 기록됩니다.

```python
with agent_span("CPA", "validate_tax_adjustment", input_data={...}) as sp:
    # ... validation logic ...
    sp.set_output({"agreed": agreed, "conflict_count": len(conflicts)})
    sp.set_attribute("had_conflict", str(not agreed))
```

Phoenix 대시보드에서 확인 가능한 항목:
- 각 노드의 입력/출력 데이터
- CoT(Chain of Thought) 단계별 추론 로그
- 에이전트 색상 구분 트리 시각화 (CPA=파랑, CTA=빨강, ToT=주황)
- 충돌 발생 위치 및 중재 결과

**→ Trace structure:** [`docs/observability.md`](./docs/observability.md)

---

## Domain Expertise — 법률·회계 도메인 전문성

| 도메인 | 구현 내용 | 법적 근거 |
|--------|-----------|----------|
| 접대비 손금불산입 | 기본 ₩12M + 수입금액×0.3% 자동 계산 | 법인세법 §25 |
| 대손충당금 한도 | 수입금액×1% 세무한도 자동 계산 | 법인세법 §34 |
| 감가상각비 상각범위 | 세무 내용연수 기준 초과분 유보 처리 | 법인세법 §23 |
| 이연법인세 판단 | 영구적/일시적 차이 자동 분류 | K-IFRS 1012.15 |
| 위약금 세무 처리 | 손금인정/VAT/원천징수 자동 판단 | 법인세법 §19 |
| 하도급 지연이자 | 법정 이자율 연 15.5% 자동 적용 | 하도급법 |

**→ Full domain spec:** [`docs/domain_expertise.md`](./docs/domain_expertise.md)

---

## Repository Structure

```
QUASAR-Showcase/
├── README.md                        ← 이 파일
├── architecture/
│   ├── system_overview.md           ← 전체 시스템 Mermaid 다이어그램
│   ├── cpa_cta_debate.md            ← CPA-CTA Debate 흐름도
│   ├── audit_graph.md               ← Zero-Tolerance 감사 그래프
│   └── tot_engine.md                ← Tree of Thoughts 엔진 설명
├── docs/
│   ├── domain_expertise.md          ← 한국 세법·회계기준 구현 상세
│   ├── observability.md             ← Arize Phoenix 트레이싱 구조
│   └── feasibility_metrics.md       ← 테스트 결과 및 품질 지표
├── showcase/
│   ├── core_logic/
│   │   ├── debate_orchestrator.py   ← CPA-CTA Debate 오케스트레이터 (sanitized)
│   │   ├── cta_agent.py             ← 세무조정 자동화 코어 (sanitized)
│   │   ├── cpa_agent.py             ← 가결산 · 검증 에이전트 (sanitized)
│   │   ├── deferred_tax_ledger.py   ← 유보 추적 원장 (sanitized)
│   │   └── tot_arbitration.py       ← Tree of Thoughts 중재 엔진 (sanitized)
│   └── schemas/
│       └── cpa_cta_schemas.py       ← 공유 Pydantic 스키마 (sanitized)
└── tests/
    ├── PYTEST_RESULTS.md            ← pytest 실행 결과 로그
    └── test_samples.py              ← 핵심 테스트 샘플 (sanitized)
```

---

## Quick Start

```bash
# 1. 환경 설정
python -m venv .venv && source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt

# 2. 환경변수 설정
cp .env.example .env
# ANTHROPIC_API_KEY, DART_API_KEY, LAW_API_EMAIL 입력

# 3. 서버 실행
uvicorn main:app --reload

# 4. 접대비 한도 초과 시나리오 데모 (API 키 없이 실행 가능)
python demo_audit_trace.py

# 5. 전체 테스트
pytest --tb=short -q
```

---

## Contact

**Team QUASAR** · `choegyeoyong123@gmail.com`  
*"모두의 창업" 심사위원 여러분, 기술적 질문은 언제든 환영합니다.*
