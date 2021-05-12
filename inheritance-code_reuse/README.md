# 10. 상속과 코드 재사용

> 클래스를 재사용하기 위해 새로운 클래스를 추가하는 가장 대표적인 기법인 상속에 관해 살펴본다.
재사용 관점에서 상속이란 클래스 안에 정의된 인스턴스 변수와 메서드를 자동으로 새로운 클래스에 추가하는 구현 기법이다.

### 01. 상속과 중복 코드

**DRY 원칙 (Don't Repeat Yourself 의 첫글자)**

모든 지식은 시스템 내에서 단일하고, 애매하지 않고, 정말로 믿을 만한 표현 양식을 가져야 한다.</br>
DRY 원칙은 한 번, 단 한번(Once and Only Once) 원칙 또는 단일 지점 제어(Single-Point Control) 원칙 이라고도 부른다.</br>
원칙의 이름이 무엇이건 핵심은 코드 안에 중복이 존재해서는 안 된다는 것이다.</br>

**중복과 변경**

중복 코드가 늘어날수록 어플리케이션은 변경에 취약해지고 버그가 발생할 가능성이 높아진다.</br>
민첩하게 변경하기 위해서는 중복 코드를 추가하는 대신 제거해야 한다. 기회가 생길 때마다 코드를 DRY하게 만들기 위해 노력이 필요하다.</br>

**상속을 이용해 중복 코드 제거하기**

상속을 이용해 코드를 재사용하기 위해서는 부모 클래스의 개발자가 세웠던 가정이나 추론 과정을 정확하게 이해해야 한다.</br>
이것은 자식 클래스의 작성자가 부모 클래스의 구현 방법에 대한 정확한 지식을 가져야 한다는 것을 의미한다.</br>
따라서 상속은 결합도를 높인다. 그리고 상속이 초래하는 부모 클래스와 자식 클래스 사이의 강한 결합이 코드를 수정하기 어렵게 만든다.</br>

**강하게 결합된 Phone과 NightlyDiscountPhone**

```java
public class Phone {
    private Money amount;
    private Duration seconds;
    private List<Call> calls = new ArrayList<>();
    private double taxRate;

    public Phone(Money amount, Duration seconds, double taxRate) {
        this.amount = amount;
        this.seconds = seconds;
        this.taxRate = taxRate;
    }

    public void call(Call call) {
        calls.add(call);
    }

    public List<Call> getCalls() {
        return calls;
    }

    public Money getAmount() {
        return amount;
    }

    public Duration getSeconds() {
        return seconds;
    }

    public Money calculateFee() {
        Money result = Money.ZERO;

        for(Call call : calls) {
            result = result.plus(amount.times(call.getDuration().getSeconds() / seconds.getSeconds()));
        }

        return result.plus(result.times(taxRate));
    }
}
----------------------------------------------
public class NightlyDiscountPhone extends Phone {
    private static final int LATE_NIGHT_HOUR = 22;

    private Money nightlyAmount;
    private Money regularAmount;
    private Duration seconds;

    public NightlyDiscountPhone(Money nightlyAmount, Money regularAmount, Duration seconds) {
        this.nightlyAmount = nightlyAmount;
        this.regularAmount = regularAmount;
        this.seconds = seconds;
    }

    @Override
    protected Money calculateCallFee(Call call) {
        if (call.getFrom().getHour() >= LATE_NIGHT_HOUR) {
            return nightlyAmount.times(call.getDuration().getSeconds() / seconds.getSeconds());
        } else {
            return regularAmount.times(call.getDuration().getSeconds() / seconds.getSeconds());
        }
    }
}
```

상속을 사용하면 적은 노력으로도 새로운 기능을 쉽고, 빠르게 추가할 수 있다. 하지만 그로 인해 커다란 대가를 치러야 할 수도 있다.

```
**상속을 위한 경고 - 1**
자식 클래스의 메서드 안에서 super 참조를 이용해 부모 클래스의 메서드를 직접 호출할 경우 두 클래스는 강하게 결합된다.
super 호출을 제거할 수 있는 방법을 찾아 결합도를 제거해야 한다.
```

### 02. 취약한 기반 클래스 문제

상속은 자식 클래스와 부모 클래스의 결합도를 높인다. 이 강한 결합도로 인해 자식 클래스는 부모 클래스의 불필요한 세부사항에 엮이게 된다.</br>
부모 클래스의 작은 변경에도 자식 클래스는 컴파일 오류와 실행 에러라는 고통에 시달려야 할 수도 있다.</br>
이처럼 부모 클래스의 변경에 의해 자식 클래스가 영향을 받는 현상을 **취약한 기반 클래스 문제(Fragile Base Class Problem, Brittle Base Class Problem)**라고 부른다.</br>
이 문제는 상속을 사용한다면 피할 수 없는 객체지향 프로그래밍 근본적인 취약성이다.</br>

취약한 기반 클래스 문제는 캡슐화를 약화시키고 결합도를 높인다.</br>
상속은 자식 클래스가 부모 클래스의 구현 세부사항에 의존하도록 만들기 때문에 캡슐화를 약화시킨다. → 상속을 피해야 하는 첫 번째 이유이다.</br>

**불필요한 인터페이스 상속 문제**

```
**상속을 위한 경고 - 2**
상속받은 부모 클래스의 메서드가 자식 클래스의 내부 구조에 대한 규칙을 깨트릴 수 있다.
```

**메서드 오버라이딩의 오작용 문제**

```
**상속을 위한 경고 - 3**
자식 클래스가 부모 클래스의 메서드를 오버라이딩할 경우 부모 클래스가 자신의 메서드를 사용하는 방법에 자식 클래스가 결합될 수 있다.
```

조슈아 블로치는 클래스가 상속되기를 원한다면 상속을 위해 클래스를 설계하고 문서화해야 하며, 그렇지 않은 경우에는 상속을 금지시켜야 한다고 주장한다.

**부모 클래스와 자식 클래스의 동시 수정 문제**

상속은 기본적으로 부모 클래스의 구현을 재사용한다는 기본 전제를 따르기 때문에 자식 클래스가 부모 클래스의 내부에 대해 속속들이 알도록 강요한다.

따라소 코드 재사용을 위한 상속은 부모 클래스와 자식 클래스를 강하게 결합시키기 때문에 함께 수정해야 하는 상황 역시 빈번하게 발생할 수 밖에 없다.

```
**상속을 위한 경고 - 4**
클래스를 상속하면 결합도로 인해 자식 클래스와 부모 클래스의 구현을 영원히 변경하지 않거나, 자식 클래스와 부모 클래스를 동시에 변경하거나 둘 중 하나를 선택할 수 밖에 없다.
```

### 03. Phone 다시 살펴보기

취약한 기반 클래스 문제를 완전히 없앨 수는 없지만 어느 정도까지 위험을 완화시키는 것은 가능하다.

문제 해결의 열쇠는 바로 추상화다.

**추상화에 의존하자**

코드 중복을 제거하기 위해 상속을 도입할 때 따르는 두 가지 원칙이 있다.

- 두 메서드가 유사하게 보인다면 차이점을 메서드로 추출한다. 메서드 추출을 통해 두 메서드를 동일한 형태로 보이도록 만들 수 있다.
- 부모 클래스의 코드를 하위로 내리지 말고 자식 클래스의 코드를 상위로 올려라.</br>
  부모 클래스의 구체적인 메서드를 자식 클래스로 내리는 것보다 자식 클래스의 추상적인 메서드를 부모 클래스로 올리는 것이 재사용성과 응집도 측면에서 더 뛰어난 결과를 얻을 수 있다.

**차이를 메서드로 추출하라**

이것은 흔히 말하는 "변하는 것으로부터 변하지 않는 것을 분리하라", 또는 "변하는 부분을 찾고 이를 캡슐화하라" 라는 조언을 메서드 수준에서 적용한 것이다.

### 04. 차이에 의한 프로그래밍

기존 코드와 다른 부분만을 추가함으로써 어플리케이션의 기능을 확장하는 방법을 **차이에 의한 프로그래밍(programming by difference)**이라고 부른다.</br>
상속을 이용하면 이미 존재하는 클래스의 코드를 쉽게 재사용할 수 있기 때문에 어플리케이션의 점진적인 정의(incremental definition)가 가능해진다.</br>

<img src="/inheritance-code_reuse/img/10-5.png" width="500px;" />

상속이 코드를 재사용이라는 측면에서 매우 강력한 도구 인 것은 사실이지만 강력한 만큼 잘못 사용할 경우에 돌아오는 피해 역시 크다는 사실을 뼈저리게 경험한 하였다.</br>
상속의 오용과 남용은 어플리케이션을 이해하고 확장하기 어렵게 만든다. 정말로 필요한 경우에만 상속을 이용해야 한다.</br>
