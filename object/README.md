# 01. 객체, 설계
### 02. 무엇이 문제인가?

**예상을 빗나가는 코드**

**변경에 취약한 코드**

의존성은 어떤 객체가 변경될 때 그 객체에 의존하는 다른 객체도 변경될 수 있다는 사실이 내포되어 있다.<br>
우리의 목표는 어플리케이션의 기능을 구현하는데 필요한 최소한 의존성만 유지하고 불필요한 의존성을 제거하는 것이다.<br>
객체 사이의 의존성이 과한 경우를 가르켜 **결합도(coupling)** 가 높다고 한다.<br>
설계의 목표는 객체 사이의 결합도를 낮춰 변경이 용이한 설계를 만드는 것이어야 한다.<br>

### 03. 설계 개선하기

**자율성을 높이자**

객체를 인터페이스와 구현으로 나누고 인터페이스만을 공개하는 것은 객체 사이의 결합도를 낮추고 변경하기 쉬운 코드를 작성하기 위해 따라야 하는 가장 기본적인 설계 원칙이다.

**캡슐화와 응집도**

밀접하게 연관된 작업만을 수행하고 연관성 없는 작업은 다른 객체에게 위임하는 객체를 가리켜 **응집도(cohesion)** 가 높다고 말한다.<br>
자신의 데이터를 스스로 처리하는 자율적인 객체를 만들면 결합도를 낮출 수 있을뿐더러 응집도를 높일 수 있다.<br>
객체의 응집도를 높이기 위해서는 스스로 자신의 데이터를 책임져야 한다.<br>
객체는 자신의 데이터를 스스로 처리하는 자율적인 존재여야 한다.<br>

**절차지향과 객체지향**

프로세스와 데이터를 별도의 모듈에 위치시키는 방식을 **절차적 프로그래밍(Procedural Programming)** 이라고 부른다.<br>
절차적 프로그래밍은 프로세스가 필요한 모든 데이터에 의존해야 한다는 근본적인 문제점 때문에 변경에 취약할 수 밖에 없다.<br>
데이터와 프로세스가 동일한 모듈 내부에 위치하도록 프로그래밍하는 방식을 **객체지향 프로그래밍(Object-Oriented Programming)** 이라고 부른다.<br>
훌륭한 객체지향 설계의 핵심은 캡슐화를 이용해 의존성을 적절히 관리함으로써 객체 사이의 결합도를 낮추는 것이다.<br>
일반적으로 객체지향이 절차지향에 비해 변경에 좀 더 유연하다고 말하는 이유가 객체 사이의 결합도가 낮기 때문이다.<br>

**책임의 이동**

### 04. 객체지향 설계

**설계가 왜 필요한가?**

글쓴이의 설계의 정의는 설계란 코드를 배치하는 것이다.<br>
설계는 코드를 작성하는데 매 순간 코드를 어떻게 배치할 것인지를 결정하는 과정에서 나온다.<br>
프로그램은 두 가지 요구사항을 만족시켜야 한다.<br>
오늘 완성해야 하는 기능을 구현하는 코드를 짜는 동시에, 내일 쉽게 변경할 수 있는 코드를 짜야 한다.<br>

**객체지향 설계**

객체지향 프로그래밍은 의존성을 효율적으로 통제할 수 있는 다양한 방법을 제공함으로써 요구사항 변경에 좀 더 수월하게 대응할 수 있는 가능성을 높여준다.<br>
훌륭한 객체지향 설계란 협력하는 객체 사이의 의존성을 적절하게 관리하는 설계다.<br>
