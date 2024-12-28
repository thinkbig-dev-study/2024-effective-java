
### 단순히 정적 메서드와 정적 필드만을 담은 클래스가 때때로 유용하게 쓰일 수 있다.
EX) `java.lang.Math`, `java.util.Arrays`

`java.util.Arrays`의 코드를 살펴보면 아래와 같다.

```java
public class Arrays {
    public static boolean isArray(Object o) { ...}

    public static Object[] asObjectArray(Object array) { ...}

    public static List<Object> asList(Object array) { ...}

    public static <T> boolean isNullOrEmpty(T[] array) { ...}

    ...
    // 이 생성자에 주목
    private Arrays() {
    }
}
```
우리는 Array를 사용할때 Arrays arrays = new Arrays()로 생성하지않는다.   
내부 메소드를 보면 전부 static으로 선언되어 있고 생성자가 private으로 선언되어 있다.

#### private 생성자를 명시한 이유 ?
1. 생성자를 명시하지 않으면 컴파일러가 자동으로 public 기본 생성자를 만든다.
2. 사용자는 이 생성자가 자동 생성인지 구분할 수 없다.
3. public 생성자가 있으면 의도치 않게 인스턴스화 할 수 있다.
4. private 생성자를 추가하면 클래스의 인스턴스화를 막을 수 있다.

#### 추상클래스로 만들면 ? (abstract 클래스는 인스턴스화가 불가능)
1. 추상 클래스로 만드는 것으로는 인스턴스화를 막을 수 없다.
2. 하위 클래스를 만들어 인스턴스화 하면 된다.
3. 이것을 본 사용자는 상속해서 쓰라는 뜻으로 오해할 수 있다. (019)

