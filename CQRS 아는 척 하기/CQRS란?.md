CQRS는 Command and Query Responsibility Segregation(명령과 조회의 책임 분리)을 나타냅니다. 이름처럼 시스템에서 명령을 처리하는 책임과 조회를 처리하는 책임을 분리하는 것이 CQRS의 핵심입니다. 이제 명령과 조회에 대해 정의할 필요가 있습니다. CQRS에서 명령은 시스템의 상태를 변경하는 작업을 의미하며 조회는 시스템의 상태를 반환하는 작업을 의미합니다. 정리하면, CQRS는 시스템의 상태를 변경하는 작업과 시스템의 상태를 반환하는 작업의 책임을 분리하는 것입니다.

> 모든 연산이 명령과 조회로 쉽게 양분되지는 않습니다. 개념적으로 어려운 경우도 있고 동시성 등 기술적인 문제도 있습니다. Martin Fowler는 스택 자료구조의 `pop()` 연산을 예로 들었습니다.

너무 단순하다고 생각될지 모르겠지만 이것이 전부입니다. 어쩌면 CQRS에 대한 오해는 CQRS가 생각보다 복잡하지 않기 때문일지도 모릅니다. 이 단순한 규칙이 몇 가지 응용기술과 조합되어 시스템에 적용되면 그 모습은 무척이나 다양합니다. 그만큼 CQRS를 설명하는 정보들이 표현하는 구현체의 모습이 제각각이고 여기서 혼란이 시작될 가능성이 있습니다. CQRS를 설명할 때 명령 처리기 패턴(Command Processor Pattern)을 얘기하기도 하고 다른 경우는 다계층 아키텍처(Multitier Architecture)나 이벤트 소싱(Event Sourcing)을 다룹니다. 이것들 모두와 DDD(Domain-Driven Design)를 조합하기도 합니다.

CQRS를 처음으로 소개한 Greg Young은 CQRS는 아주 단순한 패턴(_“CQRS is a very simple pattern”_)이라고 말했습니다. 물론 Greg Young은 DDD의 고통을 해결하기 위해 CQRS를 사용했다고 하지만 DDD에 국한된 기법은 아닙니다. 이 글에서는 CQRS의 적용 예를 설명하기 위해 다계층 아키텍처를 사용하지만 이것은 단지 하나의 예시일 뿐 CQRS는 아키텍처 독립적입니다. 다시 강조하지만 CQRS 자체는 복잡하거나 거대하지 않습니다. 지금 당장 시스템에 적용해 볼 수 있으며 경우에 따라 이미 실천하고 있을지도 모릅니다.

> CQRS는 [CQS(Command and Query Separation)](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation) 원리에 기원합니다. 사실 CQRS는 처음엔 CQS의 확장으로 얘기되었습니다. 하지만 CQS는 명령과 조회를 연산 수준에서 분리하는 반면 CQRS는 개체(object)나 시스템(혹은 하위 시스템) 수준에서 분리합니다.

