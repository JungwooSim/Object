# 09. 유연한 설계
## 01. 개방-폐쇄 원칙

로버튼 마틴은 확장 가능하고 변화에 유연하게 대응할 수 있는 설계를 만들 수 있는 원칙 중 하나로 개방-폐쇄(Open-Closed Principle, OCP)을 고안했다.</br>
"소프트웨어 개체(클래스, 모듈, 함수, 등등)는 확장에 대해 열려 있어야 하고, 수정에 대해서는 닫혀 있어야 한다."</br>

- 확장에 대해 열려 있다.
    - 어플리케이션의 요구사항이 변경될 때 이 변경에 맞게 새로운 '동작'을 추가해서 어플리케이션의 기능을 확장할 수 있다.
- 수정에 닫혀 있다.
    - 기존의 '코드'를 수정하지 않고도 어플리케이션의 동작을 추가하거나 변경할 수 있다.

**컴파일타임 의존성을 고정시키고 런타임 의존성을 변경하라**

<img src="/supple_design/img/9-1.png" width="500px;" />

<img src="/supple_design/img/9-2.png" width="500px;" />

의존성 관점에서 개방-폐쇄 원칙을 따르는 설계란 컴파일타임 의존성은 유지하면서 런타임 의존성의 가능성을 확장하고 수정할 수 있는 구조라고 할 수 있다.

**추상화가 핵심이다**

개방-폐쇄 원칙의 핵심은 추상화에 의존하는 것이다.</br>
추상화란 핵심적인 부분만 남기고 불필요한 부분은 생략함으로써 복잡성을 극복하는 기법이다.</br>

```java
public abstract class DiscountPolicy {
    private List<DiscountCondition> conditions = new ArrayList<>();

    public DiscountPolicy(DiscountCondition ... conditions) {
        this.conditions = Arrays.asList(conditions);
    }

    public Money calculateDiscountAmount(Screening screening) {
        for(DiscountCondition each : conditions) {
            if (each.isSatisfiedBy(screening)) {
                return getDiscountAmount(screening);
            }
        }

        return Money.ZERO;
    }

    abstract protected Money getDiscountAmount(Screening Screening);
}
```

위 코드를 보면 변하지 않는 부분은 할인 여부를 판단하는 로직이고 변하는 부분은 할인된 요금을 계산하는 방법이다. 따라서 `DiscountPolicy Class` 추상화 이다.

변하는 부분을 고정하고 변하지 않는 부분을 생략하는 추상화 메커니즘이 개방-폐쇄 원칙의 기반이 된다는 사실을 주목해야 한다.</br>
언제라도 추상화의 생략된 부분을 채워넣음으로써 새로운 문맥에 맞게 기능을 확장할 수 있다.</br>
따라서 추상화는 설계의 확장을 가능하게 한다.</br>
주의할 점은 추상화를 했다고 모든 수정에 대해 설계가 폐쇄되는 것이 아니라는 점이다.</br>
변경에 의한 파급효과를 최대한 피하기 위해서는 변하는 것과 변하지 않는 것이 무엇인지를 이해하고 이를 추상화의 목적으로 삼아야 한다.</br>

### 02. 생성 사용 분리

```java
public class Movie {
    private DiscountPolicy discountPolicy;
    
    public Movie() {
        this.discountPolicy = new AmountDiscountPolicy();
    }    
}
```

`Movie Class` 가 DiscountPolicy 라는 추상화에만 의존하기 위해서는 Movie 내부에서 AmountDiscountPolicy 같은 구체화 클래스의 인스턴스를 직접생성해는 안된다.</br>
이 코드에서 DiscountPolicy 을 변경하는 방법은  Movie 생성자에서 new AmountDiscountPolicy(); 를 수정하는 방법 밖에 없다.</br>
이것은 동작을 추가하거나 변경하기 위해 기존의 코드를 수정하도록 만들기 때문에 개방-폐쇄 원칙을 위반한다.</br>

**FACTORY 추가하기**

위 Movie 의 문제를 해결하는 방법중 한가지는 FACTORY 패턴을 사용하는 것이다.</br>
생성과 사용을 분리하기 위해 객체 생성에 특화된 객체를 FACTORY라고 부른다.</br>

```java
public class Factory {
    public Movie createAvatarMovie() {
        return new Movie("아바타",
                Duration.ofMinutes(120),
                Money.wons(10000),
                new AmountDiscountPolicy(
                    Money.wons(800),
                    new SequenceCondition(1),
                    new SequenceCondition(10)));
    }
}
----------------------------------------------------------------
public class Client {
    private Factory factory;

    public Client(Factory factory) {
        this.factory = factory;
    }

    public Money getAvatarFee() {
        Movie avatar = factory.createAvatarMovie();
        return avatar.getFee();
    }
}
```

