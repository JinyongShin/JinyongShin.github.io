---
title:  "Google ADK를 활용한 확장 가능한 Multi-Agent System 구축 튜토리얼 공부"

categories:
  - AI
  - AI 에이전트
tags:
  - googleADK
  - AI
  - Agent
  - 에이전트
  - Multi-Agent-System
  - MAS

author: JinyongShin
toc: true
toc_sticky: true
 
date: 2025-07-30 18:00:00 +0900
---

## Google ADK와 확장 가능한 멀티 에이전트 시스템

오늘 [Google ADK(Agent Development Kit)를 활용한 멀티 에이전트 시스템 구축 튜토리얼](https://www.marktechpost.com/2025/07/30/a-coding-guide-to-build-a-scalable-multi-agent-system-with-google-adk/) 포스트를 보고 코드를 한줄 한줄 보며 공부했다. 제공된 글과 코드를 꼼꼼히 뜯어보며, 멀티 에이전트 시스템을 어떻게 구축하고 확장할 수 있는지에 대한 생각을 정리해 보았다.

튜토리얼에서 제공한 전체 코드는 [github](https://github.com/Marktechpost/AI-Tutorial-Codes-Included/blob/main/advanced_google_adk_multi_agent_tutorial_Marktechpost.ipynb)에서 확인 가능하다.

### 1. Google ADK 활용 방법

Google ADK는 복잡한 작업을 효율적으로 처리하기 위한 **에이전트 시스템**을 구축하는 데 최적화된 프레임워크다. 이 튜토리얼에서는 다음과 같은 방법으로 ADK의 기능을 활용했다.

* **전문 역할 에이전트 생성**: `Agent` 클래스를 사용해 `researcher`, `calculator`, `analyst`, `writer`와 같은 특정 역할을 수행하는 에이전트들을 만들었다. 각 에이전트는 고유의 `instruction`(지시사항)을 부여받아 특정 분야의 전문가처럼 행동한다.
* **도구 통합(Tool Integration)**: `researcher` 에이전트에는 `Google Search`라는 외부 도구를 통합하여 실시간 웹 검색이 가능하도록 했다. 이는 에이전트가 자체적인 지식뿐만 아니라 외부 정보를 활용해 최신 데이터를 다룰 수 있게 해준다.
* **비동기 실행(Asynchronous Execution)**: `async`와 `await` 키워드를 활용해 에이전트들의 작업을 **비동기적으로** 처리했다. 이를 통해 여러 에이전트가 동시에 또는 순차적으로 작업을 수행하면서도 시스템 전체의 효율성을 높일 수 있다.
* **모듈식 아키텍처**: 전체 시스템은 `AdvancedADKTutorial`이라는 단일 클래스 내에 `create_specialized_agents()`, `demonstrate_single_agent_research()` 등 기능별 메서드로 분리되어 있었다. 이처럼 **모듈화**된 구조는 코드의 가독성을 높이고 유지보수를 쉽게 만든다.

---

### 2. Scalable 멀티 에이전트 시스템 분석

튜토리얼을 통해 구축된 시스템이 '확장 가능하다(scalable)'고 평가하는 핵심적인 이유를 분석해 보았다.

* **모듈식 설계**: 각 에이전트는 독립적인 모듈로 존재한다. 새로운 역할이 필요하면 기존 코드를 수정하지 않고 새로운 `Agent` 인스턴스를 추가하기만 하면 된다. 예를 들어, `translator` 에이전트가 필요하다면 `self.agents['translator'] = Agent(...)`를 추가하면 된다. 이렇게 모듈이 서로 의존하지 않아 시스템의 크기를 쉽게 늘릴 수 있다.
* **비동기 처리**: `asyncio`를 활용한 비동기 실행은 여러 작업을 병렬로 처리할 수 있는 기반이 된다. 이는 에이전트의 수가 늘어나더라도 전체 시스템의 처리 속도가 급격히 느려지는 것을 방지한다. 예를 들어, 100개의 에이전트가 동시에 웹 검색을 수행해야 할 때, 비동기 처리는 각각의 검색 요청이 블로킹(blocking)되지 않고 효율적으로 관리되도록 해준다.
* **전문성 분리**: 각 에이전트에게 특정 역할과 도구를 할당함으로써, 하나의 거대 에이전트가 모든 작업을 처리하는 방식보다 훨씬 효율적이다. 각 에이전트는 자신의 전문 분야에 집중하므로 더 정확하고 신뢰성 높은 결과를 낼 수 있다. 이는 마치 한 팀의 각 전문가가 자신의 역할을 수행하는 것과 같다. 이 구조는 새로운 전문가(에이전트)를 팀에 합류시키는 것처럼 시스템을 쉽게 확장할 수 있게 한다.
* **플랫폼 호환성**: 글의 결론 부분에서 언급된 것처럼, 이 시스템은 Google Cloud Run이나 Vertex AI Agent Engine과 같은 클라우드 환경에 배포하기 용이하다. 클라우드 환경은 에이전트 시스템의 규모를 필요에 따라 유연하게 조절할 수 있게 해주므로, 트래픽이 많을 때는 자원을 늘리고 적을 때는 줄이는 방식으로 비용 효율적인 **확장성**을 확보할 수 있다.

에이전트마다 역할이 나뉘어 있다는 점, 그리고 비동기 처리가 가능하다는 점이 확장성의 핵심이라는 것을 알 수 있었다. 또한, 새로운 에이전트가 필요한 경우에 모듈식 설계를 통해 간편하게 해결한 점은 지금까지 왜 그렇게 하지 못했을까 하는 생각이 들 만큼 간단하면서도 내가 놓치고 있던 부분이었다. 하지만 코드를 자세히 들여다보니, 새로운 에이전트를 추가할 때마다 **`demonstrate_...` 함수를 매번 새로 작성해야 하는 비효율적인 부분**이 눈에 띄었다.

#### 원본 코드의 아쉬운 점

아래 원본 코드를 보면, 각 에이전트의 시연 함수들이 거의 비슷한 구조를 반복하고 있다. 에이전트 이름, 태스크 내용, 입력 데이터만 다를 뿐, 에이전트 실행 및 결과 저장 로직은 거의 동일하다.

```python
async def demonstrate_single_agent_research(self):
    print("\n=== Single Agent Research Demo ===")
    query = "What are the latest developments in quantum computing breakthroughs in 2024?"
    # ... (중략)
    try:
        response_text = await self.run_agent_with_input(
            agent=self.agents['researcher'],
            user_input=query
        )
        task_result = TaskResult(
            agent_name="researcher",
            task="Quantum Computing Research",
            result=summary
        )
        self.results.append(task_result)
        # ... (중략)
```

#### 제안하는 개선 방법

만약 새로운 에이전트가 생긴다면, 이런 반복적인 코드를 다시 작성해야 할 것이다. 그래서 이 부분을 **추상 클래스(Abstract Class)** 와 **상속(Inheritance)** 을 사용해 개선하면 좋겠다는 생각이 들었다. 공통된 로직은 하나의 추상 클래스에 정의하고, 각 에이전트 시연 클래스는 그 클래스를 상속받아 고유한 정보만 제공하는 방식이다.

```python
# 개선 코드: 추상 클래스를 활용한 에이전트 시연 로직
from abc import ABC, abstractmethod

class BaseAgentDemonstrator(ABC):
    """
    모든 에이전트 시연 클래스의 기본 기능을 정의하는 추상 클래스
    """
    def __init__(self, tutorial_instance):
        self.tutorial = tutorial_instance
        self.agent_name = self.get_agent_name()
        self.task_name = self.get_task_name()
        self.input_data = self.get_input_data()

    @abstractmethod
    def get_agent_name(self):
        pass

    @abstractmethod
    def get_task_name(self):
        pass

    @abstractmethod
    def get_input_data(self):
        pass

    async def run(self):
        """
        공통된 에이전트 실행 및 결과 처리를 수행하는 메서드
        """
        print(f"\n=== {self.agent_name.title()} Agent Demo ===")
        print(f"Task: {self.task_name}")
        # ... (중략: 공통 로직)

# Researcher 에이전트 시연 클래스
class ResearcherDemonstrator(BaseAgentDemonstrator):
    def get_agent_name(self):
        return "researcher"
    def get_task_name(self):
        return "Quantum Computing Research"
    def get_input_data(self):
        return "What are the latest developments in quantum computing breakthroughs in 2024?"

# Calculator 에이전트 시연 클래스
class CalculatorDemonstrator(BaseAgentDemonstrator):
    def get_agent_name(self):
        return "calculator"
    def get_task_name(self):
        return "CAGR Calculation"
    def get_input_data(self):
        return """Calculate the compound annual growth rate..."""

# 메인 함수에서는 이렇게 활용
async def main_improved():
    tutorial = AdvancedADKTutorial()
    tutorial.create_specialized_agents()
    
    print(f"\n🎯 Running comprehensive agent demonstrations...")
    
    demonstrators = [
        ResearcherDemonstrator(tutorial),
        CalculatorDemonstrator(tutorial),
        # 새로운 에이전트 추가 시 여기에 인스턴스만 추가하면 됨
    ]
    
    for demo in demonstrators:
        await demo.run()
    
    tutorial.display_comprehensive_summary()
```

이렇게 하면, 새로운 `translator` 에이전트를 추가할 때 `TranslatorDemonstrator` 클래스를 만들고 필요한 정보만 채워 넣으면 된다. 코드의 양도 줄고, 유지보수도 훨씬 쉬워질 것이다.

---

### 3. 결론: Scalable Multi-Agent System의 본질

이번 튜토리얼을 통해 얻은 가장 중요한 통찰은 **'확장 가능한 멀티 에이전트 시스템은 기능적 분할과 효율적인 협업 구조에 기반한다'** 는 것이다.

단순히 에이전트의 수를 늘리는 것이 아니라, 각 에이전트가 독립적인 역할을 수행하면서도 전체 시스템의 목표를 달성하기 위해 유기적으로 작동하는 구조를 만드는 것이 중요하다. ADK는 이러한 구조를 구축하는 데 필요한 도구(전문 역할 정의, 도구 통합, 비동기 실행)를 제공함으로써 개발자가 복잡한 오케스트레이션을 쉽게 설계할 수 있게 돕는다.

이러한 시스템은 단순히 한두 가지 작업을 처리하는 것을 넘어, 기업의 다양한 비즈니스 문제를 해결하는 엔터프라이즈급 애플리케이션으로 발전할 잠재력을 가지고 있다. 마치 잘 조직된 팀이 개개인의 전문성을 발휘해 복잡한 프로젝트를 완수하는 것처럼, 멀티 에이전트 시스템도 각 에이전트의 전문성을 조합하여 더욱 복잡하고 가치 있는 결과를 창출할 수 있을 것이다.

---
회사의 프로젝트나 나의 사이드 프로젝트에서 에이전트 시스템 구축을 위해 Google ADK를 자주 사용하고 있기에 이미 어느정도 잘 사용하고 있다고 생각했는데, 생각지 못했던 간단하면서도 효율적인 구조를 만나니 반가웠다. (한편으로는 왜 진작 이렇게 하지 못했는지 스스로에게 질책하기도 했다.) 앞으로도 다른 사람들이 어떻게 활용하는지 샘플 코드를 자주 찾아보면서 내가 활용할 수 있는 것들을 흡수해야겠다.