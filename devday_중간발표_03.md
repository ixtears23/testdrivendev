
---

# Hamcrest

- 유연한 표현을 만들기 위해 결합 할 수있는 Matchers  

Java에서 태어난 Hamcrest는 현재 여러 언어로 구현되어 있습니다.  

- Java  
- Python  
- Ruby  
- Objective-C  
- PHP  
- Erlang  
- Swift  


## Getting Started

### 소개

Hamcrest은 일치 규칙을 선언적으로 정의 할 수 있도록 정규 일치 개체를 작성하기위한 프레임 워크입니다.  
UI 유효성 검사 또는 데이터 필터링과 같이 matchers가 사용할 수없는 상황이 많이 있지만 matcher는 가장 일반적으로 사용되는 **유연한 테스트를 작성** 하는 영역에 있습니다.  
이 튜토리얼에서는 **단위 테스트를 위해 Hamcrest를 사용하는 방법** 을 보여줍니다.  

### 의존성 추가

~~~xml
<dependency>
    <groupId>org.hamcrest</groupId>
    <artifactId>hamcrest</artifactId>
    <version>2.1</version>
    <scope>test</scope>
</dependency>
~~~


#### first Hamcrest test

`JUnit`의 `assertEquals` 메소드를 사용하는 대신, 우리는 **정적으로 가져 오는 Hamcrest** 의 **`assertThat` 구문** 과 **표준 `matcher` 세트** 를 사용합니다.  


~~~java
import org.junit.jupiter.api.Test;
import static org.hamcrest.MatcherAssert.assertThat;
import static org.hamcrest.Matchers.*;

public class BiscuitTest {
  @Test
  public void testEquals() {
    Biscuit theBiscuit = new Biscuit("Ginger");
    Biscuit myBiscuit = new Biscuit("Ginger");
    assertThat(theBiscuit, equalTo(myBiscuit));
  }
}
~~~


`assertThat` 메서드는 테스트 `assertion`을 만들기 위한 양식화 된 문장입니다.  
이 예에서 `assertion`의 주체는 첫 번째 메서드 파라미터인 Biscuit 객체 입니다.  
2번째의 메소드 파라미터는, Biscuit 객체의 매처입니다.  

이 테스트는 Biscuit 클래스가 equals 메서드를 정의하기 때문에 성공합니다.  

테스트에서 하나 이상의 `assertion`이 있는 경우 테스트 된 값에 대한 식별자를 `assertion` 포함 할 수 있습니다.  
~~~java
assertThat("chocolate chips", theBiscuit.getChocolateChipCount(), equalTo(10));

assertThat("hazelnuts", theBiscuit.getHazelnutCount(), equalTo(3));
~~~

#### 기타 테스트 프레임 워크

Hamcrest는 처음부터 다른 단위 테스트 프레임 워크와 통합되도록 설계되었습니다.  
예를 들어, Hamcrest는 JUnit (모든 버전) 및 TestNG와 함께 사용할 수 있습니다.  
(자세한 내용은 전체 Hamcrest 배포와 함께 제공되는 예제를 참조하십시오.)  
다른 테스트 스타일이 Hamcrest와 공존 할 수 있기 때문에 기존 테스트 스위트에서 Hamcrest 스타일의 어설 션을 사용하는 것으로 마이그레이션하는 것은 쉽습니다.  

#### 일반적인 matchers 둘러보기

- Core  
  - anything : 항상 일치. 테스트중인 객체가 무엇인지 신경 쓰지 않으면 유용  
  - describedAs : 사용자 지정 실패 오류 추가  
  - is - 가독성을 높이기위한 데코레이터  
- Logical  
  - allOf : 모두 일치하면(예 : Java &&)    
  - anyOf : 하나라도 일치하면    
  - not : 일치하지 않으면  
- Object  
  - equalTo : Object.equals를 사용해 객체의 동등성을 테스트  
  - hasToString : Object.toString를 테스트  
  - instanceOf, isCompatibleType : 테스트 유형  
  - notNullValue, nullValue : null 테스트  
  - sameInstance : 객체 식별 테스트  
