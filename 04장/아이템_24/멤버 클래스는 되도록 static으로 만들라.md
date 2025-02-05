## 멤버 클래스란?
- 자바에서 클래스는 여러 가지 형태로 정의할 수 있음
- 그중 멤버 클래스(Member Class) 는 다른 클래스 내부에 정의된 클래스를 의미

```java
class OuterClass {
    class MemberClass {  // 멤버 클래스
        // 내부 로직
    }
}
```

- 멤버 클래스는 외부 클래스의 인스턴스와 강하게 연결되며, 외부 클래스의 필드와 메서드에 쉽게 접근할 수 있음

## 정적(static) 멤버 클래스와 비정적 멤버 클래스

- 멤버 클래스는 크게 두 가지로 나뉨

## ✅ (1) 비정적 멤버 클래스 (Non-static Member Class)

- 인스턴스 내부 클래스라고도 함.
- 외부 클래스의 인스턴스에 암묵적인 참조를 가짐.
- 외부 클래스의 인스턴스가 있어야 생성 가능.
- 생성자를 호출할 때 외부 클래스의 참조가 필요함.

```java
class Outer {
    private String message = "Hello";

    class Inner {
        void printMessage() {
            System.out.println(message); // 외부 클래스의 필드 접근 가능
        }
    }
}

public class Main {
    public static void main(String[] args) {
        Outer outer = new Outer();
        Outer.Inner inner = outer.new Inner(); // 외부 클래스의 인스턴스 필요
        inner.printMessage();
    }
}
```

🔴 문제점:

- 내부 클래스가 외부 클래스를 참조하기 때문에 메모리 누수(메모리 릭) 발생 가능.
- 예기치 않은 동작을 초래할 수 있음.

## ✅ (2) 정적(static) 멤버 클래스 (Static Member Class)

- static 키워드가 붙은 멤버 클래스.
- 외부 클래스의 인스턴스와 독립적.
- 외부 클래스의 인스턴스 없이도 생성 가능.
- 외부 클래스의 필드에 직접 접근할 수 없음.

```java
class Outer {
    private static String message = "Hello";

    static class StaticInner {
        void printMessage() {
            System.out.println(message); // 외부 클래스의 정적 필드만 접근 가능
        }
    }
}

public class Main {
    public static void main(String[] args) {
        Outer.StaticInner inner = new Outer.StaticInner(); // 외부 클래스 인스턴스 불필요
        inner.printMessage();
    }
}
```

### ✅ 장점:

- 외부 클래스의 참조를 가지지 않아 메모리 누수 방지.
- 클래스 간 결합도가 낮아 유지보수가 쉬움.
- 독립적인 클래스처럼 사용 가능.

### 왜 멤버 클래스는 되도록 static으로 만들어야 할까?

⚠️ 비정적 멤버 클래스의 문제점
- 외부 클래스의 참조를 내부적으로 유지하므로 메모리 누수 발생 가능.
- 예상치 못한 동작을 초래할 수 있음.
- 불필요하게 외부 클래스와 강하게 연결됨 (캡슐화 저해).

✅ static 멤버 클래스를 권장하는 이유
- 불필요한 외부 클래스 참조를 제거하여 메모리 누수를 방지.
- 독립적인 클래스처럼 동작하여 유지보수 용이.
- 의도적으로 외부 클래스의 인스턴스가 필요할 때만 비정적 멤버 클래스를 사용.

## 언제 비정적 멤버 클래스를 사용할까?
- 비정적 멤버 클래스는 다음과 같은 경우에만 사용해야 하
- 외부 클래스의 인스턴스에 대한 참조가 반드시 필요할 때
- 외부 클래스의 필드나 메서드를 직접 사용해야 할 때
- 외부 클래스의 논리를 구성하는 작은 부분일 때

## 정리

- 멤버 클래스 종류	외부 클래스의 인스턴스 필요 여부	외부 클래스 참조 여부	권장 여부
- 비정적 멤버 클래스	필요함	참조함 (메모리 누수 위험)	❌ 되도록 피할 것
- 정적 멤버 클래스	불필요	참조 없음 (메모리 안전)	✅ 권장

✅ 멤버 클래스는 되도록 static으로 만들자.
✅ 외부 클래스의 인스턴스를 참조해야 할 때만 비정적 멤버 클래스를 사용하자.
