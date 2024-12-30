## toString 메서드
- 기본 toString 메서드는 의미없는 값을 리턴하므로 
- 따라서 기본 toString 메서드를 '재정의(override)'하여 '간결하면서도 사람이 읽기 쉬운 형태의 유의미한 정보'를 반환하도록 해야 한다!
- 어떤 포맷으로 이러한 문자열로 리턴하는지 Java Docs으로 정리해두는 것이 좋다.(문서화)
- '정적 팩토리 메서드'를 만들면, 사용자가 입력한 문자열을 기반으로 객체를 생성할 수 있다.
  ```
  public final class PhoneNumber {
  
    private final short areaCode, prefix, lineNum;
  
    public PhoneNumber(short areaCode, short prefix, short lineNum) {
        this.areaCode = rangeCheck(areaCode, 999, "지역코드");
        this.prefix = rangeCheck(prefix, 999, "프리픽스);
        this.lineNum = rangeCheck(lineNum, 9999, "가입자 번호");
    }
  
    public static PhoneNumber of(String phoneNumberString) {
  
      String[] split = phoneNumberString.split("-");
      PhoneNumber phoneNumber = new PhoneNumber(
          Short.parseShort(split[0]),
          Short.parseShort(split[1]),
          Short.parseShort(split[2]));
      return phoneNumber;
    }
  }
  ... 

  // Client Code
  PhoneNumber phoneNumber = PhoneNumber.of("010-1234-5678");
  ```
- toString에서 제공하는 정보는 '외부에 제공할 수 있는 공개된 정보'이어야 한다. (객체가 가진 모든 정보 X)
  - ex) 커머스 도메인에서는 '주문 내역', '주문번호'와 같은 주문 관련 민감 정보는 toString 메서드로 인해 외부로 드러나서는 안 된다고 한다.