- Beans  
  - hasProperty : JavaBeans 속성 테스트  
- Collections  
  - array - 배열 요소르르 테스트  
  - hasEntry, hasKey, hasValue - map 을 테스트  
  - hasItem, hasItems - 컬렉션에 요소가 포함되어 있는지 테스트  
  - hasItemInArray - 배열에 요소가 들어 있는지 테스트  
- Number  
  - closeTo : 생략  
  - greaterThan, greaterThanOrEqualTo, lessThan, lessThanOrEqualTo : 생략  
- Text
  - equalToIgnoringCase : 대소 문자를 무시한 문자열 동등성 테스트  
  - equalToIgnoringWhiteSpace : 공백의 실행 차이를 무시한 문자열 동등성 테스트
  - containsString, endsWith, startsWith : 문자열 일치 테스트  

- 유쾌한  
`Hamcrest`는 **가능한 한 읽을 수있는 테스트를하기 위해 노력** 합니다.  
다음은 모두 동일합니다.  

~~~java
assertThat(theBiscuit, equalTo(myBiscuit));
assertThat(theBiscuit, is(equalTo(myBiscuit)));
assertThat(theBiscuit, is(myBiscuit));
~~~

마지막 형식은 `(T value)`가 `(equalTo (value))`를 반환하도록 오버로드 되어 있기 때문에 허용됩니다.  

#### 커스텀 matchers 작성하기

Hamcrest는 많은 유용한 matchers와 함께 번들로 제공되지만, 테스트 요구 사항에 맞추기 위해 사용자마다 자신 만의 템플릿을 만들어야 할 필요가 있을 것입니다.  
이것은 일반적으로 동일한 속성 집합을 여러 번 테스트 (그리고 다른 테스트에서)하는 코드 단편을 발견하고 단편을 단일 어설 션으로 묶으려는 경우에 주로 발생합니다.   
독자적인 정규 표현식을 작성함으로써 코드 중복을 제거하고 테스트를 보다 쉽게 ​​읽을 수 있습니다!  

`double` 값의 값이 `NaN`(숫자가 아님) 인 경우 테스트를 위해 자체 matcher를 작성해 보겠습니다. 이것은 우리가 작성하고자하는 테스트입니다.  

~~~java
@Test
public void testSquareRootOfMinusOneIsNotANumber() {
  assertThat(Math.sqrt(-1), is(notANumber()));
}
~~~

그리고 구현은 다음과 같습니다.  

~~~java
package org.hamcrest.examples.tutorial;

import org.hamcrest.Description;
import org.hamcrest.Matcher;
import org.hamcrest.TypeSafeMatcher;

public class IsNotANumber extends TypeSafeMatcher {

  @Override
  public boolean matchesSafely(Double number) {
    return number.isNaN();
  }

  public void describeTo(Description description) {
    description.appendText("not a number");
  }

  public static Matcher notANumber() {
    return new IsNotANumber();
  }

}
~~~

`assertThat` 메소드는, `assertion`의 주제의 형태에 의해 파라미터화 된 `Matcher`를 취하는 범용 메소드입니다.
우리는 `Double` 값에 대해 주장하고 있으므로 `Matcher<Double>`이 필요하다는 것을 알고 있습니다.
`Matcher` 구현의 경우 `TypeSafeMatcher`를 서브 클래싱하는 것이 가장 편리합니다. `TypeSafeMatcher`는 `Double`을 캐스팅합니다.  
- matchesSafely 메소드 만 구현하면됩니다.  
- Double이 NaN인지 단순히 확인합니다.  
- describeTo 메소드 : 테스트가 실패 할 때 실패 메시지를 생성하는 데 사용됩니다.  

다음은 실패 메시지가 표시되는 방법의 예입니다.  

~~~java
assertThat(1.0, is(notANumber()));

// fails with the message

