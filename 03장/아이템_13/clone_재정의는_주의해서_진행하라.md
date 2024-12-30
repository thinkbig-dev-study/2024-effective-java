### 정리1 - 애매모호한 clone 규약

1. clone은 반드시 원본과는 다른 인스턴스여야 한다.   
   `x.clone() != x 반드시 true`
2. clone을 했던 클래스와 원본 클래스가 동일해야 한다.   
   `x.clone().getClass() == x.getClass() 반드시 true`
3. equals를 통해 비교했을 때 같을 수도 있고, 다를 수도 있다.   
   `x.clone().equals(x) true가 아닐 수도 있다.`

```JAVA
// PhoneNumber에 clone 메서드 추가 (79쪽)
public final class PhoneNumber implements Cloneable {
    private final short areaCode, prefix, lineNum;

    public PhoneNumber(int areaCode, int prefix, int lineNum) {
        this.areaCode = rangeCheck(areaCode, 999, "지역코드");
        this.prefix = rangeCheck(prefix, 999, "프리픽스");
        this.lineNum = rangeCheck(lineNum, 9999, "가입자 번호");
        System.out.println("constructor is called");
    }

    public PhoneNumber(PhoneNumber phoneNumber) {
        this(phoneNumber.areaCode, phoneNumber.prefix, phoneNumber.lineNum);
    }
    
    // 원래의 clone 메서드
    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }

    // 코드 13-1 가변 상태를 참조하지 않는 클래스용 clone 메서드 (79쪽)
    @Override
    public PhoneNumber clone() {
        try {
            return (PhoneNumber) super.clone();
        } catch (CloneNotSupportedException e) {
            throw new AssertionError();  // 일어날 수 없는 일이다.
        }
    }

    public static void main(String[] args) {
        PhoneNumber pn = new PhoneNumber(707, 867, 5309);
        Map<PhoneNumber, String> m = new HashMap<>();
        m.put(pn, "제니");
        PhoneNumber clone = pn.clone();
        System.out.println(m.get(clone));

        System.out.println(clone != pn); // 반드시 true
        System.out.println(clone.getClass() == pn.getClass()); // 반드시 true
        System.out.println(clone.equals(pn)); // true가 아닐 수도 있다.
    }
}
```
가변 상태를 참조하지 않는 클래스용 clone 시 주의할 점
1. 반드시 cloneable 인터페이스를 구현해야 한다.
2. clone 안에서 super.clone()을 사용해야 한다.
---
### 정리2 - 가변 객체 clone 정의하는 방법
```JAVA
/**
 * // 잘못된 clone() 메서드
 * TODO hasTable -> entryH[],
 * TODO copy -> entryC[]
 * TODO entryH[0] == entryC[0]
 */
@Override
public HashTable clone() {
    HashTable result = null;
    try {
        result = (HashTable)super.clone();
        result.buckets = this.buckets.clone(); // p82, shallow copy 라서 위험하다.
        return result;
    } catch (CloneNotSupportedException e) {
        throw  new AssertionError();
    }
}

/**
 * // clone() 메서드 수정
 * TODO hasTable -> entryH[],
 * TODO copy -> entryC[]
 * TODO entryH[0] != entryC[0]
 */
@Override
public HashTable clone() {
    HashTable result = null;
    try {
        result = (HashTable)super.clone();
        result.buckets = new Entry[this.buckets.length];
        for (int i = 0 ; i < this.buckets.length; i++) {
            if (buckets[i] != null) {
                result.buckets[i] = this.buckets[i].deepCopy(); // p83, deep copy
            }
        }
        return result;
    } catch (CloneNotSupportedException e) {
        throw  new AssertionError();
    }

    // 재귀적 deep copy
    public Entry deepCopy() {
        return new Entry(key, value, next == null ? null : next.deepCopy());
    }

    // 반복적 deep copy
    public Entry deepCopy() {
        Entry result = new Entry(key, value, next);
        for (Entry p = result ; p.next != null ; p = p.next) {
            p.next = new Entry(p.next.key, p.next.value, p.next.next);
        }
        return result;
    }
}
```
- 잘못된 clone() 메서드는 가변 상태를 공유한다. (shallow copy)
- 재귀적 deep copy와 반복적 deep copy를 사용해 해결 가능하다.

---
### 정리3 - clone 대안
일반적으로 상속용 클래스에는 Cloneable 인터페이스 사용을 권장하지 않는다.   
해당 클래스를 확장하려는 프로그래머에게 많은 부담을 주기 때문이다.
1. 부담을 덜어주기 위해 기본 clone() 메서드를 제공하거나,
2. 하위 클래스에서 구현하지 못하도록 protected final를 통해 재정의를 막을 수 있다.
3. 고수준 API(get, put) 사용을 통해 entity를 채워줄 수 있다.

#### 보통 clone을 잘 사용하지 않고 생성자나 팩토리메서드를 통해 카피한다 ...
```JAVA
public class HashSetExample {
    public static void main(String[] args) {
        Set<String> hashSet = new HashSet<>();
        hashSet.add("woongjin");
        hashSet.add("thinkbig");
        System.out.println("HashSet: " + hashSet);

        Set<String> treeSet = new TreeSet<>(hashSet);
        System.out.println("TreeSet: " + treeSet);
    }
}
```