---
title:  "Nomic Embedding을 활용한 Context-Aware Multi-Agent System 구축 튜토리얼 공부"

categories:
  - AI
  - AI 에이전트
tags:
  - 인공지능
  - AI
  - Agent
  - 에이전트
  - RAG
  - NomicEmbedding
  - 임베딩
author: JinyongShin
toc: true
toc_sticky: true
 
date: 2025-07-27 18:00:00 +0900
---

오늘 ['Building a Context-Aware Multi-Agent AI System Using Nomic Embeddings and Gemini LLM'](https://www.marktechpost.com/2025/07/27/building-a-context-aware-multi-agent-ai-system-using-nomic-embeddings-and-gemini-llm/)을 읽고 공부한 내용을 정리했다. Nomic Embeddings와 Gemini LLM을 활용해 다중 에이전트 AI 시스템을 구축하는 과정이 매우 흥미로웠다. 특히, 메모리 관리와 쿼리 라우팅 방식에 대한 분석이 기억에 남는다.

전체 코드는 [Github](https://github.com/Marktechpost/AI-Tutorial-Codes-Included/blob/main/nomic_gemini_multi_agent_ai_Marktechpost.ipynb)에서 확인할 수 있다.

-----

### **1. Nomic Embedding의 역할과 구현**

오늘 글에서 가장 중요한 기술적 요소는 바로 **Nomic Embedding**이었다. 

#### 1.1 Nomic Embedding이란?

시작부터 Nomic Embedding이 뭔지 몰라 공부를 해야했다. 자세한 내용은 나중에 다루기로 하고 간략하게 설명하고 넘어가겠다.
Nomic embedding은 Nomic AI에서 개발한 텍스트 임베딩 모델로, 긴 문맥(최대 8,192 토큰)을 처리할 수 있는 오픈소스 모델이다. 이 모델은 텍스트를 벡터로 변환하여 단어 의미와 문맥 관계, 유사성 등을 효과적으로 포착한다. ([Nomic Embedding 논문](https://arxiv.org/abs/2402.01613))

Nomic Embedding의 주요 특징은 다음과 같다:

- BERT 기반이며 일반 BERT보다 훨씬 긴 2048 토큰 이상의 문맥을 다룰 수 있도록 확장됨
- 회전 위치 임베딩(rotary positional embedding)을 사용해 긴 시퀀스 처리가 가능
- Flash Attention, Deepspeed 같은 최적화 기술로 성능과 효율성 극대화
- 텍스트뿐만 아니라 이미지 임베딩 모델과 동일 잠재 공간을 공유해 멀티모달 작업에도 강점

즉, Nomic embedding은 긴 문장이나 문서를 효과적으로 임베딩해 자연어 처리와 멀티모달 AI 시스템에서 뛰어난 성능을 내도록 설계된 최첨단 임베딩 기술이다.

#### 1.2 Nomic Embedding의 활용

이 글에서 Nomic Embedding은 텍스트의 **의미적 유사성**을 수치적으로 파악하는 데 사용되었다. 이 시스템에서는 두 가지 주요 용도로 활용되었다.

1.  **지식 검색(RAG)**: `IntelligentAgent` 클래스에서 `add_knowledge` 메서드를 통해 문서를 벡터 스토어에 저장하고, `search_knowledge` 메서드로 특정 쿼리와 유사한 문서를 찾는 데 사용되었다.
2.  **의미적 라우팅**: `MultiAgentSystem` 클래스에서 `route_query` 메서드를 통해 사용자의 쿼리 임베딩과 에이전트별 설명 임베딩 간의 유사도를 계산하여 가장 적합한 에이전트를 선택하는 데 사용되었다.

이처럼 Nomic Embedding은 단순한 텍스트 매칭을 넘어, 의미 기반의 지식 검색과 동적 라우팅을 가능하게 하는 핵심적인 역할을 수행했다.

이를 구현하기 위해 다음의 패키지를 설치하고 활용했다.

```python
# 설치 패키지
!pip install -qU langchain-nomic langchain-core langchain-community langchain-google-genai faiss-cpu numpy matplotlib

# 임포트 패키지
from langchain_nomic import NomicEmbeddings
```

-----

### **2. RAG(Retrieval-Augmented Generation) 패턴 분석**

글에서 제시된 시스템은 RAG 패턴을 효과적으로 구현했다. 이 패턴은 `IntelligentAgent` 클래스의 `reason_and_respond` 메서드를 통해 이루어진다.

1.  **Retrieval (검색)**: 사용자의 쿼리가 들어오면, 먼저 **Nomic Embedding**을 이용해 쿼리 벡터를 생성한다. 그 후, 이 쿼리 벡터와 에이전트의 영구 지식 저장소(벡터 스토어)에 있는 문서 벡터들의 유사도를 비교하여 가장 관련성이 높은 문서들을 검색한다. 이와 함께 에피소드 메모리(과거 대화 기록)에서도 유사한 대화들을 찾아낸다.
2.  **Augmentation (증강)**: 검색된 관련 지식 문서와 유사한 대화 기록을 프롬프트에 포함할 **컨텍스트**로 구성한다.
3.  **Generation (생성)**: 최종적으로 컨텍스트가 포함된 프롬프트를 Gemini LLM에 전달하여, 검색된 정보를 바탕으로 더 정확하고 맥락에 맞는 최종 답변을 생성한다.

이러한 과정을 통해 단순한 텍스트 생성에서 벗어나 외부 지식을 활용한 답변을 만들어내는 RAG 패턴을 성공적으로 구현했다.

```python
# RAG 패턴 구현의 핵심 코드 (reason_and_respond 메서드)
def reason_and_respond(self, user_input: str) -> str:
    """Main reasoning pipeline with context integration"""
    
    # 1. Retrieval 단계: 유사 메모리 및 관련 지식 검색
    similar_memories = self.retrieve_similar_memories(user_input, k=2)
    relevant_docs = self.search_knowledge(user_input, k=3)
    
    # 2. Augmentation 단계: 검색된 정보를 컨텍스트로 구성
    context = {
        "similar_memories": similar_memories,
        "relevant_knowledge": [doc.page_content for doc in relevant_docs],
        "working_memory": self.memory.working
    }
    
    # 3. Generation 단계: 컨텍스트를 활용하여 응답 생성
    response = self._generate_contextual_response(user_input, context)
    
    self.remember_interaction(user_input, response, context)
    
    self.memory.working["last_query"] = user_input
    self.memory.working["last_response"] = response
    
    return response
```

-----

### **3. 의미적 라우팅(Semantic Routing) 기법**

이 시스템은 여러 에이전트 중 가장 적합한 에이전트를 선택하기 위해 **의미적 라우팅**을 사용했다. `MultiAgentSystem` 클래스의 `route_query_with_confidence` 메서드가 이 기능을 담당한다.

1.  **쿼리 임베딩**: 사용자의 쿼리를 **Nomic Embedding** 모델을 사용해 벡터로 변환한다.
2.  **에이전트 설명 임베딩**: 각 에이전트의 역할(`research`, `chat`)에 대한 설명(`"analysis, research, data, statistics..."`, `"conversation, questions, general discussion..."`)을 미리 정의하고, 이를 벡터로 변환한다.
3.  **유사도 비교**: 쿼리 임베딩과 각 에이전트 설명 임베딩 간의 **코사인 유사도**를 계산한다.
4.  **에이전트 선택**: 유사도 점수가 가장 높은 에이전트를 최종적으로 선택하여 쿼리를 전달한다.

<!-- end list -->

```python
# 의미적 라우팅 구현의 핵심 코드 (route_query_with_confidence 메서드)
def route_query_with_confidence(self, query: str) -> tuple[str, float]:
    """Route query to most appropriate agent and return confidence"""
    
    agent_descriptions = {
        "research": "analysis, research, data, statistics, technical information",
        "chat": "conversation, questions, general discussion, casual talk"
    }
    
    query_embedding = self.coordinator_embeddings.embed_query(query)
    best_agent = "chat"
    best_similarity = 0.0
    
    for agent_name, description in agent_descriptions.items():
        desc_embedding = self.coordinator_embeddings.embed_query(description)
        similarity = np.dot(query_embedding, desc_embedding)
        
        if similarity > best_similarity:
            best_similarity = similarity
            best_agent = agent_name
            
    return best_agent, best_similarity
```

-----

### **4. 계층적 Memory 관리**

이 시스템은 에이전트의 기억을 세 가지 유형으로 계층적으로 관리한다. `AgentMemory`라는 데이터 클래스를 통해 구현되었다.

1.  **Episodic Memory (일화 기억)**: 과거 대화 기록을 순서대로 저장하는 공간이다.
2.  **Semantic Memory (의미 기억)**: RAG를 통해 구축된 영구적인 지식(벡터 스토어)을 의미한다.
3.  **Working Memory (작업 기억)**: 현재 진행 중인 대화의 단기적인 맥락 정보를 저장하는 공간이다.

이러한 계층적 메모리 구조를 통해 에이전트는 다양한 수준의 정보를 활용하여 더 정교하고 맥락에 맞는 추론을 할 수 있었다.

```python
# Memory 계층적 관리를 위한 AgentMemory 데이터 클래스
@dataclass
class AgentMemory:
    """Agent's episodic and semantic memory"""
    episodic: List[Dict[str, Any]]  # 일화 기억 (과거 대화 기록)
    semantic: Dict[str, Any]        # 의미 기억 (장기 지식)
    working: Dict[str, Any]         # 작업 기억 (단기 컨텍스트)
```
---
### 결론

이번 학습을 통해 Nomic Embedding과 같은 임베딩 모델, 그리고 Gemini와 같은 LLM을 결합하여 컨텍스트를 인지하는 다중 에이전트 시스템을 구축하는 방법론을 깊이 이해할 수 있었다.

가장 중요한 통찰은 **AI 시스템의 지능을 향상시키기 위해 단순히 LLM의 성능에만 의존하는 것이 아니라, 외부 지식과 과거 경험을 효율적으로 관리하고 활용하는 아키텍처를 설계하는 것이 필수적**이라는 점이다. 이 글에서 제시된 RAG 패턴과 계층적 메모리 관리는 이러한 통찰을 구체적으로 보여주는 좋은 예시였다.

1.  **Nomic Embedding의 활용**: Nomic Embedding은 단순한 텍스트 매칭을 넘어, 의미 기반의 지식 검색과 동적 라우팅을 가능하게 하는 핵심 기술이었다. 특히, RAG를 통해 외부 지식을 효과적으로 검색하고, 의미적 라우팅을 통해 사용자의 의도에 따라 전문 에이전트를 할당하는 방식은 AI 시스템의 정확성과 효율성을 크게 높일 수 있는 방안이었다.
2.  **지능적인 메모리 관리**: 일화 기억, 의미 기억, 작업 기억으로 구성된 계층적 메모리 시스템은 에이전트가 단기적인 대화 맥락뿐만 아니라, 장기적인 지식과 과거 경험까지 종합적으로 고려하여 추론할 수 있게 한다. 이는 인간의 기억 체계를 모방한 것으로, AI의 응답이 더 일관되고 맥락에 맞게 이루어지도록 돕는다.
3.  **임베딩 모델 선택의 중요성**: 그동안 RAG의 핵심 개념인 **임베딩**에 대해 깊이 고민하지 않았던 점을 반성하게 되었다. 임베딩 모델의 성능은 RAG 시스템의 검색 정확도와 직결된다. 어떤 임베딩 모델을 사용하느냐에 따라 쿼리와 문서 간의 의미적 유사도를 얼마나 잘 파악하는지가 결정되기 때문이다. 따라서 앞으로는 모델의 특성과 성능을 고려하여 **Nomic Embedding**과 같이 목적에 맞는 임베딩 모델을 신중하게 선택하고 활용해야겠다는 중요한 교훈을 얻었다.

결론적으로, 이번 학습은 단순히 코드를 이해하는 것을 넘어, 현대 AI 에이전트 시스템이 어떻게 지능적으로 설계되는지에 대한 깊이 있는 통찰을 제공했다. 앞으로 이러한 원리를 바탕으로 더 복잡하고 실용적인 AI 시스템을 설계하고 구현하는 데 이 지식을 적극적으로 활용할 것이다.