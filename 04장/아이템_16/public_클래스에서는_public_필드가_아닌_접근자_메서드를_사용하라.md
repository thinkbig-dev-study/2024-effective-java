## 1. 퇴보한 public 클래스
- 인스턴스 필드를 모아 놓는 것 외에 아무 기능도 하지 않는 클래스
- 코드 예시
  ```java
  class Point {
        public double x;
        public double y;  
  }
  ```
- 문제1 : 캡슐화의 이점이 없다.
  - 직접 필드 접근이 가능함
- 문제2 : API를 수정해야만 내부 표현을 바꿀 수 있다.
- 문제3 : 불변식을 보장할 수 없다.
- 문제4 : 외부에서 필드에 접근할 때 부수 작업을 수행할 수 없다.

## 2. 캡슐화된 public 클래스
- 모든 필드는 private이며, public 접근자(=getter)가 있는 클래스
- 코드 예시
  ```java
  class Point {
        private double x;
        private double y;
  
        public Point(double x, double y) {
            this.x = x;
            this.y = y;  
        }
        
        public double getX() { return x; }
        public double getY() { return y; }
  
        public void setX(double x) { this.x = x; }
        public void setY(double y) { this.y = y; }
  }
  ```
- 패키지 바깥에서 접근할 수 있는 클래스라면 접근자(=getter)를 제공해야 한다.
  - '클래스 내부 표현 방식'을 언제든 바꿀 수 있는 '유연성'을 얻을 수 있다! (= 메서드 내 부가적인 작업 수행할 수 있다)
  - 만약 public 필드가 외부에 공개되면 이를 직접 사용하는 클라이언트가 생기게 되고, 그 결과 클래스 설계자 입장에서 '클래스 내부 표현 방식'을 마음대로 설정하거나 바꿀 수 없는 상황이 되는 것!

- 캡슐화의 장점
  - 개발 속도 높아짐
  - 관리 비용 낮춤
  - 성능 최적화에 도움
  - 소프트웨어 재사용성을 높임
  - 시스템 개발 난이도를 낮춤

## 3. package-private 클래스 / private 중첨 클래스
- package-private 클래스, private 중첩 클래스는 데이터 필드를 public으로 노출해도 큰 문제가 없다.
### 3-1. package-private 클래스
- 접근 제한자를 명시하지 않은 클래스
- 동일한 패키지 내부에서만 접근 가능
- 특징
  - 접근 제한자를 명시하지 않으면 'default 접근 수준(=패키지 수준 접근)'을 가짐
  - 동일한 패키지에 속한 클래스만 이 클래스 사용 가능
  - 패키지 외부에서는 보이지 않음
- 코드 예시
  ```java
  // 파일: packageexample/MyPackageClass.java
  package packageexample;
    
  class MyPackageClass { // 접근 제한자 생략 -> "package-private"
      void displayMessage() {
               System.out.println("This is a package-private class.");
      }
  }
  ```
  ```java
  // 파일 : packageexample/Main.java
    package packageexample;
    
    public class Main {
        public static void main(String[] args) {
            MyPackageClass obj = new MyPackageClass(); // 동일한 패키지에서만 접근 가능
            obj.displayMessage();
        }
    }
  ```
  ```java
    // 파일 : anotherpackage/AnotherClass.java
    package anotherpackage;
    
    import packageexample.MyPackageClass; // [Error] package-private은 다른 패키지에서 접근 불가!
    
    public class AnotherClass {
        public static void main(String[] args) {
            MyPackageClass obj = new MyPackageClass(); // 접근 불가
        }
    }
  ```
- package-private의 경우 굳이 private 필드를 사용하지 않아도 된다.
- package-private의 의미 : 
  - "우리가 직접 정의한 패키지 내에서만 사용한다" (클래스의 사용 범위)
  - "우리 회사/우리 팀/나만 사용하는 코드라는 의미"
  - 그나마 해당 클래스의 변경에 따른 사이드이펙트가 적음
  - 그럼에도 필드에 접근할 때는 '직접 접근'보다는 '메서드 접근'이 훨씬 더 안정적인 방법임!
- '직접 접근'보다 '메서드 접근'이 훨씬 더 안정적인 이유 :
  - 만약 필드 이름이 변경되는 경우, 메서드는 유지한 채 필드 이름을 우선적으로 바꾸고 나중에 추가적인 메서드를 구현해서 메서드를 점진적으로 교체하는 안정적인 교체가 가능함
  - 만약 필드 직접 접근을 한다면, 이러한 점진적인 교체(=유연한 변화)는 불가능함!
### 3-2. private 중첩 클래스
- 클래스 안에 중첩된 클래스에서 'private' 접근 제한자를 사용할 경우, 해당 외부 클래스(outer class)의 내부에서만 사용이 가능함
- 코드 예시
    ```java
     package item16;
    
    public class OuterClass {
    
        private static class PrivateNestedInnerClass {
            void displayMessage() {
                System.out.println("This is private nested class.");
            }
        }
    
        // *외부 클래스의 내부에서만 사용 가능*
        public void useNestedClass() {
            PrivateNestedInnerClass privateNestedClass = new PrivateNestedInnerClass();
            privateNestedClass.displayMessage();
        }
    
    }
    ```
    ```java
    package item16;
    
    public class Main {
        public static void main(String[] args) {
            // [1] Outer Class를 통해서 private nested class에 접근하는 것은 가능!
            OuterClass outerClass = new OuterClass();
            outerClass.useNestedClass();
    
            // [Error] : Outer 클래스 이외의 클래스에서 직접 private nested class를 생성 및 호출하는 것은 불가능
            OuterClass.PrivateNestedInnerClass nested = new OuterClass.PrivateNestedInnerClass();
        }
    }
    ```
  
## 4. 자바 라이브러리 중 public 클래스 필드를 직접 노출한 사례
- 잘못된 예시 : `java.awt.Point`, `java.awt.Dimension`
- 책 내용 중 "특히 Dimension 클래스의 성능 문제는 여전히 심각하다."

### 4-1. java.awt.Dimension 클래스
- public 클래스임에도 불구하고 `width`와 `height`가 public으로 선언됨
- 코드 예시
  ```java
    
  ```