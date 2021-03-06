# 05. 책임 할당하기
> 이번 장에서 살펴볼 GRASP 패턴은 책임 할당의 어려움을 해결하기 위한 답을 제시해줄 것이다.<br>
> GRASP 패턴을 이해하고 나면 응집도와 결합도, 캡슐화 같은 다양한 기준에 따라 책임을 할당하고 결과를 트레이드 오프할 수 있는 기준을 배우게 될 것이다.

### 01. 책임 주도 설계를 향해

데이터 중심의 설계에서 책임 중심 설계로 전환하기 위해서는 다음의 두 가지 원칙을 따라야 한다.

- 데이터보다 행동을 먼저 결정하라
- 협력이라는 문맥 안에서 책임을 결정하라

**데이터보다 행동을 먼저 결정하라**

객체에게 중요한 것은 데이터가 아니라 외부에 제공하는 행동이다.<br>
클라이언트의 관점에서 객체가 수행하는 행동이란 곧 객체의 책임을 의미한다.<br>

**협력이라는 문맥 안에서 책임을 결정하라**

객체에게 적절한 책임을 할당하기 위해서는 협력이라는 문맥을 고려해야 한다.<br>
협력이라는 문맥에서 적절한 책임이란 곧 클라이언트의 관점에서 적절한 책임을 의미한다.<br>

## 02. 책임 할당을 위한 GRASP 패턴

GRASP 은 "General Responsibility Assignment Software Pattern(일반적인 책임 할당을 위한 소프트웨어 패턴)"의 약자로 객체에게 책임을 할당할 때 지침을 삼을 수 있는 원칙들의 집합을 패턴 형식으로 정리한 것이다.

**도메인 개념에서 출발하기**

설계를 시작하기 전에 도메인에 대한 개략적인 모습을 그려 보는 것이 유용하다.<br>
도메인 안에는 무수히 많은 개념들이 존재하며 이 도메인 개념들을 책임 할당의 대상으로 사용하면 코드에 도메인의 모습을 투영하기가 좀 더 수월해진다.<br>

<img src="/general_responsibility_assignment/img/5-1.png" width="500px;" />

**정보 전문가에게 책임을 할당하라**

객체에게 책임을 할당하는 첫 번째 원칙은 책임을 수행할 정보를 알고 있는 객체에게책임을 할당하는 것이다.<br>
GRASP에서는 이를 INFORMATION EXRERT(정보 전문가) 패턴이라고 부른다.<br>

```
**INFORMATION EXPERT 패턴**
책임을 수행하는데 필요한 정보를 가지고 있는 객체에게 할당하라.
INFORMATION EXPERT 패턴은 객체가 자율적인 존재여야 한다는 사실을 다시 한번 상기 시킨다.
정보를 알고 있는 객체만이 어떻게 수행할지 스스로 결정할 수 있기 때문이다.
INFORMATION EXPERT 패턴을 따르면 정보와 행동을 최대한 가까운 곳에 위치시키기 때문에 캡슐화를 유지할 수 있다.
```

**높은 응집도와 낮은 결합도**

높응 응집도와 낮은 결합도는 객체에 책임을 할당할 때 항상 고려해야 하는 기본 원리다.<br>
책임을 할당할 수 있는 다양한 대안들이 존재한다면 응집도와 결합도의 측면에서 더 나은 대안을 선택하는 것이 좋다.<br>
다시 말해 두 협력 패턴 중에서 응집도와 낮은 결합도를 얻을 수 있는 설계가 있다면 그 설계를 선택해야 한다는 것이다.<br>
GRASP에서는 이를 LOW COUPLING(낮은 결합도) 패턴과 HIGH COHESION(높은 응집도) 패턴이라고 부른다.<br>

```
**LOW COUPLING 패턴**
낮은 결합도는 모든 설계 결정에서 염두해 둬야 하는 원리다.
현재의 책임 할당을 검토하거나 여러 설계 대안들이 있을 때 낮은 결합도를 유지할 수 있는 설계를 선택하라.

**HIGH COHESION 패턴**
높은 응집도를 유지할 수 있게 책임을 할당하라.
****현재의 책임 할당을 검토하고 있거나 여러 설계 대안 중 하나를 선택해야 한다면 높은 응집도를 유지할 수 있는 설계를 선택하라.
```

LOW COUPLING 패턴과 HIGH COHESION 패턴은 설계를 진행하면서 책임과 협력의 품질을 검토하는데 사용할 수 있는 중요한 평가 기준이다.<br>
책임을 할당하고 코드를 작성하는 매순간마다 LOW COUPLING과 HIGH COHESION 의 관점에서 전체적인 설계 품질을 검토하면 단순하면서도 재사용 가능하고 유연한 설계를 얻을 수 있을 것이다.<br>

**창조자에게 객체 생성 책임을 할당하라**

