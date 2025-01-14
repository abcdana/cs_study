# 아이템 17 : 변경 가능성을 최소화하라

# 불변 객체

- 인스턴스 내부의 값을 수정할 수 없는 객체
- 불변 인스턴스에 간직된 정보는 고정되어 객체가 파괴될 때까지 절대 변하면 안된다.
- 객체 데이터의 수정이 불가능한 것이 아니라 **힙 영역에 저장된 값의 수정이 불가능한 것**으로 **재할당은 가능**하다.

예시로 `String`, `Boolean`, `Integer`, `Float`, `Long` 등이 존재한다.

## 불변 객체를 만드는 규칙

1. 객체의 상태를 변경하는 메서드(변경자)를 제공하지 않는다.
2. 클래스를 확장할 수 없도록 한다.
    - 하위 클래스에서 객체의 상태를 변하게 만드는 것을 막아준다.
    - 상속을 막는 대표적인 방법으로 `final`로 선언하는 것이 있다.
        
        ```java
        public final class Exam {
        ```
        
3. 모든 필드를 `final`로 선언한다.
    - 시스템이 강제하는 수단을 이용해 설계자의 의도를 드러내는 방법이다.
    - 새로 생성된 인스턴스를 동기화과정 없이 다른 스레드에서도 동작하는것을 보장한다.
4. 모든 필드를 `private`으로 선언한다.
    - 필드가 참조하는 가변 객체를 클라이언트에서 직접 접근해 수정하는 것을 막아준다.
    - 불변 객체를 참조하는 필드를 `public final`로만 선언해도 불변 객체가 되지만 이렇게 하면 다음 릴리스에서 내부 표현을 변경하지 못하는 상태가 된다.
5. 자신 외에는 내부의 가변 컴포넌트에 접근할 수 없도록 한다.
    - 클래스에 가변객체를 참조하는 필드가 있다면 클라이언트에서 그 객체의 참조를 얻을 수 없도록 해야한다.
        
        이런 필드는 클라이언트가 제공한 객체 참조를 가리키면 안되며, 접근자 메서드가 그 필드를 그대로 반환해서도 안된다. 생성자, 접근자 `readObject` 메서드 모두에서 방어적 복사를 수행하는게 좋다.
        
    
    ```java
    public class Exam {
    	private final List<Integer> numbers;
    	
    	public Exam(List<Integer> numbers){
    		this.numbers = List.copyOf(numbers);
    		// 생성자에 방어적 복사
    	}
    }
    ```
    

## 불변 객체를 사용해야 하는 이유

1. 불변 객체는 생성된 시점의 상태를 파괴할 때까지 그대로 간직한다.
2. 불변 객체는 근본적으로 스레드에 안전하여 따로 동기화할 필요가 없다.
    
    → 그 어떤 스레드도 다른 스레드에 영향을 줄 수 없으니, 불변 객체는 안심하고 공유할 수 있다.
    
    가장 쉬운 방법은 **자주 쓰이는 값들을 상수(public static final)** 로 만드는 것이다.
    
    ```java
    public static final Complex ZERO = new Complex(0, 0);
    public static final Complex ONE  = new Complex(1, 0);
    public static final Complex I    = new Complex(0, 1);
    ```
    
3. 불변 클래스는 **자주 사용되는 인스턴스를 캐싱하여 같은 인스턴스를 중복 생성하지 않게 해주는 정적 팩터리**를 제공할 수 있다.
    
    → 생성자를 `private`로 만들어서 상속을 막는다.
    
    - 정적 팩토리를 사용하면 클라이언트들이 인스턴스를 공유하여 메모리 사용량과 가비지 컬렉션 비용이 줄어든다.
    - 새로운 클래스를 설계할 때 정적 팩토리를 만들어두면 클라이언트를 수정하지 않고 필요에 따라 캐시 기능을 덧붙일 수 있다.