java.lang.AssertionError: Expected: is not a number got : <1.0>
~~~

우리의 `matcher`에서 세 번째 메소드는 편리한 factory 메소드입니다.  
테스트에서 정규식을 사용하기 위해 이 메소드를 정적으로 가져옵니다.  

~~~java
import org.junit.jupiter.api.Test;
import static org.hamcrest.MatcherAssert.assertThat;
import static org.hamcrest.Matchers.*;
import static org.hamcrest.examples.tutorial.IsNotANumber.notANumber;

public class NumberTest {
  @Test
  public void testSquareRootOfMinusOneIsNotANumber() {
    assertThat(Math.sqrt(-1), is(notANumber()));
  }
}
~~~

비록 notANumber 메서드가 호출 될 때마다 새로운 매처를 생성하지만,
이것이 매처의 유일한 사용 패턴이라고 가정해서는 안됩니다.  
따라서 매처의 상태를 확인해야 하므로 매처간에 단일 인스턴스를 다시 사용할 수 있습니다.  

---


## Mockito Test 에서 @(Annotation) 활용하는 방법

~~~java
@RunWith(MockitoJUnitRunner.class)
public class MockitoAnnotationTest {
    ...
}
~~~


~~~java
@Before
public void init() {
    MockitoAnnotations.initMocks(this);
}
~~~

## @Mock Annotation

~~~java
@Test
public void whenNotUseMockAnnotation_thenCorrect() {
    List mockList = Mockito.mock(ArrayList.class);

    mockList.add("one");
    Mockito.verify(mockList).add("one");
    assertEquals(0, mockList.size());

    Mockito.when(mockList.size()).thenReturn(100);
    assertEquals(100, mockList.size());
}
~~~

~~~java
@Mock
List<String> mockedList;

@Test
public void whenUseMockAnnotation_thenMockIsInjected() {
    mockedList.add("one");
    Mockito.verify(mockedList).add("one");
    assertEquals(0, mockedList.size());

    Mockito.when(mockedList.size()).thenReturn(100);
    assertEquals(100, mockedList.size());
}
~~~

## @InjectMocks Annotation

쉽게 말하면 테스트 하려는 클래스의 참조변수들을 mock으로 주입(초기화) 해야 하는 경우

테스트 클래스에서 `@Mock` 애너테이션을 달고 있는 필드들(Mock으로 생성되는 객체들) 을

실제 product 클래스(테스트 클래스에 @InjectMocks 애너테이션을 달고 있는 필드)의 필드에 Mock 객체로 주입해 준다.  


---


## Mockito의 InjectMocks..

`InjectMocks`은 생성자, 설정자(setter), 필드 순서로 찾아서 주입을 하게 되어 있다.  
만약 생성자가 존재하고, 생성자에는 내부에 선언된 필드의 초기화를 수행하지 않는 경우
null 에러 발생 주입은 실패한다.  

Mockito javadoc에 InjectMocks이 주입하는 방법에 대해 나와있다.  

또 하나의 문제는 만약 성공하던 테스트에 생성자를 새롭게 추가하게 된다면
어느 날 갑자기 테스트는 실패하게 될 것이다.  


---

## About DbUnit

DbUnit은 데이터베이스 실행 프로젝트를 대상으로하는 JUnit 확장 (Ant와 함께 사용할 수도 있음)입니다.  
이 프로젝트는 테스트 실행 사이에 데이터베이스를 알려진 상태로 만듭니다.  

이것은 하나의 테스트 케이스가  
데이터베이스를 손상시키고  
후속 테스트가 실패하거나  
손상을 심하게 할 때  
발생할 수 있는 무수한 문제를 피하는 훌륭한 방법입니다.  


DbUnit은 데이터베이스 데이터를 `XML` 데이터 세트로 내보내거나 가져올 수 있습니다.  
버전 2.0부터 DbUnit은 스트리밍 모드에서 사용될 때 매우 큰 데이터 세트로 작업 할 수 있습니다.  
또한 DbUnit을 사용하면 데이터베이스 데이터가 예상 값 집합과 일치하는지 확인할 수 있습니다.  


