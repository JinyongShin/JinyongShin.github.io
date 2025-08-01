---
title:  "[Paper Review] A Survey of AI Agent Protocols"

categories:
  - AI
  - AI 에이전트
tags:
  - 인공지능
  - AI
  - Agent
  - 에이전트
  - MCP
  - A2A
  - 에이전트프로토콜
  - AgentProtocols
author: JinyongShin
toc: true
toc_sticky: true
 
date: 2025-05-08 12:00:00 +0900
---

# AI 에이전트 프로토콜의 모든 것

오늘, AI 에이전트 프로토콜에 대한 서베이 논문([A Survey of AI Agent Protocols](https://arxiv.org/pdf/2504.16736))을 읽고 정리하는 시간을 가졌다. LLM(Large Language Model) 에이전트들이 다양한 산업 분야에 빠르게 도입되면서, 이들 에이전트가 외부 도구나 데이터 소스와 소통하는 방식에 표준이 없다는 큰 문제가 있다는 것을 다시 한번 깨달았다. 이 표준화의 부재가 에이전트들의 협업과 확장을 어렵게 하고, 복잡한 실제 문제 해결 능력을 제한한다는 점이 가장 큰 문제점으로 지적되었다. 마치 초창기 인터넷이 호환되지 않는 시스템들로 파편화되어 있던 시기와 비슷하다는 비유가 인상 깊었다. TCP/IP나 HTTP 같은 통일된 프로토콜이 인터넷 혁명을 가져왔듯이, LLM 에이전트를 위한 통합된 프로토콜이 **연결된 지능 네트워크(connected network of intelligence)** 를 탄생시킬 것이라는 비전이 정말 흥미로웠다.

## 에이전트 프로토콜의 필요성

![에이전트 프로토콜 개발 개요](https://miro.medium.com/v2/resize:fit:720/format:webp/1*Udw7Pv25sbAJDLZkk2iIJQ.png)
>에이전트 프로토콜 개발 개요

먼저, **에이전트**에 대한 정의부터 명확히 이해했다. 에이전트는 대규모 언어 모델의 정교한 언어 처리 능력에 **자율적인 의사결정 프레임워크**가 통합된 AI 시스템이다. 단순히 텍스트를 생성하는 LLM과 달리, 에이전트는 실제 환경에서 자율적으로 기능하도록 설계되었다. 핵심 아키텍처는 **기초 모델(Foundation Model)**, **메모리 시스템(Memory Systems)**(단기 및 장기), **계획(Planning)**, **도구 사용(Tool-Using)**, 그리고 **액션 실행(Action Execution)** 으로 구성된다.

이러한 에이전트들이 외부 자원이나 다른 에이전트들과 효율적으로 소통하려면 **에이전트 프로토콜**이 필수적이다. 전통적인 API나 GUI 방식과 비교했을 때, 프로토콜은 **높은 효율성, 광범위한 운영 범위, 강력한 표준화, 그리고 AI 시스템과의 기본 호환성**이라는 독보적인 장점을 가진다. 특히, 이 프로토콜이 **상호 운용성(interoperability)** 을 보장하고, **표준화된 상호작용**을 가능하게 하며, **보안 및 거버넌스 메커니즘**을 제공하여 궁극적으로 **집단 지능(collective intelligence)** 의 출현을 가능하게 한다는 점이 가장 핵심적인 가치라는 것을 알게 되었다.

## 프로토콜 분류

논문에서는 에이전트 프로토콜을 체계적으로 분류하는 **두 가지 차원의 프레임워크**를 제시했다.

*   **객체 지향(Object Orientation)**:
    *   **맥락 지향 프로토콜(Context-Oriented Protocols)**: 에이전트와 외부 도구/데이터 소스 간의 통신에 중점을 둔다.
    *   **에이전트 간 프로토콜(Inter-Agent Protocols)**: 여러 에이전트 간의 협업 및 정보 교환에 중점을 둔다.
*   **적용 시나리오(Application Scenario)**:
    *   **범용 프로토콜(General-Purpose Protocols)**: 광범위한 에이전트와 맥락 제공자를 통합된 인터페이스를 통해 지원하는 것을 목표로 한다.
    *   **도메인별 프로토콜(Domain-Specific Protocols)**: 특정 사용 사례에 대한 특화된 최적화에 중점을 둔다.

![프로토콜 분류](https://miro.medium.com/v2/resize:fit:720/format:webp/1*FdC2sndAefUPMJMrEfElYA.png)
> 프로토콜 분류

### 맥락 지향 프로토콜

맥락 지향 프로토콜은 에이전트가 외부 도구를 자율적으로 호출하여 필요한 맥락을 얻을 수 있도록 돕는다. 특히 **Model Context Protocol (MCP)** 이 이 분야의 선구적인 범용 프로토콜로 소개되었다.

*   **MCP의 특징**:
    *   **Host(LLM 에이전트)**, **Client(자원 설명 및 요청 시작)**, **Server(자원에서 맥락 제공)**, **Resource(데이터, 도구, 서비스)** 의 네 가지 구성 요소로 이루어져 있다.
    *   **표준화**를 통해 다양한 LLM 제공자와 도구 제공자 간의 파편화를 해결하고 **상호 운용성 및 확장성**을 크게 향상시킨다.
    *   가장 중요한 점은 **도구 호출과 LLM 응답을 분리**하여 데이터 유출 위험을 줄이고 **개인 정보 보호 및 보안**을 강화한다는 것이다. 민감한 사용자 정보가 클라이언트를 통해 로컬에서 처리되어 LLM으로 직접 노출되지 않도록 하는 방식이 정말 똑똑하다고 생각했다.

도메인별 맥락 지향 프로토콜로는 웹사이트 정보를 에이전트에 제공하는 **agents.json**이 있었다. OpenAPI 표준을 기반으로 하여 AI 호환 인터페이스를 선언하는 구조다.

### 에이전트 간 프로토콜

단일 에이전트의 한계를 극복하고 복잡한 작업을 해결하기 위해 **다중 에이전트 협업**이 중요해지면서 **에이전트 간 프로토콜**의 필요성이 대두되었다.

*   **범용 에이전트 간 프로토콜**:
    *   **Agent Network Protocol (ANP)**: 오픈 소스 커뮤니티에서 개발한 프로토콜로, **다양한 에이전트 간 상호 운용성**을 목표로 한다. **에이전트 인터넷(Internet of Agents)** 이라는 비전을 제시하며, **탈중앙화 식별자(DID)** 기반의 신원 및 암호화 통신, 자연어 기반의 메타-프로토콜 계층, 그리고 에이전트 발견 및 정보 기술을 위한 애플리케이션 프로토콜 계층으로 구성된다.
    *   **Agent2Agent (A2A) Protocol**: 구글에서 제안한 에이전트 협업 프로토콜로, **엔터프라이즈 환경 내의 원활한 에이전트 협업**에 중점을 둔다. HTTP(S), JSON-RPC 2.0 등 **기존 표준을 재사용하여 단순성**을 추구하며, 인증, 권한 부여, 보안, 추적 가능성 등을 내장하여 **기업 준비성(Enterprise Readiness)** 을 강조한다. 특히, 에이전트 상호작용 시 **사고 과정이나 도구를 공유할 필요 없이** 컨텍스트, 상태, 지시, 데이터에 초점을 맞춰 **구현 프라이버시와 지적 재산권을 보존**하는 '불투명한 실행(Opaque Execution)' 개념이 독특했다.
    *   **Agora**: LLM 에이전트 통신에서 겪는 **'에이전트 통신 트릴레마(Agent Communication Trilemma)'(다용성, 효율성, 이식성)** 를 해결하기 위해 제안되었다. **자연어 이해, 코드 생성, 자율 협상** 능력을 활용하여 에이전트가 맥락에 따라 다양한 통신 프로토콜을 채택할 수 있도록 한다. **프로토콜 문서(Protocol Documents, PDs)** 라는 개념을 도입하여 에이전트가 인간의 개입 없이 프로토콜을 자율적으로 협상, 구현, 적용, 심지어 생성할 수 있도록 한다는 점이 매우 혁신적이라고 생각했다.

*   **도메인별 에이전트 간 프로토콜**:
    *   **인간-에이전트 상호작용 프로토콜**: **PXP(Predict and eXplain Protocol)** 는 인간 전문가와 LLM 에이전트 간의 양방향 이해 가능한 상호작용을 촉진하며, **LOKA(Layered Orchestration for Knowledgeful Agents) Protocol**은 AI 에이전트 생태계에서 신원, 책임, 윤리적 정렬 문제를 해결하기 위한 탈중앙화 프레임워크를 제시한다.
    *   **로봇-에이전트 상호작용 프로토콜**: **CrowdES**는 로봇 에이전트 상호작용에 특화된 현실적인 군중 행동 생성 프로토콜이며, **Spatial Population Protocols (SPPs)** 는 익명 로봇 간의 분산 위치 파악 문제를 해결하는 프레임워크를 제공한다.
    *   **시스템-에이전트 상호작용 프로토콜**: **LMOS(Language Model Operating System)** 는 에이전트 인터넷(Internet of Agents) 구축을 위한 기본 아키텍처를 제공하고, **Agent Protocol**은 제어 콘솔과 AI 에이전트 간의 원활한 상호작용을 위한 프레임워크 독립적인 통신 표준을 정의한다.

흥미롭게도, 맥락 지향 프로토콜과 에이전트 간 프로토콜은 **장기적으로 점차 수렴하고 동질화될 가능성**이 있다고 한다. 상호작용하는 도구가 '자율성이 낮은 에이전트'로, 에이전트가 '자율성이 높은 도구'로 인식될 수 있기 때문이다.

## 프로토콜 평가 방법

에이전트 프로토콜의 성능이나 기능 비교는 빠르게 변하기 때문에 정적 비교가 어렵다. 따라서 논문은 평가를 위한 **핵심적인 차원과 과제**에 초점을 맞추었다. 인터넷 프로토콜에서 영감을 받아 7가지 핵심 지표가 제시되었다:

*   **효율성(Efficiency)**: 처리량 관리, 지연 시간 최소화, 리소스 사용 최적화 등을 포함한다. 특히 **LLM 기반 작업에 특유한 토큰 소비량**이 중요한 지표였다.
*   **확장성(Scalability)**: 노드(에이전트 또는 도구) 또는 연결(링크) 수가 기하급수적으로 증가함에 따라 성능과 가용성을 유지하는 능력이다. **노드 확장성, 링크 확장성, 역량 협상(Capability Negotiation)** 이 평가된다.
*   **보안(Security)**: 신원 인증, 암호화, 무결성 검증을 통한 에이전트 간 및 에이전트-도구 간 상호작용 보호를 의미한다. **인증 모드 다양성, 역할/ACL 세분화, 맥락 비감도화 메커니즘**이 중요하다.
*   **신뢰성(Reliability)**: 다중 에이전트 시스템에서 에이전트 간의 안정적이고 정확한 통신을 보장하는 능력이다. **패킷 재전송, 흐름 및 혼잡 제어, 지속적인 연결** 등이 핵심 메커니즘이다.
*   **확장성(Extensibility)**: 기존 시스템이나 호환성을 방해하지 않으면서 새로운 기능을 추가하거나 기존 기능을 수정하여 새로운 요구 사항 및 기술 발전에 유연하게 적응하는 능력이다. **하위 호환성, 유연성 및 적응성, 사용자 정의 및 확장**이 평가된다.
*   **운용성(Operability)**: 구현, 운영 및 유지 관리의 용이성과 효율성을 의미한다. **프로토콜 스택 코드 볼륨, 배포 및 구성 복잡성, 관찰 가능성** 등이 지표가 된다.
*   **상호 운용성(Interoperability)**: 서로 다른 시스템, 프레임워크, 브라우저 및 기타 환경 간의 원활한 통신을 가능하게 하는 능력이다. **크로스-시스템 및 크로스-브라우저 호환성, 크로스-네트워크 및 크로스-플랫폼 적응성**이 핵심이다.

MCP의 버전 업그레이드나 MCP에서 ANP, A2A로의 발전 사례를 통해 프로토콜이 기능, 성능, 보안 측면에서 어떻게 진화하는지 확인할 수 있었다.

## 실제 적용 사례

다양한 프로토콜이 실제 문제 해결에 어떻게 적용되는지 '5일간 베이징에서 뉴욕으로 여행 계획'이라는 시나리오를 통해 이해할 수 있었다.

![프로토콜 별 사용 예시](https://miro.medium.com/v2/resize:fit:720/format:webp/1*g1vS7BJujvkTiP2PSL9zWg.png)
> 프로토콜 별 사용 예시

*   **MCP**: 단일 에이전트(MCP Travel Client)가 모든 외부 서비스(Flight Server, Hotel Server, Weather Server)를 직접 호출하는 **중앙 집중식 접근 방식**이다. 단순하지만 유연성이 낮고 병목 현상이 발생할 수 있다.
*   **A2A**: 여러 전문 에이전트(Transportation, Accommodation & Activities 부서)에 지능을 분산시켜 **직접 통신하며 협업**한다. 불필요한 통신 오버헤드를 줄이고 더 복잡한 협업 패턴을 가능하게 한다.
*   **ANP**: 조직 경계가 다른 독립적인 에이전트(Airline Company, Hotel, Weather Website) 간의 **표준화된 교차 도메인 상호작용**을 가능하게 한다.
*   **Agora**: 사용자의 자연어 요청을 직접 표준화된 프로토콜로 변환하는 **사용자 중심적인 접근 방식**이다. 자연어 처리의 복잡성으로부터 전문 에이전트들을 보호하며, 이해, 생성, 분배의 세 단계를 거친다.

각 프로토콜이 **에이전트 자율성 수준, 통신 유연성, 인터페이스 표준화, 작업 복잡성** 등 특정 조건과 의존성에 따라 적합하다는 점을 명확히 알 수 있었다.

## AI 에이전트 프로토콜의 미래

논문은 에이전트 프로토콜의 **단기, 중기, 장기적인 발전 방향**도 제시했다.

*   **단기 전망**:
    *   **평가 및 벤치마킹**: 통신 효율성, 환경 변화에 대한 강건성, 적응성, 확장성 등을 포괄하는 통합된 평가 프레임워크 개발.
    *   **개인 정보 보호 프로토콜**: 에이전트가 내부 상태나 개인 데이터를 최소한으로 노출하면서 정보를 교환할 수 있도록 하는 프로토콜 개발.
    *   **에이전트 메시 프로토콜**: 인간의 그룹 채팅에서 영감을 받아 에이전트 그룹 내에서 통신 기록의 완전한 투명성과 공유 액세스를 가능하게 하는 모델.
    *   **진화 가능 프로토콜(Evolvable Protocols)**: 프로토콜을 정적인 규칙이 아닌, 에이전트의 적응 능력에 필수적인 동적, 모듈형, 학습 가능한 구성 요소로 간주하는 패러다임.
*   **중기 전망**:
    *   **내장형 프로토콜 지식**: LLM의 매개변수에 프로토콜 내용과 구조를 통합하여 명시적인 프롬프트 없이 프로토콜 준수 행동을 수행할 수 있도록 하는 연구.
    *   **계층형 프로토콜 아키텍처**: 통신 계층을 저수준 전송 및 동기화 메커니즘과 고수준 의미론 및 작업 관련 상호작용으로 분리하여 모듈성과 확장성을 향상시키는 방향.
*   **장기 전망**:
    *   **집단 지능 및 스케일링 법칙**: 대규모 상호 연결된 에이전트 집단에서 집단 지능의 출현을 탐구하고, 에이전트의 규모, 통신 토폴로지, 프로토콜 구성이 시스템 수준 행동에 미치는 영향 연구.
    *   **에이전트 데이터 네트워크(Agent Data Networks, ADN)**: 자율 에이전트 통신 및 조정을 위해 최적화된 기반 데이터 인프라의 출현. 인간 중심의 웹 상호작용과 달리, 에이전트의 운영 요구 사항을 직접 지원하는 기계 중심의 데이터 표현에 중점을 둔다.

## 마치며

오늘 공부한 내용을 통해 AI 에이전트 프로토콜이 현재 AI 분야에서 얼마나 중요한 역할을 하는지, 그리고 앞으로 어떤 방향으로 발전할지 폭넓게 이해할 수 있었다. 파편화된 에이전트 생태계에 표준이라는 다리를 놓아 상호 운용성을 확보하고, 나아가 **분산되고 협력적인 지능의 새로운 시대**를 열어갈 잠재력을 가진다는 점이 매우 인상 깊었다. 마치 인터넷의 초기 프로토콜들이 인류 사회를 변화시켰듯이, 미래의 에이전트 통신 표준도 지능이 시스템 간에 공유되고 조정되며 증폭되는 방식을 근본적으로 재편할 것이라는 비전이 기대된다. MCP, A2A등은 공개된지 얼마 안된 기술이며 향후 어떤 기술이 발표되고 시장을 선도할지는 알 수 없다. 앞으로도 이러한 프로토콜들의 발전을 꾸준히 지켜보며 내 지식을 확장해 나가야겠다.