4. 불변 객체가 공유 가능하다는 점은 방어적 복사가 필요 없어지니, clone 메서드나 복사 생성자를 제공하지 말아야 한다.
    
    → 불변 클래스를 **아무리 복사해봐야 원본과 똑같기 때문에 복사 자체가 의미가 없다.**
    
5. 불변 객체는 자유롭게 공유할 수 있음은 물론, 불변 객체끼리는 내부 데이터를 공유할 수 있다.
    
    ```java
    public class BigInteger extends Number implements Comparable<BigInteger> {
        final int signum; // 값의 부호
        
        final int[] mag; // 값의 크기
        
        ...
        
         public BigInteger negate() {
            return new BigInteger(this.mag, -this.signum);
        }
        ...
    ```
    
    `BigInteger.negate()` 는 크기가 같고 부호만 반대인 새로운 BigInter를 생성하는데, 이때 절댓값인 배열은 비록 가변이지만 복사하지 않고 원본 인스턴스와 공유해도 된다. 따라서 새로 만든 BigInteger 인스턴스도 원본 인스턴스가 가리키는 내부 배열을 그대로 가리킨다.
    
6. 불변 객체는 그 자체로 실패 원자성을 제공한다.
    
    → 상태가 절대 변하지 않으니 불일치 상태에 빠질 가능성이 없다.
    
    <aside>
    ⭐
    
    **실패 원자성**
    
    메서드에서 예외가 발생한 후에도 그 객체는 여전히 유효한 상태여야 함
    
    </aside>
    

## 불변 객체 사용의 단점

1. 값이 다르면 반드시 독립된 새 인스턴스를 만들어주어야 한다.
    - 값의 가짓수가 많으면 비용이 크다.
        
        ex)  BigInteger의 비트 하나를 바꾸는 연산
        
        ```java
        BigInteger moby = ...;
        moby = moby.flipBit(0);  //새로운 BigInteger 인스턴스 생성
        ```
        
    - 해결 : 다단계 연산 또는 가변 동반 클래스
        
        > ⭐ **가변 동반 클래스** <br>
        흔하게 쓰일 다단계 연산을 예측하여 기본 기능으로 제공하는 것. <br>
        `BigInteger`는 지수 연산(비즈니스 로직 연산 등 시간 복잡도가 높은 연산)같은 다단계 연산시 발생하는 불변 클래스의 문제점을 보완하기 위해 다단계 연산들을 묶음으로 제공하는 클래스를 제공한다. <br>
        → 다단계 연산을 기본으로 제공하면 더 이상 각 단계마다 객체를 생성하지 않아도 된다. <br><br>
        ex) 불변클래스인 `String`에게 가변 동반 클래스 `StringBuilder`
        
        

## 최종 정리

- 클래스는 꼭 필요한 경우가 아니면 불변이여야 한다.
    - `getter` 가 있다고 `setter` 를 만들지 말자
    - 단순한 값 객체는 항상 불변으로, `BigInteger`나 `String` 같은 무거운 값 객체도 불변으로 만들거나 성능이슈로 불변으로 만드는것이 불가능하다면 **가변 동반 클래스**를 `public` 클래스로 제공하도록 하자.
- 불변으로 만들 수 없는 클래스라도 변경할 수 있는 부분을 최소한으로 줄이자
    - 객체가 가질 수 있는 상태수를 줄이면 객체를 예측하기 쉬워지고 오류가능성이 줄어든다. 그러니 꼭 변경해야 할 필드를 제외하고 나머지 모든 필드는 `private final` 이어야 한다.
- 생성자는 불변식 설정이 모두 완료된, 초기화가 완벽히 끝난 상태의 객체를 생성해야 한다.
    - 확실한 이유가 없다면 생성자와 정적 팩터리 외에는 그 어떤 메서드도 `public`으로 제공해서는 안된다.(객체 재활용을 목적으로 상태를 다시 초기화하는 메서드도 안된다.)