## Database Testing
데이터베이스 유닛 테스트는 DbUnit 프레임 워크의 실현에 영감을주었습니다.  

## 문제는 이것입니다  
SQL 데이터베이스, 일부 저장 프로 시저 및 응용 프로그램과 데이터베이스 사이에 코드 계층이 있습니다.  
***어떻게하면 코드가 실제로 데이터베이스에서 올바른 데이터를 읽고 쓰는지 테스트를 할 수 있습니까?***  

데이터베이스를 설정하고, 데이터베이스에 액세스하기위한 코드를 작성하고, 코드를 실행하고, SELECT를 수행하여 레코드가 데이터베이스에 나타나는지 확인하십시오.  


### Maven

~~~xml
<dependency>
    <groupId>org.dbunit</groupId>
    <artifactId>dbunit</artifactId>
    <version>2.6.0</version>
    <scope>test</scope>
</dependency>
~~~


먼저 데이터베이스 테이블을 나타내는 XML 파일을 만들어야합니다.  


#### user.xml

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<dataset>
    <USER userid='1' first_name='Vinod' last_name='Kashyap' />

    <USER userid='2' first_name='Dhwani' last_name='Kashyap' />

    <USER userid='3' first_name='Asmi' last_name='Kashyap' />
</dataset>
~~~

- table: USER  
- column: userid, first_name, last_name  

`user.xml` 파일 위치는 **루트 경로** 에 둡니다.  


~~~java
package junitdbunit;

import static org.hamcrest.CoreMatchers.is;
import static org.junit.Assert.assertThat;

import java.io.FileInputStream;

import org.dbunit.DBTestCase;
import org.dbunit.PropertiesBasedJdbcDatabaseTester;
import org.dbunit.dataset.IDataSet;
import org.dbunit.dataset.xml.FlatXmlDataSetBuilder;
import org.dbunit.operation.DatabaseOperation;
import org.junit.Test;

public class DbUnit extends DBTestCase {

		// database 연결 초기화
    public DbUnit(String name) {
        super(name);
        System.setProperty(PropertiesBasedJdbcDatabaseTester.DBUNIT_DRIVER_CLASS, "com.mysql.jdbc.Driver");
        System.setProperty(PropertiesBasedJdbcDatabaseTester.DBUNIT_CONNECTION_URL, "jdbc:mysql://localhost:3306/user");
        System.setProperty(PropertiesBasedJdbcDatabaseTester.DBUNIT_USERNAME, "root");
        System.setProperty(PropertiesBasedJdbcDatabaseTester.DBUNIT_PASSWORD, "");
    }

 		/*  
				XML 파일을 읽고 데이터베이스에 필수 항목을 작성합니다.
				여기에서는 IDataSet의 FlatXmlDataSet을 사용하고 있습니다.
		*/
    protected IDataSet getDataSet() throws Exception {
        return new FlatXmlDataSetBuilder().build(new FileInputStream("user.xml"));
    }

		// 테스트하기 전에 데이터베이스를 초기 위치로 설정합니다.
    protected DatabaseOperation getSetUpOperation() throws Exception {
        return DatabaseOperation.REFRESH;
    }

 		// 테스트 케이스 실행 후 모든 entry가 지워집니다.
    protected DatabaseOperation getTearDownOperation() throws Exception {
        return DatabaseOperation.NONE;
    }

    @Test
    public void testById() {

        int userId = 5;// get user id from database
        assertThat(1, is(userId));
    }
}
~~~


### Best Practices

#### 개발자 당 하나의 데이터베이스 인스턴스 사용
동일한 프로젝트에서 작업하는 여러 개발자가 데이터 손상을 방지하기 위해 자체 데이터베이스 인스턴스를 가져야합니다.  

