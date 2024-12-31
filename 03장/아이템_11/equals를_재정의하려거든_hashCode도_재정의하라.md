## 1. HashCode 규약
1. 같은 Object라면 같은 hashCode를 리턴해야 한다.
2. 하지만 다른 Object이더라도 같은 hashCode가 나올 수 있다.
3. equals 메서드에서 사용된 모든 필드들은 hashCode 알고리즘에 사용되어야 한다.
    - 여기에 사용된 필드 값이 변하지 않는다면, 동일한 hashCode 값이 리턴될 것이다.

## 2. 좋은 해시 함수란 
- 서로 다른 인스턴스에 다른 해시코드를 반환하는 해시 함수
- 서로 다른 인스턴스들은 `32비트 정수 범위`에 균일하게 분배한다.
  - 32비트(=4바이트) 정수 범위
    - 메모리에서 데이터를 표현할 때 32개의 비트를 사용하는 정수 형식
    - 32비트 정수로 표현할 수 있는 값의 총 개수 : 2^32 = 4,294,967,296 (약 43억)
    - 범위
      - 부호가 있는 정수(signed integer) : -2,147,483,648 ~ 2,147,483,647
      - 부호가 없는 정수(unsigned integer) : 0 ~ 4,294,967,295
    - 해시 함수에서의 '32비트 정수 범위'
      - 좋은 해시 함수는 서로 다른 입력 값(인스턴스)들이 해시 값을 계산할 때, 가능한 한 고르게 전체 범위에 분포하도록 설계되어야 함!
      - '균일하게 분배한다'는 것은 입력 값들이 서로 달라도 결과로 나오는 해시 값이 32비트 정수 범위 내에서 특정 부분에 치우치지 않고 고르게 분포해야 한다는 의미
      - 해시 함수가 이 범위를 고르게 사용하지 못하면, 특정 값에 해시 값이 몰리게 되어 충돌이 자주 발생할 수 있음!

## 3. Objects 클래스의 hash()
- 임의의 개수만큼 객체를 받아 해시코드를 계산해주는 정적 메서드
- 흔히 사용되는 hashCode 메서드임
  - Intellij > generate > hashCode 선택 시
- 간단하지만 성능은 느림 [이펙티브 자바]
  - 해시 충돌(collision) 발생 시 HashMap 내부에 '연결리스트(Linked List)'로 저장되기 때문!
  - hashMap 조회 속도 : O(1)
  - Linked List 조회 속도 : O(N)
- 성능에 민감하지 않은 상황에서만 사용할 것! [이펙티브 자바]
  - 실무자 관점에서는 실제 application 관점에서 hashMap 병목에 의해 성능이 좌우될 경우는 극히 드물다고 한다
  - `자바 7버전`까지는 해시 충돌 시 중복 key 값 저장을 위해 Linked List만 사용되었는데, `자바 8버전`부터 동일한 버킷에 저장된 엔트리의 개수가 일정 범위(8개) 이상이면 Linked List -> Binary Tree(조회 속도 : O(logN))으로 자료구조를 변환한다


## 4. 해시코드 구현 방법 
1. 필드 하나를 선택해서 그 값에 해당하는 Primitive Type의 hashCode() 메서드로 해시코드를 구한다
2. 두 번째 필드를 선택해서 '그 값에 해당하는 Primitive Type의 hashCode() 메서드로 구한 해시코드 값'과 '1에서 구한 값에 31을 곱한 값'을 더한다.
3. 세 번째 필드를 선택해서 '그 값에 해당하는 Primitive Type의 hashCode() 메서드로 구한 해시코드 값'과 '2에서 구한 값에 31을 곱한 값'을 더한다.
4. 모든 유효한 필드들에 대해서 위의 과정을 반복 수행한다.
    ```java
    // PublicMethods.java
   @Override
    public int hashCode() {
        return System.identityHashCode(name) + // guaranteed interned String
               31 * Arrays.hashCode(ptypes);
    }
   
   // StackTraceElement.java
   public int hashCode() {
        int result = 31*declaringClass.hashCode() + methodName.hashCode();
        result = 31*result + Objects.hashCode(classLoaderName);
        result = 31*result + Objects.hashCode(moduleName);
        result = 31*result + Objects.hashCode(moduleVersion);
        result = 31*result + Objects.hashCode(fileName);
        result = 31*result + lineNumber;
        return result;
    }
    ```

### 31을 사용하는 이유
- '홀수'이기 때문 -> '짝수'는 연산 과정을 반복하다 보면 0이 생기면서 32비트 정수 범위에서 숫자가 한쪽으로 치우칠 경향이 있다.
- 연구자들이 사전에 있는 단어들을 해싱하면서 비교한 연구에서 '31'이 가장 충돌이 적었다는 이야기도 있다.

## 4. 해시코드 사용 시 주의점
1. equals() 메서드에서 사용하는 필드들은 hashCode() 메서드(=해시코드 생성 메서드)에서 임의로 제외시켜선 안 된다.
2. 외부에 hashCode algorithm을 노출시켜서는 안 된다.
   - 외부(클라이언트)에서 hashCode가 어떻게 생성되느냐에 따라서 다르게 동작하는 로직을 작성할 수 있게 허용하면 안 됨

## 5. 실무자가 권장하는 hashCode 사용법
1. Lombok 라이브러리 내의 애노테이션 @EqualsAndHashCode 사용
   - Lombok 라이브러리는 애초에 equals()와 hashCode()가 함께 제공되고 있음 
   - Lombok 개발자들의 테스트가 완료된 hashCode() 메서드이기 때문에 우리가 직접 테스트코드를 작성할 필요가 없다
2. Google의 AutoValue 라이브러리 사용
   - 많이 사용되는 라이브러리는 아님 (덜 권장)