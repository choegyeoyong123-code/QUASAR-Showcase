# QUASAR-Showcase
"QUASAR is a Multi-Agent AI Orchestration system replacing SME workforces. Built with FastAPI, it utilizes specialized departmental agents, CoT/ToT reasoning, and strict tax compliance rules, all governed by a secure Human-on-the-Loop approval architecture."

graph TD
    %% 1. Entry Point
    User([User Request]) --> API[FastAPI: /orchestrate]
    
    subgraph "Orchestration Layer"
        API --> CCM[Central Control Manager]
        CCM -- "Dynamic Routing" --> Reasoning{CoT & ToT Reasoning}
    end

    %% 2. Agent Layer
    subgraph "Specialized Agent Intelligence (Parallel)"
        Reasoning --> P[Planning Agent]
        Reasoning --> HR[HR Agent]
        Reasoning --> FT[Finance/Tax Agent <br/><b>Primary Axis</b>]
        Reasoning --> S[Sales Agent]
        Reasoning --> M[Marketing Agent]
        Reasoning --> PR[Production Agent]
        Reasoning --> RD[R&D Agent]
        Reasoning --> PC[Purchasing Agent]
    end

    %% 3. Synthesis & Constraint
    P & HR & FT & S & M & PR & RD & PC --> LogicCheck{K-Tax & Risk <br/>Compliance Check}
    
    LogicCheck -- "Verified Proposal" --> HOTL_Wait[State: awaiting_approval]

    %% 4. Security Layer
    subgraph "Human-on-the-Loop (HOTL) Protocol"
        HOTL_Wait --> Human[/Human Administrator/]
        Human -- "JWT auth_token Approval" --> Exec[Final Execution]
    end

    Exec --> Result([Execution Result / Report])

    %% Styling
    style FT fill:#f96,stroke:#333,stroke-width:4px
    style CCM fill:#d4a373,stroke:#333
    style HOTL_Wait fill:#e9edc9,stroke:#333
    style LogicCheck fill:#faedcd,stroke:#333

    중앙 제어 매니저 (CCM) 및 추론 기법:

단순한 명령어 실행이 아닌, CoT(Chain-of-Thought)와 ToT(Tree of Thoughts) 기법을 통해 각 부서 에이전트의 의견을 논리적으로 합성하고 충돌을 해결합니다.

재무/세무 중심축 (Finance/Tax Agent):

모든 비즈니스 제안은 반드시 한국의 세무 규정 및 예산 리스크 관리 기준을 통과해야 합니다. 다이어그램에서도 이 에이전트가 시스템의 핵심축(Primary Axis)임을 시각적으로 강조했습니다.

HOTL 보안 아키텍처:

AI의 독단적인 실행을 방지하기 위해 awaiting_approval 상태를 거치도록 설계되었습니다.

인간 관리자의 최종 승인(JWT 기반)이 있어야만 태스크가 완료되는 구조를 통해 시스템의 신뢰성과 안전성을 보장합니다.

8개 전문 부서 에이전트:

기획부터 구매까지, 중소기업 경영에 필요한 모든 실무 부서를 에이전트화하여 인력난에 처한 기업에 즉각적인 디지털 노동력을 제공합니다.