#### 좋은 설정은 정리가 필요하지 않습니다.  
앞선 테스트의 결과에 따라 테스트를 작성하지 않아야 합니다.  
(순서에 관계없이 테스트는 성공해야 합니다.)  

#### 여러 개의 작은 데이터 세트 사용
전체 데이터베이스 데이터를 하나의 큰 데이터 세트에 넣는 대신 여러 개의 작은 덩어리로 나누십시오.  

#### 전체 테스트 클래스 또는 테스트 슈트에 대해 한 번 부실 데이터 설치를 수행하십시오.  
여러 테스트에서 동일한 읽기 전용 데이터를 사용하는 경우 이 데이터를 전체 테스트 클래스 또는 테스트 슈트에 대해 한 번 초기화 할 수 있습니다.  

---

### TDD(Test Driven Development)

> TDD를 학습하고 적용하면서 하나씩 질문을 던져가며 진행 했습니다.
> 아래 내용은 블로깅과 책 그리고 얼마 되지 않지만 경험을 바탕으로 작성 했습니다.

## 테스트 더블이란 무엇인가?
테스트 더블이란 테스트를 하기 위해 실제 객체를 사용하지 않고, 실제 객체와 비슷한 Stub 또는 모의(mock) 객체를 나타내는 말입니다.

## Stub 이란 무엇인가?
메서드의 서명부와 (반환값이 있을 경우) 반환 명령만 적는 식으로 해서, 이 메서드를 호출하는 코드가 컴파일 될 수 있도록 껍데기만 만들어두는 것을 뜻합니다.

## Mock이란?
Mock 이란 번역을 하게 되면 모의, 모조품이라는 뜻입니다.
단위 테스트가 아닌 것 - 예를 들면 하나의 메서드를 테스트할 때 메서드에 DB를 호출하는 로직이 들어 있는 경우 DB에 접근하는 객체를 Mock으로 만들지 않고 실제 DB에 접근하면서 테스트 하는 것. 이런 것은 단위 테스트가 아닙니다.
Mock 은 단위테스를 할 때 실제 DB에 접근하지 않고도 해당 메서드를 테스트하게 해줍니다.

## Given When Then 템플릿이란 무엇인가?
Given 상황이 주어졌을 때
When 무언가 행동을 취했을 때
Then 이런 결과를 얻게 된다.
단위테스트 작성 경험 후 느낀 점
Given 테스트를 위한 선 작업, @Before 애너테이션에 해당하는 setUp메서드에 테스트를 위한 값, 객체 초기화
When 과 Then 은 @Test 애너테이션 메서드에 속합니다.
When -> 실제 프로덕트 코드를 호출합니다.
Then 프로젝트 코드를 호출한 결과 값과 기대한 값(Expected)을 검증(확인) 합니다. assertThat 절.

## 그와 함게 AAA 라는 패턴 또한 접하게 됩니다.
몇 개의 블로그를 봤지만 .NET 예제가 많았습니다. 조금은 코드에 집중해야 할 때라고 생각됩니다.

## BDD?
TDD를 이야기하면 BDD 이야기가 종종 나옵니다.
궁금하지만 아직은 TDD에 집중해야 할 때인 것 같습니다.

## field주입보다는 생성자 주입을 사용해야 한다?
작년 devday 스프링에서 의존성주입을 설명할 때의 내용과 이어집니다.
작년 발표내용 :생성자 주입, setter 주입, 필드 주입. 스프링 팀에서는 생성자 주입을 옹호합니다..

많은 사람들이 필드 주입은 코드의 간결함을 제외하고는 아무런 이점이 없다고 말하고 있습니다.
하지만 아직도 필드 주입을 선호하고 필드 주입이 문제되지 않는 다고 말하는 사람 또한 존재 합니다.
조금 더 경험해 봐야 겠습니다.  


