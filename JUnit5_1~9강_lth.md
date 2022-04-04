# JUnit5

### 자바 개발자가 가장 많이 사용하는 테스팅 프레임워크

- Java 8 이상 필요.
- 대체제 : TestNG, Spock 등

# 1. 구조

![image](https://user-images.githubusercontent.com/33479944/161467368-7bbe0d36-c9fc-49be-bd54-7359c3df95a5.png)


- Platform : 테스트를 실행해주는 런처. TestEngineAPI 제공
- Jupiter : TestEngine API구현체로 JUnit5 제공.
    - 주로 Jupiter를 사용함.
    - import
- Vintage : JUnit4와 3을 지원하는 TestEngine 구현체.

### sample

```java
class AppTest{
	@Test
	void create(){
		App app = new App();
		assertNotNull(app);
	}
}
```

# 2. JUnit5 시작하기

- 스프링 부트 프로젝트로 테스트
- 사용환경
    - IDE - sts4.0.12
    - SpringBoot 2.6.4 gradle war
    - Java 8

## 2.1. Spring-boot-starter-test

- 스프링부트 2.2.x 이후부터 기본적으로 의존성 추가되는것으로 JUnit5를 기본 제공한다.
- JUnit4에 대한 호환성을 제거하고 싶으면 `exclude - org.junit.vintage:junit-vintage-engine` 모듈을 제거한다.
    - maven
        
       ![image](https://user-images.githubusercontent.com/33479944/161467410-7bc37043-b450-4ab4-b6b2-0e2b4eff2d91.png)
        
    - gradle
        
       ![image](https://user-images.githubusercontent.com/33479944/161467437-730e3399-f368-4bd9-aed1-64ec916b9b22.png)
        
- 2.2.x 이전의 경우 JUnit4가 기본 테스트 프레임워크 였음.
- `spring-boot-starter-test` 모듈 주입후 JUnit4 모듈은 exclude로 제거한다.
    - JUnit5를 사용하려면 junit-jupiter 모듈을 의존하면 된다.
        
        ![image](https://user-images.githubusercontent.com/33479944/161467467-fd8ff8ca-0c74-46f2-b462-1f4c24220369.png)
        

## 2.2 STS에서 Test 시작

- JUnit5 부터는 클래스나 테스트 메서드에 굳이 public을 안써도 된다.
    - JUnit4는 클래스나 테스트 메서드에 public을 사용했어야 했다.
    - Java Reflection을 사용하면 private, default에 접근 가능하다.
- 해당 프로젝트의 Properties에서 JUnit5를 추가한다.
    - add Libray → JUnit → JUnit5
    
    ![image](https://user-images.githubusercontent.com/33479944/161467510-c38e16ab-71a3-4100-9f9a-bc9db64494a5.png)
    
- 테스트 할 클래스에서 우클릭 → New - Others → JUnit Test Case 생성
    
    ![image](https://user-images.githubusercontent.com/33479944/161467540-f9bd5acd-53be-45ca-b82d-897c9ad31eb6.png)
    
- 테스트 파일 생성 후 테스트 진행
    
    ![image](https://user-images.githubusercontent.com/33479944/161467604-3b873f03-b694-4cb8-b6ab-3ed62f8e1daf.png)
    
- 스프링 부트로 만들지 않은 경우 의존성을 추가해준다.
    
   ![image](https://user-images.githubusercontent.com/33479944/161467643-10c90e90-508d-4d57-b395-501405e64f46.png)
    

## 2.3 기본 어노테이션

- JUnit4기반 테스트도 JUnit5로 충분히 돌아감.
- @Test
    - 메서드 위에 선언시 해당 메서드는 테스트 대상 메서드
    - @Test(timeout = n)
        - 테스트 결과 반환을 n m/s를 넘기면 테스트 실패(단위 m/s)
    - @Test(expected=RuntimeException.class)
        - RuntimeException이 발생해야 테스트 성공
- @BeforeAll / @AfterAll
    - 모든 테스트 클래스에서 실행전/후 1번만 호출됨.
        - BeforeAll : 테스트 실행전 1번만 호출
        - AfterAll: 테스트 실행 후 1번만 호출
    - 반드시 static으로 선언해야되며 리턴 타입이 있으면 안됨.
    - private은 안되나 default는 접근가능.
    - JUnit4에서 @BeforeClass / @AfterClass와 매칭
- @BeforeEach / @AfterEach
    - 모든 테스트 실행할 때 테스트 실행 이전과 이후에 실행됨.
    - static일 필요 없음.
    - JUnit4에서 @Before / @After와 매칭
- @Disabled
    - 해당 테스트 실행 안함.
    - JUnit4에서 @Ignore와 매칭
    
    ```java
    package com.study.JUnit5;
    
    import static org.junit.jupiter.api.Assertions.*;
    
    import org.junit.jupiter.api.*;
    
    class StudyTest {
    
    	@Test
    	void create() {
    		Study study = new Study();
    		assertNotNull(study);
    		System.out.println("create");
    	}
    	
    	@Test
    	@Disabled
    	void create1() {
    		System.out.println("create1");
    	}
    	
    	@Test
    	void create2() {
    		System.out.println("create2");
    	}
    	
    	@BeforeAll
    	static void befoeAll() {
    		System.out.println("beforeAll");
    	}
    	
    	@AfterAll
    	static void afterAll() {
    		System.out.println("afterAll");
    	}
    	
    	@BeforeEach
    	void beforeEach() {
    		System.out.println("beforeEach");
    	}
    	
    	@AfterEach
    	void afterEach() {
    		System.out.println("afterEach");
    	}
    	
    	
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/33479944/161467687-bb7f87a6-eceb-4f24-bd56-bb0802fb6107.png)
    

# 3. JUnit5: 테스트 이름 표기하기

@DisplayNameGeneration

- Method와 Class 레퍼런스를 사용해서 테스트 이름을 표기하는 방법 설정
- 기본 구현체로 ReplaceUnderscores 제공
- 클래스에 적용하면 클래스 내 모든 메서드가 적용됨.
- @DisplayNameGeneration(DisplayNameGenerator.ReplaceUnderscores.class) :  메서드명 _(UnderScore)를 공백으로 처리
- @DisplayNameGeneration(DisplayNameGenerator.Standard.class)
: 메서드명을 그대로 출력 → Default
    
   ![image](https://user-images.githubusercontent.com/33479944/161467734-ecd4e58f-95ea-4dd8-b138-918a11055f42.png)
    
    ![image](https://user-images.githubusercontent.com/33479944/161467772-f3015cf8-4b4b-4aa5-b5db-b7ddf6843f78.png)
    

@DisplayName

- 어떤 테스트인지 테스트 이름을 보다 쉽게 표현할 수 있는 방법을 제공하는 어노테이션.
- @DisplayNameGeneration 보다 우선 순위가 높다
    
    ![image](https://user-images.githubusercontent.com/33479944/161467799-fa95b889-9aa2-42dd-87bb-46be57f93643.png)
    
    - 테스트명에 공백, 한글, 이모지 가능.

참고 : [https://junit.org/junit5/docs/current/user-guide/#writing-tests-display-names](https://junit.org/junit5/docs/current/user-guide/#writing-tests-display-names)

# 4. JUnit5 : Assertion

### 4.1 JUnit 기본 메서드

- assertArrayEquals(a,b)
    - 배열 a와 b가 일치함을 확인.
- assertEquals(a,b)
    - 객체 a(기대값)와 b(실제값)의 값이 같은지 확인
    - 순서가 바뀌어도 상관없다.
- assertSame(a,b)
    - 객체 a와 b가 같은 객체임을 확인.
- assertTrue(a)
    - a가 true인지 false인지 확인
- assertNotNull(a)
    - a객체가 null인지 확인
- assertALL(executables...)
    - executables의 모든 확인 구문 확인
- asserThrows(expectedType, executables)
    - 예외 발생 확인
- assertTimeout(duration, executable)
    - 특정시간 안에 실행이 완료되는지 확인.
- [AssertJ](https://joel-costigliola.github.io/assertj/), [Hemcrest](https://hamcrest.org/JavaHamcrest/), [Truth](https://truth.dev/) 등의 라이브러리를 사용할 수도 있다.

```java
public class Study {

	private StudyStatus status = StudyStatus.DRAFT;

	private int limit;
	
	public Object getStatus() {
		// TODO Auto-generated method stub
		return this.status;
	}

}
```

```java
public enum StudyStatus {
	DRAFT,STARTED, ENDED
}
```

```java
import static org.junit.jupiter.api.Assertions.*;

import org.junit.jupiter.api.*;

class StudyTest2 {

	@Test
	@DisplayName("JUnit5 스터디")
	void create_new_study() {
		Study study = new Study(10);
		// 모든 확인구문
		assertAll(
		// 람다식으로 작성시 실패시에만 동작.
				() -> assertNotNull(study),
				() -> assertEquals(StudyStatus.DRAFT, study.getStatus(),
						() -> "스터디 처음 생성시 상태값이 " + StudyStatus.DRAFT + "상태다."),
				() -> assertTrue(study.getLimit() > 0, "스터디 최대 참석 가능인원 0보다 크다")
		);
		
		// 예외 확인
		IllegalArgumentException exception = assertThrows(IllegalArgumentException.class, () -> new Study(-10));
		String message = exception.getMessage();
		//assertEquals("limit은 0보다 커야함", message);
		assertEquals("limit은 0보다 커야함", exception.getMessage());

		// 특정시간 실행완료 확인
		assertTimeout(Duration.ofMillis(100), () -> {
			new Study(10);
			Thread.sleep(300);
		});

		// 테스트가 실행됬을때 성공/실패든 무조건 실행
		// assertEquals(StudyStatus.DRAFT, study.getStatus(),"스터디 처음 생성시 상태값이 DRAFT가
		// 되어야한다.");
		// 람다식으로도 가능.
		// 람다식으로 작성시 실패시에만 동작.
//		assertEquals(StudyStatus.DRAFT, study.getStatus()
//				, () -> "스터디 처음 생성시 상태값이 "+ StudyStatus.DRAFT+"상태다.");
		// assertTrue(1<2);
		// assertTrue(study.getLimit() > 0, "스터디 최대 참석 가능인원 0보다 크다");

	}
}
```

# 5. JUnit5 : 조건에 따라 테스트 실행하기.

특정 조건을 만족하는 경우 테스트 실행방법

테스트를 위해서 시스템 환경 변수 편집→ 환경변수 → 시스템 변수에 새로만들기로 생성한다.(ex TEST_ENV : rrakoon)

- Assumptions : 가정 클래스
    - 테스트를 계속 실행할지 or 추가 실행을 건널뛸지 결정할 수 있는 메서드포함
    - import static org.junit.jupiter.api.Assumptions.*;
- assumeTure(조건)
    - assumption 조건이 false인 경우 다음 코드라인을 실행하지 않는다.
        
        ![image](https://user-images.githubusercontent.com/33479944/161467951-513ebc47-4d7e-47b4-9a31-58747dff04d9.png)
        
        ![image](https://user-images.githubusercontent.com/33479944/161467995-21f4bd5b-3257-4730-b9e9-3aee29aaa386.png)
        
        ![image](https://user-images.githubusercontent.com/33479944/161468023-893382fe-c489-4045-aac8-297c3ac16bae.png)
        
        ![image](https://user-images.githubusercontent.com/33479944/161468049-1382b352-e5a4-43d1-a8a2-7c9b2dcba9b5.png)
        
- assumingThat(조건, 테스트)
    - 조건에 true인 경우 executable의 람다 형식의 인스턴스 실행
    - equalsIgnoreCase는 대소문자를 구분하지 않고 같은지 비교한다.
        
        ![image](https://user-images.githubusercontent.com/33479944/161468082-8bd8898f-49ee-412b-9bbe-51b00cd3ed36.png)
        
        ![image](https://user-images.githubusercontent.com/33479944/161468105-dd25c55c-0a0d-41dd-bef2-e640d9f24eee.png)
        
- @Enabled / @Disabled
    - OnOs
        - 특정 OS에서만 실행/비실행
            
            ![image](https://user-images.githubusercontent.com/33479944/161468132-f53ca62c-d0ff-47d6-af9c-5e92f0835039.png)
            
    - OnJre
        - 특정 자바 버전에서만 실행/비실행
            
            ![image](https://user-images.githubusercontent.com/33479944/161468180-8953d536-635d-444c-8d0a-d795c9459e7d.png)
            
- IfEnvironmentVariable(name=대상값, matches = 비교값)
    - 위의 assertTrue를 어노테이션으로 사용.
        
        ![image](https://user-images.githubusercontent.com/33479944/161468220-2a4ea5f8-ac7b-4c7e-b0cf-0a0d8d3e43f6.png)
        
- If / IfSystemProperty 도 있지만 따로 테스트하진 않았다.

# 6. JUnit5 : 태깅과 필터링

### 테스트 그룹을 만들고 원하는 테스트 그룹만 실행할 수 있는 기능.

@Tag

- 테스트 메서드를 추가하고 하나의 테스트 메서드에 여러 태그 사용 가능하다.

### 이클립스 Gradle에서 테스트 필터링

- 특정 테스트만 테스트 하고싶은 경우 gradle에서 설정한다.
    
    ![image](https://user-images.githubusercontent.com/33479944/161468258-fcfde5fa-3003-4aec-9d14-f9ce67b06e17.png)
    
    - includeTags :  @Tag(태그명)이 포함된것은 실행한다.
    - excludeTags : @Tag(태그명)이 포함된것은 실행하지 않는다.
    
    ![image](https://user-images.githubusercontent.com/33479944/161468299-35e39807-58ec-422d-a68c-791f4ce61778.png)
    

### 참고 : [https://binux.tistory.com/131](https://binux.tistory.com/131)

- 변경시 됬다 안됬다하는게 이해가 안감.

# 7. JUnit5 : 커스텀 태그

- JUnit5가 제공하는 어노테이션은 메타 어노테이션으로 사용가능.
    - 메타 어노테이션 : 다른 어노테이션에서도 사용되는 어노테이션의 경우를 말하며 커스텀 어노테이션을 생성할때 주로 사용됨.
- @Retention
    - 어노테이션의 라이플 사이클이 언제까지 살아남을지 결정.
    - @Retention(RetentionPolicy.x)
        - RetentionPolicy.SOURCE : 소스 코드(.java)까지 남아있는다.
        - RetentionPolicy.CLASS : 클래스 파일(.class)까지 남아있는다.(=바이트 코드)
        - RetentionPolicy.RUNTIME : 런타임까지 남아있는다.(=사실상 안 사라진다.
        
        ![image](https://user-images.githubusercontent.com/33479944/161468337-668c0744-3c9e-477f-a975-1e6ecd5dd50e.png)
        
- @Target
    - 자바 컴파일러가 어노테이션이 어디까지 적용될지 결정하기 위해 사용.
    - @Target(ElementType.x)
        
        ```
        ElementType.PACKAGE : 패키지 선언
        ElementType.TYPE : 타입 선언
        ElementType.ANNOTATION_TYPE : 어노테이션 타입 선언
        ElementType.CONSTRUCTOR : 생성자 선언
        ElementType.FIELD : 멤버 변수 선언
        ElementType.LOCAL_VARIABLE : 지역 변수 선언
        ElementType.METHOD : 메서드 선언
        ElementType.PARAMETER : 전달인자 선언
        ElementType.TYPE_PARAMETER : 전달인자 타입 선언
        ElementType.TYPE_USE : 타입 선언
        ```
        
- @Tag
    - 테스트에 어떤 테스트인지 표기와 동시에 필터링하는것.
- 커스텀 어노테이션 생성
    
    ```java
    import java.lang.annotation.ElementType;
    import java.lang.annotation.Retention;
    import java.lang.annotation.RetentionPolicy;
    import java.lang.annotation.Target;
    
    import org.junit.jupiter.api.Tag;
    import org.junit.jupiter.api.Test;
    
    @Target(ElementType.METHOD)
    @Retention(RetentionPolicy.RUNTIME)
    @Test
    @Tag("fast")
    public @interface FastTest {
    
    }
    ```
    
    ```java
    @Test
    @DisplayName("스터디 만들기 fast")
    @Tag("fast")
    void create_new_study(){
    	Study actual = new Study(100);
    	assertThat(actual.getLimit()).isGreaterThan(0);
    }
    
    @FastTest  //@Tag("fast") 생략
    @DisplayName("스터디 만들기 fast")
    void create_new_study(){
    	Study actual = new Study(100);
    	assertThat(actual.getLimit()).isGreaterThan(0);
    }
    ```
    
    - 위처럼 커스텀으로 태그 생성 가능.

# 8. JUnit5 : 테스트 반복하기 1

@RepeatedTest

- 반복 횟수와 반복 테스트 이름 설정가능
    
    ![image](https://user-images.githubusercontent.com/33479944/161468375-79468878-3424-49fa-8c56-5ee6a21d7832.png)
    
- RepetitionInfo타입의 인자를 통해 현재 반복 횟수, 전체 반복횟수를 확인가능.
    
    ![image](https://user-images.githubusercontent.com/33479944/161468409-86f6bef1-6dd1-4d94-b4c2-0113c3068887.png)
    
    - {displayName} : @DisplayName의 이름을 가져온다
    - {currentRepetition} : 현재 테스트 반복횟수를 가져온다.
    - {totalRepetitions} : 전체 반복 횟수를 가져온다.
    
    ![image](https://user-images.githubusercontent.com/33479944/161468432-a90ef7cf-88aa-4d8d-825a-fdb0e90f78d3.png)
    

@ParameterizedTest

- 반복적인 테스트마다 다른 파라미터를 대입해서 실행한다.
- JUnit5에는 기본 내장이고 JUnit4에서는 서드파티 라이브러리 사용해야함.
    - {displayName} :  @DisplayName의 이름을 가져온다
    - {index} : 해당 인덱스 번호
    - {arguments} : ??
    - {0},{1},{2}.... : ??
    
    ![image](https://user-images.githubusercontent.com/33479944/161468454-b9884756-5d2a-4bef-9265-df379aff9e87.png)
    

# 9. JUnit5 : 테스트 반복하기 2

인자값 소스

- @ValueSource
    - 어노테이션에 지정한 배열을 파라미터값으로 순서대로 넘김.
    - 테스트 메서드 실행당 하나의 인수만 전달할 때 사용가능.
    - 리터럴값의 배열을 테스트 메서드에 전달가능.
        - short, byte, int, long, float, double, char, String, class
- @NullSource
    - 기본형 타입에 null을 허용할수 없으므로 기본형 타입에는 @NullSource는 사용 불가능.
- @EmptySource
    - 빈값을 인수에 전달가능.
    - String인수의 경우 “”, 배열에도 빈값을 넣을 수 있음.
- @NullAndEmptySource
    - null과 빈값 모두 전달할 수 있어 범용으로 사용.

### 위 3인자값 소스는 모두 @ParameterizedTest와 함께 결합 사용가능.

- @EnumSource
    - 열거형 값의 배열을 테스트메서드에 전달가능.
    - 테스트 메서드 실행당 하나의 인수만 전달할 때 사용.
    - names속성으로 리터럴 문자열 외 정규표현식을 전달 가능하고 기본적으로 names속성은 일치하는 enum값을 갖는다.
    - mode 속성은 EXCLUDE로 설정하면 제외하는 enum값을 가질 수 있다.
        
        ```java
        @DisplayName("Enum테스트")
        	@ParameterizedTest
        	@EnumSource(Month.class) // passing all 12 months
        	void getValueForAMonth_IsAlwaysBetweenOneAndTwelve(Month month) {
        		int monthNumber = month.getValue();
        		assertTrue(monthNumber >= 1 && monthNumber <= 12);
        	}
        
        	@DisplayName("Enum테스트1")
        	@ParameterizedTest
        	@EnumSource(value = Month.class, names = { "FEBRUARY", "APRIL", "JUNE", "SEPTEMBER", "NOVEMBER" })
        	void someMonths_Are30DaysLong(Month month) {
        		final boolean isALeapYear = false;
        		assertEquals(30, month.length(isALeapYear));
        	}
        
        	@ParameterizedTest
        	@EnumSource(value = Month.class, names = {"APRIL", "JUNE", "SEPTEMBER", "NOVEMBER"}, mode = EnumSource.Mode.EXCLUDE)
        	void exceptFourMonths_OthersAre31DaysLong(Month month) {
        		final boolean isALeapYear = false;
        		assertEquals(31, month.length(isALeapYear));
        	}
        ```
        
- @MethodSource
    - 테스트 메서드의  실행 당 복잡한 인수를 전달할 때 사용.
    - @MethodSource에 설정하는 이름은 존재하는 메서드 이름이여야 한다.
        - 메서드명 설정하지 않으면 JUnit은 테스트 메서드와 이름이 같은 소스 메서드를 찾음.
    - argument source method 조건
        - Stream<Arguments> (Iterable, Iterator) or List와 같은 컬렉션과 유사한 인터페이스를 반환 가능하다.
        - @MethodSource는 다른 테스트 클래스간 인수를 공유하는것이 가능하다.
    
    ```java
    @ParameterizedTest
    	@MethodSource("provideStringsForIsBlank") // needs to match an existing method.
    	void isBlank_ShouldReturnTrueForNullOrBlankStrings(String input, boolean expected) {
    		assertEquals(expected, Strings.isBlank(input));
    	}
    
    	// a static method that returns a Stream of Arguments
    	private static Stream<Arguments> provideStringsForIsBlank() { // argument source method
    		return Stream.of(
    				Arguments.of(null, true)
    				, Arguments.of("", true)
    				, Arguments.of("  ", true)
    				, Arguments.of("not blank", false));
    	}
    ```
    
    ```java
    //StudyTest9강.java
    @ParameterizedTest
    	 @MethodSource("com.study.JUnit5.StringParams#blankStrings") // 클래스 외부의 source method
         void isBlank_ShouldReturnTrueForNullOrBlankStringsExternalSource(String input) {
             assertTrue(Strings.isBlank(input));
         }
    ```
    
    [https://gmlwjd9405.github.io/2019/11/27/junit5-guide-parameterized-test.html](https://gmlwjd9405.github.io/2019/11/27/junit5-guide-parameterized-test.html)
    
    ```java
    //src/main/java
    package com.study.JUnit5;
    
    import java.util.stream.Stream;
    
    public class StringParams {
    	static Stream<String> blankStrings() {
    		return Stream.of(null, "", "  ");
    	}
    }
    ```
    
- 그 외 @CvsFileSource, @ArgumentSource 가 있다.
- 인자값 타입 변환
    - 암묵적 타입변환
        - [레퍼런스](https://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests-argument-conversion-implicit) 참고
    - 명시적 타입변환
        - SimpleArgumentConverter 상속 받은 구현체 제공
        - @ConvertWith
        
        ```java
        //명시적 타입 변환
        	//하나의 Argument만 변환 static or public
        	static class StudyConverter extends SimpleArgumentConverter{
        
        		@Override
        		protected Object convert(Object source, Class<?> targetType) throws ArgumentConversionException {
        			// TODO Auto-generated method stub
        			assertEquals(Study.class, targetType, "Study클래스만 컨버트 가능");
        			
        			return new Study(Integer.parseInt(source.toString()));
        		}
        		
        	}
        	
        	@DisplayName("스터디 테스트3")
        	@ParameterizedTest(name = "{index} : {displayName} message={0}")
        	@ValueSource(ints = { 100,90,50 })
        	void repeatTest3(@ConvertWith(StudyConverter.class) Study study) {
        		System.out.println(study.getLimit());
        	}
        ```
        
- @CvsSource
    - CSV (Comma Seperated Value) 형식의 데이터로 반복 테스트를 반복
        
        ![image](https://user-images.githubusercontent.com/33479944/161468522-2a91d27d-3b98-4dae-b4e6-9b0f4ffca2b1.png)
        
    - 문자열은 " " (double quote), 문자열 안에 항목 구분하기 위해선 ' ' (single quote) 사용
    - '' 로 빈 값을 표현하면 "" 과 동일, '' 도 사용하지 않으면 null로 처리
    
    ![image](https://user-images.githubusercontent.com/33479944/161468599-58599966-56c5-4f0d-968e-e00e9875cfba.png)
    
    ```java
    @DisplayName("스터디 테스트4")
    	@ParameterizedTest(name = "{index} : {displayName} message={0}")
    	@CsvSource({"100, 'JUnit테스트'", "200, '스프링'"})
    	void repeatTest4(int limit, String name) {
    		Study study = new Study(limit, name);
    		System.out.println(study);
    	}
    	
    	@DisplayName("스터디 테스트5")
    	@ParameterizedTest(name = "{index} : {displayName} message={0}")
    	@CsvSource({"100, 'JUnit테스트'", "200, '스프링'"})
    	void repeatTest5(ArgumentsAccessor argumentsAccessor) {
    		Study study = new Study(argumentsAccessor.getInteger(0), argumentsAccessor.getString(1));
    		System.out.println(study);
    	}
    ```
    
- 인자값 조합
    - ArgumentsAccessor
    - 커스텀 Accessor
        - ArgumentsAccessor 인터페이스 구현
        - @AggregatorWith
    
    ```java
    @DisplayName("스터디 테스트5")
    	@ParameterizedTest(name = "{index} : {displayName} message={0}")
    	@CsvSource({"100, 'JUnit테스트'", "200, '스프링'"})
    	void repeatTest5(ArgumentsAccessor argumentsAccessor) {
    		Study study = new Study(argumentsAccessor.getInteger(0), argumentsAccessor.getString(1));
    		System.out.println(study);
    	}
    	
    	//인자값 조합 static or public
    	static class StudyAggregator implements ArgumentsAggregator{
    
    		@Override
    		public Object aggregateArguments(ArgumentsAccessor accessor, ParameterContext context)
    				throws ArgumentsAggregationException {
    			return new Study(accessor.getInteger(0), accessor.getString(1));
    		}
    		
    	}
    	
    	@DisplayName("스터디 테스트6")
    	@ParameterizedTest(name = "{index} : {displayName} message={0}")
    	@CsvSource({"100, 'JUnit테스트'", "200, '스프링'"})
    	void repeatTest6(@AggregateWith(StudyAggregator.class) Study study) {
    		System.out.println(study);
    	}
    ```
    
    - [https://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests](https://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests)