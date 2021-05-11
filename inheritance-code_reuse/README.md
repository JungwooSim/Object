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