## hamcrest가 아닌 AssertJ를 사용하게 된 이유?
assertJ를 사용할 것인가 hamcrest를 사용할 것인가에 대한 고민.
다양한 matcher를 제공하는 hamcrest와 assertJ.
TDD 학습 중 hamcrest를 적용했지만 어떤 matcher가 있는지 IDE(개발툴)의 도움을 받기 쉽지 않았습니다.
asserj 는 외우지 않아도 코드어시스트를 이용해서 Matcher를 암기하지 않아도 사용 가능합니다.
hamcrest를 사용하다가 얼마전(6월) assertj를 이용해서 단언(assertThat)을 작성하기 시작합니다.

많은 블로그를 보더라도 assertJ는 초보자가 쓰기에 유용하다라는 말과 라이브러리가 상시적으로 update 됩니다. 물론 hamcrest도 그렇지만요.

아직 사용빈도를 보면 hamcrest를 더 많이 사용하고 있지만, 기존에 쓰던 matcher 라이브러리를 바꿀 필요가 있나에 대한 이야기가 있었습니다. 
그리고 현재는 많은 사용자가 assertJ를 사용하고 있습니다.  

## 코드 커버리지(Code Coverage)는 얼마나 충분해야 할까?  
조금 더 경험 해봐야겠지만 대부분의 사람들은 100% 코드 커버리지를 만족하지 않아도 된다고 말합니다.
모든 건 사람을 위한 것이라고 생각됩니다. 코드를 작성하는 것도 사람. 테스트를 하는 것도 사람.
안정감을 찾기 위해. 현재로서는 그 말이 정답으로 보여 집니다.

## private 메서드는 어떻게 테스트 할거야?
private 메서드를 단위테스트 하는 경우 누구는 필요하다. 누구는 잘못된 설계다 라는 글들을 봤습니다.
더 많은 사람들이 private이 아닌 private을 호출하는 public 인터페이스 메서드를 단위테스트 해야 한다고 합니다.

private 메서드에 대한 확실한 답을 찾기 위해서는 조금 더 찾아보고 여러 가지 방법으로 테스트 해봐야 할 것 같습니다.

우선 해봐야 알 수 있기 때문에 private 메서드를 java reflection을 이용해서 테스트 했습니다.

요약..
~~~java
private Method addErrorAmtAndPurchsAmt;
TmprBudgServiceImpl.class.getDeclaredMethod("getUpdatePurchsAmountInBudgetDe", List.class,List.class );
multiplyPercentAndChangedAmount.invoke(tmprBudgService, "8000", "1.250");
~~~

## 실제 지금까지의 경험을 스토리로 한번 작성해 봤습니다.  

TDD의 시작..

되도록이면 매주 목요일 모임을 가졌습니다.  
저희 팀은 운이 좋게도 모두 같은 사이트에 근무하고 있기 때문에 가능했다고 보여 집니다.  

모임의 주제는 TDD만 하자는 아니였습니다.  
TDD를 공부하다가 TDD에 나오는 용어 중 어떻게 보면 TDD가 아닌 내용이더라도.  
각자 1주일동안 무언가 학습한 걸 무조건 서로가 공유하는 시간을 갖기로 했습니다.  

그래서 서로가 모르는 새롭게 알게된 지식들에 대해서 공유할 수 있었습니다.  

아래는 실제 TDD에 접근한 방식으로 작성했습니다.  

### 1. Spring MVC 컨트롤러 테스트 해보기  
`@Runwith` 애너테이션으로 시작했습니다.
> 간단한 프로젝트 만들어서 mockMvc 스프링 컨트롤러 테스트 진행  
 - 미디어 타입, json, pathvariable, view, parameter 테스트.  