이렇게 FACTORY 를 사용하면 Movie 와 AmountDiscountPolicy 를 생성하는 책임 모두를 FACTORY 로 이동할 수 있다.</br>
Client 에는 사용과 관련된 책임만 남게 되는데 하나는 FCATORY 를 통해 생성된 Movie 객체를 얻기 위한 것이고 다른 하나는 Movie 를 통해 가격을 계산하기 위한 것이다.</br>

<img src="/supple_design/img/9-6.png" width="500px;" />

**순수한 가공물에게 책임 할당하기**

크레이그 라만은 시스템을 객체로 분해하는 데는 크게 두 가지 방식이 존재한다고 설명한다.</br>
표현적 분해(representational decomposition)이고 다른 하나는 행위적 분해(behavioral decomposition)다.</br>
표현적 분해는 도메인에 존재하는 사물 또는 개념을 표현하는 객체들을 이용해 시스템을 분해하는 것이다.</br>
표현적 분해는 도메인 모델에 담겨 있는 개념과 관계를 따르며 도메인과 소프트웨어 사이의 표현적 차이를 최소화하는 것을 목적으로 한다.</br>
따라서 객체지향 설계를 위한 가장 기본적인 접근법이다.</br>

모든 책임을 도메인 객체에게 할당하면 낮은 응집도, 높은 결합도, 재사용성 저하와 같은 심각한 문제점에 봉착하게 될 가능성이 높아진다.</br>
이 경우 도메인 개념을 표현한 객체가 아닌 설계자가 편의를 위해 임의로 만들어낸 가공의 객체에게 책임을 할당해서 문제를 해결해야 한다.</br>
도메인과 무관한 인공적인 객체를 PURE FABRICATION(순수한 가공물)이라고 부른다.</br>

어떤 행동을 추가하려고 하는데 이 행동을 책임질 마땅한 도메인 개념이 존재하지 않는다면 PURE FABRICATION 을 추가하고 이 객체에게 책임을 할당해야 한다.</br>
그 결과로 추가된 PURE FABRICATION 은 보통 특정한 행동을 표현하는 것이 일반적이다.</br>
따라서 PURE FABRICATION 은 표현적 분해보다는 행위적 분해에 의해 생성되는 것이 일반적이다.</br>

먼저 도메인의 본질적인 개념을 표현하는 추상화를 이용해 애플리케이션을 구축하기 시작해야 한다. 만약 도메인 개념이 만족스럽지 못한다면 주저말고 PURE FABRICATION 을 만들어야 한다.

```
**PURE FABRICATION 패턴**
PURE FABRICATION 은 INFORMATION EXPERT 패턴에 따라 책임을 할당한 결과가 바람직하지 않을 경우 대안으로 사용된다.
어떤 객체가 책임을 수행하는데 필요한 많은 정보를 가졌지만 해당 책임을 할당할 경우 응집도가 낮아지고 결합도가 높아진다면 가공의 객체를 추가해서 책임을 옮기는 것을 고민해야 한다.
순수한 가공물(pure fabrication) 이라는 표현은 적절한 대안이 없을 때 사람들이 창조적인 무언가를 만들어낸다는 것을 의미하는 관용적인 표현이다.
```

### 03. 의존성 주입

사용하는 객체가 아닌 외부의 독립적인 객체가 인스턴스를 생성한 후 이를 전달해서 의존성을 해결하는 방법을 의존성 주입(Dependency Injection) 이라고 부른다.</br>
의존성 주입에서는 의존성을 해결하는 세 가지 방법을 가리키는 별도의 용어를 정의한다.</br>

- 생성자 주입(constructor injection) : 객체를 생성하는 시점에 생성자를 통한 의존성 해결
- setter 주입(setter injection) : 객체 생성 후 setter 메서드를 통해 의존성 해결
- 메서드 주입(method injection) : 메서드 실행 시 인자를 이용한 의존성 해결

setter 주입의 단점은 객체가 올바로 생성되기 위해 어떤 의존성이 필수적인지를 명시적으로 표현할 수 없다.</br>
메서드 주입은 메서드 호출 주입(method call injection) 이라고도 부르며 메서드가 의존성을 필요로 하는 유일한 경우일 때 사용할 수 있다.</br>
생성자 주입을 통해 의존성을 전달받으면 객체가 올바른 상태로 생성되는데 필요한 의존성을 명확하게 표현할 수 있다는 장점이 있지만 주입된 의존성이 한 두 개의 메서드에서만 사용된다면 각 메서드의 인자로 전달받는 방법이 더 나은 방법이 될 수 있다.</br>