GRASP 의 CREATOR(창조자) 패턴은 이 같은 경우에 사용할 수 있는 책임 할당 패턴으로서 객체를 생성할 책임을 어떤 객체에게 할당할지에 대한 지침을 제공한다.

```
**CREATOR 패턴**
객체 A를 생성해야 할 때 어떤 객체에게 객체 생성 책임을 할당해야 하는가? 아래 조건을 최대한 많이 만족하는 B에게 객체 생성 책임을 할당하라.
- B 가 A 객체를 포함하거나 참조한다.
- B 가 A 객체를 기록한다.
- B 가 A 객체를 긴밀하게 사용한다.
- B 가 A 객체를 ****객체를 초기화하는데 필요한 데이터를 가지고 있다(이 경우 B는 A에 대한 정보 전문가다)
```

CREATOR 패턴의 의도는 어떤 방식으로든 생성되는 객체와 연결되거나 관련될 필요가 있는 객체에 해당 객체를 생성할 책임을 맡기는 것이다.<br>
생성될 객체에 대해 잘 알고 있어야 하거나 그 객체를 사용해야 하는 객체는 어떤 방식으로든 생성될 객체와 연결될 것이다. 다시 말해 두 객체는 서로 결합된다.<br>
이미 결합돼 있는 객체에게 생성 책임을 할당하는 것은 설계의 전체적인 결합도에 영향을 미치지 않는다.<br>
결과적으로 CREATOR 패턴은 이미 존재하는 객체 사이의 관계를 이용하기 때문에 설계가 낮은 결합도를 유지할 수 있게 한다.<br>

### 03. 구현을 통한 검증

**DiscountCondition 개선하기**

변경에 취약한 클래스는 코드를 수정해야 하는 이유는 하나 이상 가지는 클래스이다.<br>
응집도가 낮다는 것은 서로 연관성이 없는 기능이나 데이터가 하나의 클래스 안에 뭉쳐져 있다는 것을 의미한다.<br>

변경의 이유가 하나 이상인 클래스에는 위험 징후를 또렷하게 드러내는 몇 가지 패턴이 존재한다.<br>
코드를 통해 변경의 이유를 파악할 수 있는 첫 번째 방법은 인스턴스 변수가 초기화되는 시점을 살펴보는 것이다.<br>
응집도가 높은 클래스는 인스턴스를 생성할 때 모든 속성을 함께 초기화한다.<br>
반면 응집도가 낮은 클래스는 객체의 속성 중 일부만 초기화하고 일부는 초기화되지 않은 상태로 남겨진다.<br>
코드를 통해 변경의 이유를 파악할 수 있는 두 번째 방법은 메서드들이 인스턴스 변수를 사용하는 방식을 살펴보는 것이다.<br>
모든 메서드가 객체의 모든 속성을 사용한다면 클래스의 응집도는 높다고 볼 수 있다.<br>
반면 메서드들이 사용하는 속성에 따라 그룹이 나뉜다면 클래스의 응집도가 낮다고 볼 수 있다.<br>

```
**클래스 응집도 판단하기**
클래스의 응집도를 판단할 수 있는 3가지 방법을 살펴 보았다.
- 클래스가 하나 이상의 이유로 변경돼야 한다면 응집도가 낮은 것이다. 변경의 이유를 기준으로 클래스를 분리하라.
- 클래스의 인스턴스를 초기화하는 시점에 경우에 따라 서로 다른 속성들을 초기화하고 있다면 응집도가 낮은 것이다. 초기화되는 속성의 그룹을 기준으로 클래스를 분리하라.
- 메서드 그룹이 속성 그룹을 사용하는지 여부로 나뉜다면 응집도가 낮은 것이다. 이들 그룹을 기준으로 클래스를 분리하라.
```

**타입 분리하기**

**다형성을 통해 분리하기**

자바에서는 일반적으로 역할을 구현하기 위해 추상 클래스나 인터페이스를 사용한다.<br>
역할을 대체할 클래스들 사이에서 구현을 공유해야 할 필요가 있다면 추상 클래스를 사용하면 된다.<br>
구현을 공유할 필요 없이 역할을 대체하는 객체들의 책임만 정의하고 싶다면 인터페이스를 사용하면 된다.<br>

객체의 타입에 따라 변하는 행동이 있다면 타입을 분리하고 변화하는 행동을 각 타입의 책임으로 할당해야 한다.<br>
GRASP에서는 이를 POLYMORPHISM(다형성) 패턴이라고 부른다.<br>

<img src="/general_responsibility_assignment/img/5-6.png" width="500px;" />

```
**POLYMORPHISM 패턴**
POLYMORPHISM 패턴은 객체의 타입을 검사해서 타입에 따라 여러 대안들을 수행하는 조건적인 논리를 사용하지 말라고 경고한다.
대신 다형성을 이용해 새로운 변화를 다루기 쉽게 확장하라고 권고한다.
```