### 2. Spring에서 제공하는 애너테이션으로 이미 구현된 표준연 코드 단위테스트 해보기  
컨트롤러는 어떻게 테스트 하는지 대충 알았으니 이제는  
> 표준연 프로젝트 안에 있는 컨트롤러 메서드 하나를 목표로 삼았습니다.
> `@Runwith` 로 테스트 클래스를 만들고 서비스를 호출하는데 오류 발생.  
> 스프링 구성 테스트 패키지로 하나씩 가져오면서 테스트 실행. 변경된 오류 메시지의 반복.  
 - jndi 오류, datasoucre 오류, 계층구조 오류발생, 테스트 패키지 설정 경로 오류, properties 오류 등등  
 - `@ContextHierarchy`, `@ContextConfiguration` 계층 구조 설정 등..  
 - 성공 표준연 프로젝트의 컨트롤러 메서드에 접근하고 떨어지는 녹색불.  
※ 완벽하진 않지만 어쨌든 프로덕션 코드를 테스트 했습니다. 그런데.. 이제서야 알고보니 단위테스트가 아닌 통합테스트 였습니다.  

### 3. DB 테스트 라이브러리 사용(DB Unit)
우선 스프링 관련 테스트를 해봤으니 DB테스트가 궁금해졌습니다.  
토비의 스프링, 구글링 등을 통해 DBUnit을 알게 되어 DBUnit으로 xml 파일에 해당하는 테이블 컬럼 값을 명시해서  
xml을 읽어들여 DB에 insert 까지 성공시켰습니다.  
spring db unit 라이브러리를 쓰면 애너테이션을 써서 조금더 가독성 있게 코드를 작성할 수 있었는데  
표준연 프로젝트에 적용하다 보니 krisstar, epurchase, groupware  등 스키마 지정을 할 수 없어서  
DB unit 라이브러리를 사용했습니다. spring db unit 라이브러리는 몇 년 전 더 이상 update되지 않아서  
사용하지 않기로 했습니다.  
표준연은 mybatis를 사용합니다. 아직 mapper를 실제 테스트하진 못했습니다.  
테스트 방법은 사전에 필요한 데이터 설정 트랜잭션이 성공한 뒤 기대하는 결과 값을 dbunit으로 확인 할 수 있다고 합니다.   
이보다 먼저 서비스에 있는 로직을 테스트 할 수 있어야 된다는 생각이 들었습니다.  
DB 테스트는 잠시 멈추기로 했습니다.  

### 4. TDD 실패  
TDD를 해볼 수 있는 요구사항이 나왔습니다.  
이참에 실제 요구사항대로 TDD로 코드를 작성하기로 했습니다.  
TDD는 프로덕션 코드 보다 테스트 코드를 먼저 작성해야 한다라는 말대로 무조건 테스트 코드 먼저 작성 했습니다.  
실패 했습니다. 막상 해보니 단위테스트가 뭔지도 모르겠습니다.  
기간내에 끝내기 위해서는 TDD는 다음번으로 미뤄야 겠습니다.  

### 5. 개인 연습 및 단위 테스트와 통합 테스트  
개인연습을 통해서 테스트 코드를 작성했습니다.  
단위가 뭘까에 대한 고민을 많이 하고 구글링도 많이 했습니다.  
단위란 무언가. 단위란 통합이란. 사람들이 말하는 건 다 달랐습니다. 나름대로의 커트라인을 정하게 될 수 있었습니다. 
전체 통합 테스트 – 컨트롤러를 호출하면, 해당 서비스, DB 까지 모두 실행한다.
통합 테스트 – DB에 직접 붙는다. API 연계 테스트 등.
조금 큰 단위 테스트 – 단위테스트의 묶음. (서비스 테스트인 경우는 DB에 붙지 않는다.)
단위 테스트 – 단 하나의 행위만 하고 단하나의 결과만을 테스트
	    - mapper만 테스트
	    - 컨트롤러만 테스트
	    - 서비스의 메서드만 테스트  

### 6. 프로덕션 코드에 다시 TDD 도전
개발이 완료되고 추가 요구사항이 나왔습니다.  
추가 요구사항이 그렇게 크지 않습니다. 다시 한번 프로덕션 코드 작성에 TDD를 적용 해봐야 겠습니다.  
단위가 무엇인지 알고나니 무언가 시작할 수 있게 됐습니다.  

