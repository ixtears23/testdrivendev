## TDD(Test Driven Development)
:"프로그램을 작성전 테스트를 먼저 하면 개발하는것"

### 테스트할 대상이 없는데 무엇을 테스틑 하는지?
 - 메서드나 함수같은 프로그램 모듈에 대한 작은 시나리오를 작성하여 그것을 테스트한다
 - 머리속의 그려지는 추상적인 정보를 코드로 미리 구현하여 테스트한다
 - 분석/설계시 작성된 인터페이스를 구현하기 전 테스트 코드로 해당 인터페이스를 테스트 한 후 구현한다.

TDD라고 특별한 테크닉이나 테스트 프레임워크를 쓰는것을 칭하는것이 아니라
테스트 케이스를 작성하며 코드를 구현하는것이 TDD의 시작이다

### TDD의 개발 진행방식
 - 최초의 테스트코드작성
 - 실패(ask) : 테스트 실패를 통해 시스템에 질문
 - 성공(respond) : 테스트 통과를 위한 "가짜 구현"을 통해 질문에 응답
 - 정제(refine) : 중복을 제거하고 모호한것을 명확하게 정제(리펙토링)

### TDD의 목표 3가지
1) 회귀 테스트 : 코드가 제대로 돌아가는지 확인.
2) 샘플(예제) 코드 : 다른 사람에게 API 사용법을 알린다.
3) 인터페이스 설계 : 해당 인터페이스의 입력과 출력이 적절한지, 어ᄄᅠᆫ데이터를 어떤객체에 보내는지 테스트를 통해 검증하며 설계 설계에 대한 피드백 루프를 단축시킨다.

### TDD에 필요한 두가지

1. Junit
: Java단위 테스트 프레임워크, TDD의 근간이 되는 프레임워크이다.

 지원 기능
 - 테스트 결과가 예상과 같은지 판별하는 단정문
 - 여러 테스트에서 공용으로 사용하는 테스트 픽스쳐
 - 테스트 작업을 수행할 수 있게 해주는 테스트 러너

 * 테스트 픽스쳐 : 테스트를 반복적으로 수행할 수 있게 기반이 되는 정보나 Object를 의미
   ex) mock Object, 테스트를 위한 변수 등

2. Mock 객체 : 테스트를 위한 가짜 객체로 실제 객체를 만드는데 소모되는 비용을 절감시키고 의존성등을 감소하기 위해 사용

 Mock객체의 쓰임
 1) 테스트 작성을 위한 환경 구축이 어려운경우
    : 환경 구축을 위한 작업 시간이 많이 소요되는경우 Mock객체를 사용한다
      ex) DB를 설치해야하는 경우, 웹서버, 웹어플리케이션 서버, FTP등의 환경에서 테스트 케이스가 동작하는 경우

 2) 테스트가 의존적인 경우
    : 특정 객체의 의존적으로 동작하는 기능의 개발
      ex) 예외처리 테스트, 로그아웃 처리 등 특정 상황을 테스트마다 발생하도록 할경우 Mock을 이용하여 가상객체를 생성
 3) 테스트 시간이 오래 걸리는 경우
      ex) 네트워크 이슈로 인한 지연, 서버의 성능문제 등 환경으로 인해 지연되는 경우

### Mock객체의 종류 (Test Double)  
 - Ddmmy Object  
 - Test Stub  
 - Test Spy  
 - Mock Object  
 - Fake Object  

1) Test Double : 오리지널 객체를 사용해서 테스트 진행이 어려운 경우 대신하여 테스트를 진행하도록 만들어주는 객체

2) Dummy Object : 단순한 빈껍데기 객체를 위미, 인스턴스화가 가능한 수준으로만 구현한 객체
   ex) 해당 테스트가 미구현된 인터페이스에 의존적인 경우 사용됨

~~~java
public interface User {
  public void login(String userid);
  public void logout(String userid);
}
~~~

~~~java
public class eShop {
  @Test
  public void testAddCoupon() {
    // 구현부는 없지만 인터페이스를 인스턴스화하기 위해 생성
    User user = new User() {
      @Override
      public void login(String userId) {
        // TODO Auto-generated method stub    
      }
      
      @Override
      public void logout(String userId) {
        // TODO Auto-generated method stub
      }
    }
  }
}
~~~

3) Test Stub : 더미 객체가 실제 동작하는 것처럼 만들어 놓은 객체, 특정 값을 리턴하도록 한다.
   ex) 해당 테스트가 특정 값을 리턴받는 경우를 테스트할때
   
~~~java
  public class eshop {
    @Test
    public void testAddCoupon() {
      User user = new User() {
        // 테스트진행을 위해 값을 리턴하게 만든 임시 객체(Test Stub)
        @Override
        public boolean login(String userId) {
          return true;
        }
        
        @Override
        public boolean logout(String userId) {
          return false;
        }
      }
    }
  }
~~~

4) Fake Object : 하나의 인스턴스를 대표하는것이 스텁이면, 여러개의 인스턴스를 대표하는 것이 Fake Object이다
   ex) 내부에 리스트, 맵을 이용하여 DB같은 외부 의존 환경을 대체할때 사용
   
~~~java
public class eshop {

  @Before
  public void selectUserTest() {
    // DB를 대체하기 위해 만든 Fake Object
    List<User> userList = new ArrayList<User>();
  }
  
  @Test
  public void userTest() {
    User user = new User() {
      // 테스트진행을 위해 값을 리턴하게 만든 임시 객체(Test Stub)
      @Override
      public boolean login(String userId) {
         return true;
      }
      
      @Override
      public boolean logout(String userId) {
        return false;
      }
    }
  }
}
~~~

5)　Test Spy : 특정 기능을 수행하거나 정보를 기록하는 객체
   ex) 로그인 여부를 확인하기 위해 임시 기능을 추가함  
   
~~~java
  public class spyUser implements User {
    //호출여부를 확인하기 위한 필드 추가
    private int count;
    
    @Override
    public boolean login(String userId) {
      count++;
      return true;
    }
    
    @Override
    public boolean logout(String userId) {
      return false;
    }
    
    // 테스트를 위해 스펙에 없는 기능을 추가
    public int loginCount() {
      reuturn count;
    }
  }
 
~~~

~~~java

  @Test
  public void userLogOutTest() {
    int loginCnt = ((spyUser) user).loginCount();
    boolean loginValid = false;
    if(loginCnt > 0) {
      loginValid = true;
    }
    assertTrue(loginValid);
  }

~~~