### 7. TDD 성공
완벽한 best practice에 가까운 TDD, 테스트 코드를 작성했다고 할 순 없지만. 
처음으로 프로덕션 코드를 TDD로 완성시켰습니다.  


## 지금까지 학습하면서의 에러사항
스스로 고민하고 질문을 던졌을 때 답을 찾기 위해서 구글링을 하게 되면 같은 문제에도
서로 경험한 부분이 다르기 때문인지 정확한 답이 없어 보입니다.  
이럴 때 방향 결정을 하기가 쉽지 않은 것 같습니다.  
결국엔 운 좋게 올바른 방향을 택했다던가 아니면 올바르지 않은 방향을 택하고 실제 코드작성 후에
잘못됐다는 걸 알게 되는 것 같습니다.  
조언자가 없어서 더 오래 가게 되는 것 같습니다.  


## 깨달음
단위테스트를 작성하려면, 단위가 무엇인가, 내가 정말 테스트하고자 하는게 무엇인가 알아야 합니다.
우선 가장 작은 단위로 만드는게 가장 중요합니다. 테스트 하면서 정말 말도 안되는 작은 단위로 만들고 나니
이정도의 단위로 까지는 만들지 않아도 되겠구나. 좋은지 안좋은지 가장 작게 나누고 나서 알게 됐습니다.
해보지 않고 말로만 들었을 때는 어떻게 하지란 생각 뿐이었습니다.

하지만 아직 갈 길이 멀어 보입니다. 

## 목표
devday가 끝나기 전 까지 best practice 에 가까운 단위테스트를 작성할 수 있어야 합니다.
특별히 개발 방식에 대한 제약이 없다면 항상 TDD로 개발 해야 합니다.  

## 희망사항
누군가 TDD를 어떻게 해야 되냐고 물어 봤을 때 그 사람을 TDD의 길로 이끌 수 있을 정도가 됐으면 좋겠습니다.  
가능하다면 모두가 통합테스트까지 작성할 수 있으면 좋겠습니다.  


### 기타

검색한 방식
- spring mvc test
- unit test in java
- unit test
- how to private method test in java
- what is stub
- how to unit test
- how to unit test in java
- what is unit test
- what is unit test in java
- test sample in java
- unit test sample
- unit test sample in java
- hamcrest vs assertJ
- why mockito?  
.  
.  
.  


생각날 때 적어둔 참고 url
- Spring Test
https://docs.spring.io/spring/docs/current/spring-framework-reference/testing.html#testing

- Spring mvc sample 
https://github.com/spring-projects/spring-mvc-showcase

- Spring 단위 테스트
https://reflectoring.io/spring-boot-test/

- hamcrest
https://www.vogella.com/tutorials/Hamcrest/article.html

- Mockito javadoc
https://static.javadoc.io/org.mockito/mockito-core/2.28.2/org/mockito/Mockito.html

- Mockito
https://site.mockito.org/

- assertJ
https://joel-costigliola.github.io/assertj/

- assertJ matcher javadoc
http://joel-costigliola.github.io/assertj/core-8/api/org/assertj/core/api/AbstractAssert.html

- db Unit sample
https://examples.javacodegeeks.com/core-java/junit/junit-dbunit-example/

- 마틴파울러 – Given When Then
https://martinfowler.com/bliki/GivenWhenThen.html#footnote-example-credit

- 마틴파울러 – Mock은 Stub이 아니다
https://martinfowler.com/articles/mocksArentStubs.html#UsingEasymock

- 테스트더블이란
https://testing.googleblog.com/2013/07/testing-on-toilet-know-your-test-doubles.html?m=1

- 필드 주입에 대한 이견
https://lkrnac.net/blog/2014/02/promoting-constructor-field-injection/  
https://www.petrikainulainen.net/software-development/design/why-i-changed-my-mind-about-field-injection/

- 마이크로소프트 [유닛테스트 best practice]
https://docs.microsoft.com/ko-kr/dotnet/core/testing/unit-testing-best-practices
