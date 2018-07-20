## 유닛 테스트의 구성요소

### 요구사항 구성요소

Java 프로그램 유닛 테스트는 일반적으로 다음 6개의 요구사항을 각각 독립적으로 만족
해야 하므로, 각 요구사항 속성에  따라(R1-R6)를 구별할 수 있다.

- R1: 사용자가 지시한 테스트 케이스가 실행되어야 함
- R2: 상이한 환경에서도 동일한 테스트 코드는 동일한 테스트 케이스를 실행해야 함
- R3: 유닛 테스트 코드의 실행은 상호 간 영향을 받지 않아야 함
- R4: 실행 결과가 올바르지 않은 경우, 테스트 실패(test fail)로 관찰되어야 함
- R5: 테스트 실행 결과가 올바른 경우, 테스트 성공(test pass)로 관찰되어야 함
- R6: 테스트 결과가 사용자에게 올바르게 전달되어야 함.

### 실행과정 구성요소

유닛 테스트 케이스의 각 실행과정은 다음의 7가지 종류로 구분하여 범주(S1-S7)로 정의할 수 있다.

- S1 (사용자 입력에 따른 유닛 테스트 구동): 테스트 프레임워크를 통하거나 사용자의 직접 조작을 통해 주어지는 명령을 해석하여, 실행해야 할 유닛 테스트 코드를 지정하여 시행
- S2 (테스트 실행환경 설정): 유닛 테스트의 실행을 통제하기 위해 검증대상 모듈의 실행에 영향을 줄 수 있는 여러 변수(환경변수, 공유 변수 등)를 특정 값으로 설정
- S3 (테스트 입력 지정): 테스트케이스의 검증 목적에 따라 조정한 테스트 입력 값을 지정
- S4 (검증대상 모듈 실행): 지정된 테스트 입력 값으로 검증대상 모듈의 기능을 실행
- S5 (테스트 실행 결과 검사): 실행 결과를 테스트 입력 값에 대한 기대 값과 비교하여 테스트 통과 유무를 판별
- S6 (테스트 실행환경 설정 해제): S2에서 설정한 실행환경을 해제
- S7 (사용자에게 테스트 결과 보고): 테스트 통과 유무를 테스트 프레임워크를 통하거나 혹은 직접 사용자에게 전달

### 코드 구성요소

유닛 테스트 코드는 다음과 같이 서로 다른 구조로 구성되므로, 각 코드 요소는 연관된 코드 부위에 따라 4가지 코드 구성요소(P1-P4)로 분류할 수 있다.

- P1: 테스트케이스 별로 고유 시나리오를 정의하는 부위
- P2: 연관된 테스트케이스를 아울러 하나의 테스트 스위트를 구성하기 위한 부위
- P3: 검증대상 모듈이 호출하는 테스트 모형을 정의하거나, 테스트 모형 프레임워크와의 인터페이스 부위
- P4: 테스팅 프레임워크 인터페이스 부위로, 테스트 프레임워크의 기능을 호출/참조하는 부위

## Test Code Bug Patterns

#### 1. JUnit3 에서 TestCase 클래스에 test method 가 없는 경우

- **구성 요소** : R1, S1, P1
- **예제 코드**

```java
import junit.framework.TestCase;

public class AssertMethodInvokedFromRunMethod extends TestCase {
public void testComplex() {
    new Thread() {
        @Override
        public void run() {
            assertTrue(true); // Appears to pass test
            assertTrue(false); // AssertMethodFailedError occurs not test failure
        }
    }.start();
}
}
```

- **설명** : JUnit TestCase를 상속하여 Test Class이나, 어떠한 test method도 구현되어 있지 않다.
- **출처**
  - [FindBugs - IJU: TestCase has no tests (IJU_NO_TESTS)](http://findbugs.sourceforge.net/bugDescriptions.html#IJU_NO_TESTS)

#### 2. JUnit3 에서 suite() 선언이 잘못된 경우

- **구성 요소** : R1, S1, P2
- **예제 코드**

```java
public class BadSuiteMethod extends TestCase {
    static Test suite() {
        TestSuite suite = new TestSuite();
        suite.addTest(new MyTests("testEquals"));
        suite.addTest(new MyTests("testNotEquals"));
        return suite;
    }
}
```

- **설명** : suite()는 반드시 public static Test suite() 혹은 public static TestDuite suite() 로 정의되어야 한다.
- **출처**
  - [FindBugs - IJU: TestCase declares a bad suite method (IJU_BAD_SUITE_METHOD)](http://findbugs.sourceforge.net/bugDescriptions.html#IJU_BAD_SUITE_METHOD)
  - [FindBugs - IJU: TestCase implements a non-static suite method (IJU_SUITE_NOT_STATIC)](http://findbugs.sourceforge.net/bugDescriptions.html#IJU_SUITE_NOT_STATIC)
  - [PMD - JUnitStaticSuite](https://pmd.github.io/pmd-6.0.0/pmd_rules_java_errorprone.html#junitstaticsuite)

#### 3. JUnit4 에서 suite()를 오버라이드한 경우

- **구성 요소** : R1, S1, P2
- **설명** : JUnit3 프레임워크는 suite() method를 사용하여 test suite를 구성하는데, JUnit4에서는 @RunWith(Suite.class) annotation을 사용하여야 한다.
- **예제 코드**

```java
import junit.framework.TestCase;

public class BadStaticSuite extends TestCase{
    public void suite() {}         // oops, should be static
}

```

- **출처**
  - [PMD - JUnit4SuitesShouldUseSuiteAnnotation ](https://pmd.github.io/pmd-6.0.0/pmd_rules_java_bestpractices.html#junit4suitesshouldusesuiteannotation)

#### 4. JMock 객체를 사용하면서 동시에, 해당 Test Class 에서 JMock Runner를 사용하지 않음

- **구성 요소** : R1, S1, P4
- **예제 코드**

```java
17 // @RunWith(JMock.class) code using Mockery Class without JMock.class
18 public class PricingServiceTests {
19
20     private static final String SKU = "3283947";
21     private static final String BAD_SKU = "-9999993434";
22
23     private PricingService systemUnderTest;
24     private DataAccess mockedDependency;
25     private Mockery mockingContext;
26
27     @Before
28     public void doBeforeEachTestCase() {
29         mockingContext = new JUnit4Mockery();
30         systemUnderTest = new PricingServiceImpl();
31         mockedDependency = mockingContext.mock(DataAccess.class);
32         systemUnderTest.setDataAccess(mockedDependency);
33     }
```

- **설명** : jMock 테스트는 반드시 JMock Runner를 사용하여야 한다. 그렇지 않은 경우 항상 테스트가 성공하게 된다.
- **출처**
  - [ErrorProne - JMockTestWithoutRunWithOrRuleAnnotation](http://errorprone.info/bugpattern/JMockTestWithoutRunWithOrRuleAnnotation)

#### 5. JUnit3 에서 setUp()이 오버라이드되어 있으나, super.setUp() 을 호출하지 않음

- **구성 요소** : R1, S2, P3
- **코드 예제**

```java
public class BadSetUp extends TestCase {

  protected void setUp() throws Exception {
    // somePreparations();
  }
  ...
}
```

```java
public class CorrectSetUp extends TestCase {
  protected void setUp() throws Exception {
    super.setUp();
    // somePreparations();
  }
  ...
}
```

- **설명** : JUnit3 에서 setUp() method를 override할 때 반드시 super.setUp()을 호출하여야 한다.
- **출처**
  - [FindBugs - IJU: TestCase defines setUp that doesn't call super.setUp() (IJU_SETUP_NO_SUPER)](http://findbugs.sourceforge.net/bugDescriptions.html#IJU_SETUP_NO_SUPER)

#### 6. JUnit4 에서 setUp()을 정의하고 @Before Annotation이 없는 경우

- **구성 요소** : R1, S2, P4
- **설명** : JUnit4 에서는 @Before Annotation을 사용하여 테스트가 실행되기 전에 실행되는 메소드를 정의한다.
- **코드 예제**

```java
public class BadTest {
    public void setUp() {
        bad();    
    }
}
```

```java
public class GoodTest {
    @Before
    public void setUp() {
        good();
    }
}
```

- **출처**
  - [PMD - JUnit4TestShouldUseBeforeAnnotation](https://pmd.github.io/pmd-6.0.0/pmd_rules_java_bestpractices.html#junit4testshouldusebeforeannotation)
  - [ErrorProne - JUnit4SetUpNotRun](http://errorprone.info/bugpattern/JUnit4SetUpNotRun)

#### 7. JUnit4 의 @AfterClass나 @BeforeClass annotation을 사용하면서 method가 static이 아닌 경우

- **구성 요소** : R1, S2, P4
- **설명** : Beforeclass나 AfterClass annotation을 사용하는 메소드는 반드시 static 메소드로 선언되어야 한다.
- **코드 예제**

```java
public class JUnit4ClassAnnotationNonStaticPositiveCases {
  @BeforeClass
  public void beforeClassSetUp() {
      setUpSomething();
  }

  @AfterClass
  public void afterClassTearDown() {
      releaseSomething();
  }
}
```

- **출처**
  - [ErrorProne - JUnit4ClassAnnotationNonStatic](http://errorprone.info/bugpattern/JUnit4ClassAnnotationNonStatic)

#### 8. JUnit3 에서 약속된 메소드 명이 잘못된 경우

- **구성 요소** : R1, S2, S6, P1
- **코드 예제**

```java
import junit.framework.TestCase;

public class JUnit3TestNotRunPositiveCases extends TestCase {
  //  TestCase를 extend하나, @Test annotation을 사용하고 있어,
  //  JUnit3을 구현한 것인지, JUnit4 을 구현한 것인지 불분명하다.
  @Test
  public void testName() {}
}
```

- **설명** : JUnit3 에서는 반드시 test* 형식으로 메소드 명이 정의되어야 하는데, 이를 지키지 않는 경우, 테스트가 실행되지 않는다.
- **출처**
  - [ErrorProne - JUnit3TestNotRun](http://errorprone.info/bugpattern/JUnit3TestNotRun)

#### 9. JDK9 로 테스트 코드를 컴파일했을 때 오류가 나는 Mockito code pattern 을 사용한 경우

- **구성 요소** : R1, S3, P1
- **코드 예제**

```java
class Foo {
  <T> T getFirst(Iterable<T> xs) {
    return xs.iterator().next();
  }
}
class Test {
  @Mock Foo f;

  @Test
  public void test() {
    Iterable<Boolean> it = Arrays.asList(false);
    when(f.getFirst(it)).thenReturn(false);
  }
}
```

- **설명** : Jdk9으로 해당 테스트 코드를 컴파일 하는 경우 성공해야할 test가 ClassCastException이 발생하게 되어 fail하게 된다.
- **출처**
  - [ErrorProne - MockitoCast](http://errorprone.info/bugpattern/MockitoCast)


#### 10. JUnit3 에서 tearDown()이 오버라이드되어 있으나, super.tearDown() 을 호출하지 않음

- **구성 요소** : R1, S6, P3
- **설명** : JUnit3 에서 setUp() method를 오버라이드할 때 반드시, super.tearDown()을 호출하여야 한다.
- **코드 예제**

```java
public class BadTearDown extends TestCase {
    protected void tearDown() throws Exception {
        // no super.tearDown();
    }
}
```

```java
public class CorrectTearDown extends TestCase {
    protected void tearDown() throws Exception {
        super.teraDown();
    }
}
```

- **출처**
  - [FindBugs - IJU: TestCase defines tearDown that doesn't call super.tearDown() (IJU_TEARDOWN_NO_SUPER)](http://findbugs.sourceforge.net/bugDescriptions.html#IJU_TEARDOWN_NO_SUPER)

#### 11. JUnit4 에서 tearDown()을 정의하고 @After Annotation 이 없는 경우

- **구성 요소** : R1, S6, P4
- **코드 예제**

```java
public class MyTest {
    public void tearDown() {
        bad();
    }
}
public class MyTest2 {
    @After 
    public void tearDown() {
        good();
    }
}
```

- **설명** : JUnit4 에서는 @After Annotation을 사용하여 테스트가 실행된 후에 실행되는 메서드를 정의한다.
- **출처**
  - [PMD - JUnit4TestShouldUseAfterAnnotation](https://pmd.github.io/pmd-6.0.0/pmd_rules_java_bestpractices.html#junit4testshoulduseafterannotation)
  - [ErrorProne - JUnit4TearDownNotRun](http://errorprone.info/bugpattern/JUnit4TearDownNotRun)


#### 12. assertThrows에 넘겨진 코드에서 Exception을 throw한 이후 statement가 남아있음

- **구성 요소** : R2, S4, P1
- **예제 코드**

```java
ImmutableList xs;
assertThrows( UnsupportedOperationException.class, () -> 
    { xs = ImmutableList.of(); // the test passes if this throws 
      xs.add(0); // this is never executed 
});
assertThat(xs).isEmpty();
```

- **설명** : assertThrows에 파라미터로 넘겨진 lambda에 한개 이상의 statement가 있고, 내부에서 exception을 throw한다면 그 뒤의 statement들은 무시된다.
- **출처**
  - [ErrorProne - AssertThrowsMultipleStatements](http://errorprone.info/bugpattern/AssertThrowsMultipleStatements)


#### 13. Test Method 내부에서 Assertion 메소드의 호출이 없는 경우

- **구성 요소** : R4, S5, P1
- **코드 예제**

```java
public class Foo extends TestCase {
   public void testSomething() {
   Bar b = findBar();
   // This is better than having a NullPointerException
   // assertNotNull("bar not found", b);
   b.work();
   }
}
```

- **설명** : 테스트에서 Assertion 메소드가 없는 경우이다. NullPointerException을 기대하는 경우 assertNotNull의 사용이 권장된다.
- **출처**
  - [PMD - JUnitTestsShouldIncludeAssert](https://pmd.github.io/pmd-6.0.0/pmd_rules_java_bestpractices.html#junittestsshouldincludeassert)
  - [Fb-contrib - UTAO_JUNIT_ASSERTION_ODDITIES_NO_ASSERT](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_JUNIT_ASSERTION_ODDITIES_NO_ASSERT)
  - [Fb-contrib - UTAO_TESTNG_ASSERTION_ODDITIES_NO_ASSERT](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_TESTNG_ASSERTION_ODDITIES_NO_ASSERT)

#### 14. assertNull() 안에 boxed primitive 가 파라미터로 넘겨지는 경우

- **구성 요소** : R4, S5, P1
- **코드 예제**

```java
public class ImpossibleNullTest {
    @Test
    public void impossibleNullTest() {
        Integer autoboxedInt = 30;
        assertNotNull(autoboxedInt);
    }
}
```

- **설명** : assertNull() 안에 상수의 wrapper class가 파라미터로 넘겨지는 경우 항상 그 assertion은 항상 성공 pass한다.
- **출처**
  - [Fb-contrib - UTAO_JUNIT_ASSERTION_ODDITIES_IMPOSSIBLE_NULL](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_JUNIT_ASSERTION_ODDITIES_IMPOSSIBLE_NULL)
  - [Fb-contrib -   UTAO_TESTNG_ASSERTION_ODDITIES_IMPOSSIBLE_NULL](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_TESTNG_ASSERTION_ODDITIES_IMPOSSIBLE_NULL)

#### 15. Test Framework를 사용하는 동시에 assert()를 사용하는 경우

- **구성 요소** : R4, S5, P1
- **코드 예제**

```java
public class AssertUsedTest {
    @Test
    public void assertUsedTest() {
        Integer actual = methodReturnsNonZeroNumber();
        assert(actual != 0); // This is BAD
        assertNotEquals(actual, 0); // This is GOOD
    }
}
```

- **설명** : Test framework를 사용하는 경우 framework에서 제공하는 assertion method를 사용해야한다. Runtime flag에 따라서, Test framework가 assert()의 결과를 체크하지 못할 수 있어, 실패해야할 테스트가 성공할 수도 있게 된다.
- **출처**
  - [ErrorProne - UseCorrectAssertInTests](http://errorprone.info/bugpattern/UseCorrectAssertInTests)
  - [Fb-contrib - UTAO_JUNIT_ASSERTION_ODDITIES_ASSERT_USED](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_JUNIT_ASSERTION_ODDITIES_ASSERT_USED)
  - [Fb-contrib - UTAO_TESTNG_ASSERTION_ODDITIES_ASSERT_USED](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_TESTNG_ASSERTION_ODDITIES_ASSERT_USED)

#### 16. Assertion method 에서 동일한 객체의 reference 가 서로 같은 지 확인하는 경우

- **구성 요소** : R4, S5, P1
- **코드 예제**

```java
public class JUnitAssertSameCheckPositiveCase {

  public void test(Object obj) {
    // BUG: Diagnostic contains: An object is tested for reference equality to itself using JUnit
    org.junit.Assert.assertSame(obj, obj);

    // BUG: Diagnostic contains: An object is tested for reference equality to itself using JUnit
    org.junit.Assert.assertSame("message", obj, obj);

    // BUG: Diagnostic contains: An object is tested for reference equality to itself using JUnit
    junit.framework.Assert.assertSame(obj, obj);

    // BUG: Diagnostic contains: An object is tested for reference equality to itself using JUnit
    junit.framework.Assert.assertSame("message", obj, obj);
  }
}
```

- **설명** : 같은 객체에대해 reference가 같은지 확인하는 경우 assertion은 반드시 pass하게 된다.
- **출처**
  - [ErrorProne - JUnitAssertSameCheck](http://errorprone.info/bugpattern/JUnitAssertSameCheck)

#### 17. Mockito 를 사용하는 테스트에서 verify 메소드를 사용하지 않음

- **구성 요소** : R4, S5, P1
- **코드 예제**

```java
// Calls to Mockito.when should always be accompanied by a call to a method like thenReturn.
when(mock.get()).thenReturn(answer); // correct
when(mock.get())                     // oops!

```

- **설명** : Mockito를 사용하는 테스트에서 verify 메소드를 사용하지 않아, 테스트가 항상 성공하게 된다.
- **출처** :
  - [ErrorProne - MockitoUsage](http://errorprone.info/bugpattern/MockitoUsage)

#### 18. Assertion 메소드를 사용하여 동일한 객체에 대해 equality 를 검증하는 경우

- **구성 요소** : R4, S5, P1
- **코드 예제**

```java
public class TruthSelfEqualsPositiveCases {
  public void testAssertThatEq() {
    String test = Boolean.TRUE.toString();
    // BUG: Diagnostic contains: new EqualsTester().addEqualityGroup(test).testEquals()
    assertThat(test).isEqualTo(test);
  }
  ...
}
```

- **설명** :  같은 객체에대해 equality를 검증하는 경우 항상 pass하게 된다.
- **출처**
  - [ErrorProne - TruthSelfEquals](http://errorprone.info/bugpattern/TruthSelfEquals)
  - [ErrorProne - SelfEquals](http://errorprone.info/bugpattern/SelfEquals)



#### 19. Thread.run() 내부에 assertion 이 있는 경우

- **구성 요소** : R4, S7, P1
- **코드 예제**

```java
public class TestNonExistentJob extends TestCase {
@@ -96,8 +89,13 @@ protected void tearDown() throws Exception { 
  }
    
public void testGetInvalidJob() throws Exception {
RunningJob runJob = new JobClient(getJobConf()).getJob(JobID.forName("job_0_0"));
assertNull(runJob);
+    try {
+      RunningJob runJob = new JobClient(getJobConf()).getJob(JobID.forName("job_0_0"));
+      fail("Exception is expected to thrown ahead!");
+    } catch (Exception e) {
+      assertTrue(e instanceof IOException);
+      assertTrue(e.getMessage().contains("ApplicationNotFoundException"));
+    }
   }
  }

```

- **설명** : JUnit의 경우, Test를 실행하는 스레드가 assertion method를 실행해야 한다. 그렇지 않은 경우 test의 실패로 이어지지 않는다.
- **출처**
  - [findBugs - IJU: JUnit assertion in run method will not be noticed by JUnit (IJU_ASSERT_METHOD_INVOKED_FROM_RUN_METHOD)](http://findbugs.sourceforge.net/bugDescriptions.html#IJU_ASSERT_METHOD_INVOKED_FROM_RUN_METHOD)

#### 20. Assertion 메소드에서 test 하고자 하는 객체가 상수인 경우

- **구성 요소** : R4, R5, S5, P1
- **코드 예제**

```java
import static com.google.common.truth.Truth.assertThat;
public class TruthConstantAssertsPositiveCases {
  public void testAssertThat() {
    // BUG: Diagnostic contains: assertThat(new TruthConstantAssertsPositiveCases()).isEqualTo(1);
    assertThat(1).isEqualTo(new TruthConstantAssertsPositiveCases());

    // BUG: Diagnostic contains: assertThat(someStaticMethod()).isEqualTo("my string");
    assertThat("my string").isEqualTo(someStaticMethod());
    ...
}
```

**설명** : JUnit의 assertEquals는 파라미터로 기대하는 값(상수)을 먼저, 실제 테스트 하고자 하는 값을 다음에 받아야 한다.

- **출처**
  - [ErrorProne - AssertEqualsArgumentOrderChecker](http://errorprone.info/bugpattern/AssertEqualsArgumentOrderChecker)
  - [ErrorProne - TruthConstantAsserts](http://errorprone.info/bugpattern/TruthConstantAsserts)
  - [Fb-contrib - UTAO_JUNIT_ASSERTION_ODDITIES_ACTUAL_CONSTANT](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_JUNIT_ASSERTION_ODDITIES_ACTUAL_CONSTANT)
  - [Fb-contrib - UTAO_TESTNG_ASSERTION_ODDITIES_ACTUAL_CONSTANT](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_TESTNG_ASSERTION_ODDITIES_ACTUAL_CONSTANT)

#### 21. assert 구문에서 타입이 다른 두 객체의 equality 를 비교하는 경우

- **구성 요소** : R4, R6, S5, P1
- **코드 예제**

```java
public class TruthIncompatibleTypePositiveCase  {
  assertThat(someMethodThatReturnsString()).isEqualTo(10);
}
```

- **설명** : Truth framework에서 서로 타입이 다른 두 객체의 equality 를 비교하는 경우이다.
- **출처**
  - [ErrorProne - TruthIncompatibleType](http://errorprone.info/bugpattern/TruthIncompatibleType)

#### 22. assertionEquals()에서 부동 소수점 비교에서 오차 허용 범위가 없는 경우

- **구성 요소** : R5, S5, P1
- **코드 예제**

```java
import junit.framework.TestCase;

public class JUnit3FloatingPointComparisonWithoutDeltaCases extends TestCase {
  public void testFloat() {
      float actual = 0.5 + 0.5;
      assertEquals(1.0f, actual); // this is not good
  }
}

```

- **설명**
  - JUnit3에서 오류 허용 범위 없이 assertionEquals()를 사용할 수 있다. 부동소수점의 특성상 오류 허용 범위 없이 비교를 할 때 의도하지 않았던 테스트의 실패로 이어질 수 있다.
- **출처**
  - [Fb-contrib - UTAO_JUNIT_ASSERTION_ODDITIES_INEXACT_DOUBLE](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_JUNIT_ASSERTION_ODDITIES_INEXACT_DOUBLE)
  - [Fb-contrib - UTAO_TESTNG_ASSERTION_ODDITIES_INEXACT_DOUBLE](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_TESTNG_ASSERTION_ODDITIES_INEXACT_DOUBLE)
  - [ErrorProne - JUnit3FloatingPointComparisonWithoutDelta](http://errorprone.info/bugpattern/JUnit3FloatingPointComparisonWithoutDelta)

#### 23. Try 블록 내부에서 fail()이 있으나, catch 블록에서 AssertionError 를 catch 하는 경우

- **구성요소** : R5, S5, P1
- **코드 예제**

```java
try {
  doSomething();
  fail("expected doSomething to throw AssertionError");
} catch (AssertionError expected) {
  // expected exception
}

```

- **설명** : Exception throw 되기를 기대하는 try block 안에서 실제로 Exception이 throw 되지 않아 테스트가 실패되야 하므로 fail()을 호출되었으나, catch block에서 AssertionError catch하는 경우, 테스트가 fail하지 않게 된다.
- **출처**
  - [ErrorProne - AssertionFailureIgnored](http://errorprone.info/bugpattern/AssertionFailureIgnored)

#### 24. assertTrue() 내부의 판별식에서 equals 를 호출하여 두 객체가 같음을 확인하는 경우

- **구성 요소** : R6, S5, P1
- **코드 예제**

```java
import org.testng.Assert;
import org.testng.annotations.Test;

public class TestHelloWorld {
    @Test()
    public void testUseAssertEquals() {
        Object actual = getActual();
        Object expected = getExpected;
        Assert.assertTrue(expected.equals(actual)); // This is Bad
        Assert.assertEquals(expected,actual); // This is Good
    }
}
```

- **설명** : AssertTrue() eqauls()를 사용하여 두 객체가 같은지 확인하고 있다. equals()는 호출하는 객체가 null일 때 NullPointerException이 발생되므로, null-safe한 AssertSame()을 사용하는 것이 권장된다. 또한 Test의 실패시 AssertSame()을 사용하면 Framework가 더욱 상세한 오류 메세지를 출력하게 된다.
- **출처**
  - [PMD - UseAssertEqualsInsteadOfAssertTrue](https://pmd.github.io/pmd-6.0.0/pmd_rules_java_bestpractices.html#useassertequalsinsteadofasserttrue)
  - [Fb-contrib - UTAO_JUNIT_ASSERTION_ODDITIES_USE_ASSERT_EQUALS](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_JUNIT_ASSERTION_ODDITIES_USE_ASSERT_EQUALS)
  - [Fb-contrib - UTAO_TESTNG_ASSERTION_ODDITIES_USE_ASSERT_EQUALS](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_JUNIT_ASSERTION_ODDITIES_USE_ASSERT_EQUALS)

#### 25. assertTrue() 내부의 판별식에서 test 하고자 하는 객체가 null 인지를 확인하는 경우

- **구성 요소** : R6, S5, P1
- **코드 예제**

```java
import org.testng.Assert;
import org.testng.annotations.Test;

public class TestHelloWorld {
    @Test()
    public void testUseAssertNull() {
        Object actual = getActual();
        Assert.assertEquals(actual == null ); // This is Bad
        Assert.assertNull(actual) // This is Good
    }
}

```

- **설명** : AssertTrue() 내부에 null과의 비교하는 연산 사용하여 객체가 null인지 확인하고 있다. 이를 위해 만들어진 AssertNull()을 사용하는 것이 권장된다.
- **출처**
  - [PMD - UseAssertNullInsteadAssertTrue](https://pmd.github.io/pmd-6.0.0/pmd_rules_java_bestpractices.html#useassertnullinsteadofasserttrue)
  - [Fb-contrib - UTAO_JUNIT_ASSERTION_ODDITIES_USE_ASSERT_NULL](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_JUNIT_ASSERTION_ODDITIES_USE_ASSERT_NULL)
  - [Fb-contrib - UTAO_TESTNG_ASSERTION_ODDITIES_USE_ASSERT_NULL](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_JUNIT_ASSERTION_ODDITIES_USE_ASSERT_NULL)

#### 26. JUnit4 에서 test method 에 @Test Annotation이 없는 경우

- **구성 요소** : R1, S1, P4
- **예제 코드**

```java
public class MyTest {
  public void testBad() {
    doSomething(); // no execution of test
  }

  @Test
  public void testGood() {
    doSomething();
  }
}
```

- **설명** : JUnit4에서는 test method에는 반드시 @Test annotation을 사용하여야 한다.
- **출처**
  - [PMD - JUnit4TestShouldUseTestAnnotation](https://pmd.github.io/pmd-6.0.0/pmd_rules_java_bestpractices.html#junit4testshouldusetestannotation)
  - [ErrorProne - JUnit4TestNotRun](http://errorprone.info/bugpattern/JUnit4TestNotRun)

#### 27. assertionTrue() 의 파라미터로 넘어가야할 statement를 잘못 입력함

- **구성 요소** : R1, S5, P1
- **코드 예제**

```java
@@ -1071,7 +1070,7 @@ public class SystemPrivilegesPermissionTest extends BaseTestCase {
         assertEquals(p1.hashCode(), p2.hashCode());
         
         assertTrue(p1.implies(p2));
-        assertTrue(p1.implies(p2));
+        assertTrue(p2.implies(p1));
     }
```

- **설명** : Check both "p1 implies p2" and "p2 implies p1" in  assertEquivalentPermissions(), instead of checking "p1 implies p2"  twice.
- **출처**
  - [DERBY-6716](https://issues.apache.org/jira/browse/DERBY-6716)

#### 28. xml query로 사용되는 변수의 값을 잘못 입력함

- **구성 요소** : R1, S2, P1
- **코드 예제**

```java
  pupblic class PredicatesTests extends AbstractQueryTest {
    ...
    public void testEquality() throws RepositoryException {
-   String stmt = "/" + jcrRoot + "/*[@" + jcrPrimaryType + "='" + nodeTypeName + "']";
+   String stmt = "/" + jcrRoot + "/" + testPath + "/*[@" + jcrPrimaryType + "='" + nodeTypeName + "']";
    try {
      qm.createQuery(stmt, Query.XPATH);
    ...
  }
  
  public abstract class Abstract JCRTest extends JUnitTest {
    ...
    /***
      *  Absolute path to the test root node.
      */
    protected String testRoot;
    ...
    protected void setUp throws Exception {
      super.setUp();
      testRoot = getProperty(RepositoryStub.PROP_TESTROOT);
      if (testRoot == null) {
        fail("Property '" + RepositoryStub.PROP_TESTROOT + "' is not defined.");
      }
      ...
      
      // cut off '/' to build testPath
      testPath = testRoot.substring(1);
      ...
    }
  }  
```

- **설명** : xml Query에 사용되는 stmt 변수에 testPath가 concatenate 되어야 하는데, PredicateTest class에서 이를 누락하였다.
            (PredicatesTest does not respect testroot configuration property)
- **출처**
  - [JCR-524](https://github.com/apache/jackrabbit/commit/fb1cfae246e07e121cce9350309eaae619992de0)



#### 28. FileInputStream 을 생성하였으나 close()를 호출하지 않음

- **구성 요소** : R1, S6, P1
- **설명** : InputStream을 생성한 뒤 테스트 종료 전 close를 하지 않은 경우이다. (windows에서는 close 되지 않음)
- **코드 예제**

```java
@Test
   public void testBZip2Codec() throws IOException {
     ...
     // check the output to make sure it is what we expected.
     // read the gzip file and verify the contents
     BZip2Codec bz2Codec = new BZip2Codec();
     InputStream bz2in = bz2Codec.createInputStream(new FileInputStream(f
-        .getPath()
-        + "/sub-foo.bz2"));
+        .getPath() + "/sub-foo.bz2"));
     byte[] buf = new byte[1];
     StringBuilder output = new StringBuilder();
 
     while ((bz2in.read(buf)) > 0) {
       output.append(new String(buf));
     }
+    bz2in.close(); // Must close for windows to delete
     assertEquals(expected, output.toString());
 
     assertTrue("temp folder successfully deleted", FileUtil.rmr(f));
   }
```

- **출처**
  - [FLUME-349](https://github.com/apache/flume/commit/41adc8af6f179f1222edc79190accca3ca7dddc7)

#### 29. 테스트 내에서 Thread 를 생성했으나 join()을 하지 않고 테스트가 종료됨

- **구성 요소** :R4, S2, S3, S6, P1
- **코드 예제**

```java
 public simpleThread(String argv[]) throws Exception {

    ij.getPropertyArg(argv);
    _connection = ij.startJBMS();
    Connection conn = GetConnection();
    Statement stmt = conn.createStatement();
    ...
+   Thread[] threads = {
+     new simpleThread(query,0),
+     new simpleThread(query,10000),
+     new simpleThread(query,10100),
+     new simpleThread(query,20000),
+   };
+
+   for (int i = 0; i < threads.length; i++) {
+     threads[i].join();
    }
+
+   _connection.close();
+   _connection = null;
  }
```

- **설명** : Simple Thread의 constructor부분, thread 생성 후 connection을 close하지 않는다.
- **출처**
  - [DERBY-5708](https://issues.apache.org/jira/browse/DERBY-5708)

#### 30. org.junit.Test 를 import 하는 java 파일의 public class 명이 Test 로 시작되지 않음

- **구성 요소** : R1, R3, S1, P4

```java
- public class SliderUtilsTest {
+ public class TestSliderUtils {
    ...
  }
```

- **설명** : Maven에서 테스트명이 Test로 시작해야 Test Class로 인식한다 (Default).
- **출처**
  - [SLIDER-41](https://issues.apache.org/jira/browse/SLIDER-41)

#### 31. Exception을 기대한 테스트케이스에서 try문 안에 fail()이 

- **구성 요소** : R1, R4, S5, P1
- **설명** : Exception을 의도한 테스트케이스에서 의도 하지 않은 Exception이 발생하지 않은 경우 fail()이 없어서 테스트가 해당 부분을 넘어가게 된다.
- **예제 코드**

```java
/* DERBY-6088 */
try {
      insert.setObject( 1, null, ILLEGAL_JDBC_TYPES[ i ] );
+     fail( "setObject() should have failed." );
    }
      catch (SQLException se) { assertUnimplemented( se ); }
    }
```

```java
/* DERBY-3852*/
JDBCDataSource.setBeanProperty(ds, "shutdownDatabase", "shutdown");
   try {
     ds.getConnection();
+    fail("shutdown should raise exception");
   } catch (SQLException sqle) {
     assertSQLState("XJ015", sqle);
   }
```
- **출처**
  - [DERBY-6088](https://issues.apache.org/jira/browse/DERBY-6088)
  - [DERBY-3852](https://issues.apache.org/jira/browse/DERBY-3852)

#### 32. SSLContext 의 getInstance 메소드에 "SSL"을 파라미터로 넘긴 경우

- **구성 요소** : R2, S2, S3, P1
- **예제 코드**

```java
- SSLContext sc = SSLContext.getInstance("SSL");
+ SSLContext sc = null;
+ if (System.getProperty("java.vendor").contains("IBM")) {
+     sc = SSLContext.getInstance("SSL_TLS");
+ } else {
+     sc = SSLContext.getInstance("SSL");
+ }
```

- **설명** : IBM JDK 버전에 따라서 "SSL_TLS"를 getInstance()의 파라미터로 넘겨야 한다.
- **출처**
  - [FLUME-2441](https://issues.apache.org/jira/browse/FLUME-2441)

#### 33. file path 의 경로를 '/'으로만 구분하는 경우

- **구성 요소** : R2, S3, P1
- **설명** : The prior code for path construction fails on Windows due to the drive spec and backslashes. Using FileSystem#makeQualified against the local file system works cross-platform.
- **예제 코드**

```java
   private static void delete(File dir) throws IOException {
-    Path p = new Path("file://"+dir.getAbsolutePath());
     Configuration conf = new Configuration();
-    FileSystem fs = p.getFileSystem(conf);
+    FileSystem fs = FileSystem.getLocal(conf);
+    Path p = fs.makeQualified(new Path(dir.getAbsolutePath()));
     fs.delete(p, true);
   }
```

- **출처**
  - [MAPREDUCE-4983](https://issues.apache.org/jira/browse/MAPREDUCE-4983)

#### 34. for 문의 조건문을 잘못 입력하여 test 를 실행하지 못한 경우

- **구성 요소** : R2, S4, P1
- **코드 예제**

```java
   public void testDirectoryScanner() throws Exception {
     // Run the test with and without parallel scanning
-    for (int parallelism = 1; parallelism < 2; parallelism++) {
+    for (int parallelism = 1; parallelism < 3; parallelism++) {
       runTest(parallelism);
     }
   }

```

- **설명** : for문안의 조건문을 잘못 입력하여 test가 실행되지 않았다.
- **출처**
  - [HDFS-2596](https://issues.apache.org/jira/browse/HDFS-2596)

#### 35. 파일을 생성한 뒤 try-catch 블록 이후 finally 블록에 close()를 호출하지 않은 경우

- **구성 요소** : R2, S6, P1

- **설명** : FileInputStream을 생성한 뒤 테스트가 끝나기 전 try블락 안에 exception이 throw될 수 있는  메서드를 사용한 이후, finally 블록 안에 FileInputStream의 close()를 호출하지 않은 경우이다.

  temporary files created by some jUnit test are not automatically removed

- **코드 예제**

```diff
  public void testSessionGetcontentHandler() throws RepositoryException, IOException, SAXException {
    FileInputSstrean in = new FileInputStream(file);
-   exportRepository(SAVEBINARY, RECURSE);
-   doImportNoSave(treeComparator.targetFoler, in, CONTENTHANDLER);
+   try {
+     exportRepository(SAVEBINARY, RECURSE);
+     doImportNoSave(treeComparator.targetFoler, in, CONTENTHANDLER);    
+   } finally {
+     in.close();
+ }
```

- **출처**
  - [JCR-267](https://github.com/apache/jackrabbit/commit/7591c138037917c0dc6dc8d80fbeb2ebaa0cbd99)

#### 36. Test Suite 에서 하나의 DB 를 사용하고, Sequence 를 생성한 뒤 Drop 하지 않은 경우

- **구성 요소** : R3, S2, S5, P2
- **설명** : ‘alpha_seq’ 라는 sequence가 만들어지고 testCase가 종료되기 전 drop 되지 않아, 다음 테스트의 실행에 영향을 미친다.
            lang.SequenceTest fails w/ "Seqeunce 'ALPHA_SEQ' already exists." on phoneME/cvm.
            commit message : Do cleanup in testcases to make the testcases independent of running order.

- **코드 설명**

```java
  public class SequenceTest extends BaseJDBCTestCase {
    public static Test suite() {
      public void testCreateOtherSchemeSequence() throws SQLExecption {
        ..
        assertStatementError("42507", stmtBeta, "CREATE SEQUENCE alpha.alpha_seq3");
 
+       // Cleanup:
+       stmtAlpha.executeUpdate("DROP SEQUENCE alpha_seq");
+        
        stmtAlpha.close();
```

- **출처**
  - [DERBY-4393](https://github.com/apache/derby/commit/c4caf0a622be2c2a64fae9c705bab70894bd953b)

#### 37. Socket 을 생성하고 테스트가 끝나는 시점에 close() 하지 않은 경우

- **구성 요소** : R3, S6, P1
- **설명** : socket을 생성하고 close를 하지 않아 다음 테스트 실행 결과에 영향을 미칠 수 있다.
- **코드 설명**

```java
  @Test
  public void testShortCircuitReadFromServerWithoutShm() throws Exception {
    TemporarySocketDirectory sockDir = new TemporarySocketDirectory();
    //...
        
+   sockDir.close();
  }
```

- **출처**
  - [HDFS-7282](https://issues.apache.org/jira/browse/HDFS-7282)

#### 38. 테스트 실행 도중 공유 변수의 값을 임의로 변경한 후 되돌리지 않음

- **구성 요소** : R3, S6, P1, P2
- **설명** : 이전 테스트에서의 설정이 다음 테스트에서도 적용되어 테스트 결과가 바뀌는 경우이다.
- **예제 코드**

```java
@Test
public void testGzipOutputFormat() throws IOException, InterruptedException {
    // set the output format.
    FlumeConfiguration conf = FlumeConfiguration.get();
    conf.set(FlumeConfiguration.COLLECTOR_OUTPUT_FORMAT, "syslog");
    conf.set(FlumeConfiguration.COLLECTOR_DFS_COMPRESS_GZIP, "true");
    ...
}

@Test
public void testDefaultCodec() throws IOException, InterruptedException {
    // set the output format.
    FlumeConfiguration conf = FlumeConfiguration.get();
    conf.set(FlumeConfiguration.COLLECTOR_OUTPUT_FORMAT, "syslog");
    conf.set(FlumeConfiguration.COLLECTOR_DFS_COMPRESS_CODEC, "DefaultCodec");
+   conf.set(FlumeConfiguration.COLLECTOR_DFS_COMPRESS_GZIP, "false");
    ...
}    
```

- **출처**
  - [FLUME-571](https://issues.apache.org/jira/browse/FLUME-571)
  - [ACCUMULO-2198](https://github.com/apache/accumulo/commit/cd4eac0d7e2820321db9fc9cdfc8dc89f7dd53d2)

#### 39. Ant 1.9.3 에서 ant 가 test 결과를 file 을 수정할 수 있는 권한을 설정하지 않음

- **구성 요소** : R3, S7, P4
- **설명** : Ant 1.9.3 버전부터 Test result를 file에 쓰기 위해 ant.jar에 write permission이 필요해졌는데 관련 권한 설정이 실행되지 않았다.
- **예제 코드**

```java
  // java/testing/org/apache/derbyTesting/functionTests/util/derby_tests.policy
  ...
+ // Starting with Ant 1.9.3, write permission has to be granted to ant.jar
+ // as well so that Ant's JUnit runner can write test results to a file.
+ // Only needed when running the tests under Ant. See DERBY-6685.
+ grant codeBase "${derbyTesting.ant}" {
+    permission java.io.FilePermission "${user.dir}${/}*", "write";
+ };
+

```

- **출처**
  - [DERBY-6685](https://issues.apache.org/jira/browse/DERBY-6685)

#### 40. 모든 test 가 실행되는지 확인하지 않음

- **구성 요소** : R4, S1, P4
- **설명** : 테스트를 실행시키는 부분에서 실제 의도하는대로 테스트가 실행되는지 확인하는 코드가 추가되지 않았다.
- **테스트 코드**

```java
public static void runTests( final SecurityEvaluator securityEvaluator, final PrefixMapping prefixMapping ) throws Exception
{
    ...
    Method lockTest = null;
    for (final Method m : pmTest.getClass().getMethods())
    {
        if (m.isAnnotationPresent(Test.class))
        {
        // lock test must come last
            if (m.getName().equals("testLock")) { lockTest = m; }
            else { pmTest.setup(); m.invoke(pmTest); }
        }
    }
+   Assert.assertNotNull( "Did not find 'testLock' method", lockTest );     
    pmTest.setup();
    lockTest.invoke(pmTest);
}

```



- **출처**
  - [JENA-795](https://github.com/apache/jena/commit/bbd9456f3dafb19c6603bc38b957b542d786f91f)

#### 41. test 가 추가되기 전에 호출되어야 할 메소드 leasechecker.interruptAndJoin()이 누락됨

- **구성 요소** : R4, S2, P1
- **설명** : Test가 추가되기전에 반드시 호출되어야할 메소드가 누락되었다.
- **코드 예제**

```java
Index: src/test/hdfs/org/apache/hadoop/hdfs/TestReadWhileWriting.java
===================================================================
--- src/test/hdfs/org/apache/hadoop/hdfs/TestReadWhileWriting.java  (revision 889032)
+++ src/test/hdfs/org/apache/hadoop/hdfs/TestReadWhileWriting.java  (working copy)
@@ -83,6 +83,8 @@ 
       //b. On another machine M2, open file and verify that the half-block
       //   of data can be read successfully.
  01   @Test
  02   public void pipeline_02_03() throws Exception {
  03    ...
  04   checkFile(p, half, conf);
+ 05   AppendTestUtil.LOG.info("leasechecker.interruptAndJoin()");
+ 06   ((DistributedFileSystem)fs).dfs.leasechecker.interruptAndJoin();
  07   //c. On M1, append another half block of data.  Close file on M1.
  08   {
  09     //sleep to let the lease is expired.
  10     Thread.sleep(2*LEASE_LIMIT);
  11 
  12     final DistributedFileSystem dfs = (DistributedFileSystem)FileSystem.newInstance(conf);
  13     final FSDataOutputStream out = append(dfs, p);
  14     write(out, 0, half);
  15     out.close();
  16   }


```

- **출처**
  - [HDFS-824](https://issues.apache.org/jira/browse/HDFS-824)

#### 42. 테스트 환경을 설정하는 동일한 메소드를 연속해 중복 실행하는 경우

- **구성 요소** : R4, S2, P1
- **설명** : 테스트 환경을 설정하는 메소드를 연속해서 중복 호출하였다.
- **코드 예제**

```java
   c.setInt("hbase.ipc.client.connect.max.retries", 1);
-  c.setInt("hbase.ipc.client.connect.max.retries", 1);
   c.setInt("dfs.client.block.recovery.retries", 1);
```

- **출처**
  - [HBASE-11698](https://issues.apache.org/jira/browse/HBASE-11698)

#### 43. setup() 이나 teardown()에서 Exception 을 catch 하지 않음

- **구성 요소** : R4, S2, S6, P2
- **코드 예제**

```java
    protected Node cleanUpTestRoot(Session s) throws RepositoryException
    ...
    // clean test root
    testRootNode = root.getNode(testPath);
    for (NodeIterator children = testRootNode.getNodes(); children.hasNext();) {
-     children.nextNode().remove();
+     Node child = children.nextNode();
+     NodeDefinition nodeDef = child.getDefinition();
+     if (!nodeDef.isMandatory() && !nodeDef.isProtected()) {
+       child.remove();
+     }
    }
```

- **설명** : 테스트 환경을 초기화하는 메서드에서 적절한 오류 처리가 이루어지지 않았다.
- **출처**
  - [JCR-505](https://issues.apache.org/jira/browse/JCR-505)

#### 44. IOException 를 발생시키는 메소드를 assertNull()에서 호출함

- **구성 요소** : R4, S5, P1
- **코드 예제**



- **설명** : 테스트 대상 코드가 null을 리턴하는 것에서 IOException을 throw하는 것으로 변경되어 Test code도 변경되어야 하는데 변경되지 않아 Test가 실패한다.
- **출처**
  - [MAPREDUCE-5421](https://issues.apache.org/jira/browse/MAPREDUCE-5421)


#### 45. 연속으로 동일한 Assertion 을 호출함

- **구성 요소** : R1, S5, P1
- **코드 예제**

```java
@@ -1071,7 +1070,7 @@ public class SystemPrivilegesPermissionTest extends BaseTestCase {
         assertEquals(p1.hashCode(), p2.hashCode());
         
         assertTrue(p1.implies(p2));
-        assertTrue(p1.implies(p2));
+        assertTrue(p2.implies(p1));
     }
```

- **설명** : Check both "p1 implies p2" and "p2 implies p1" in  assertEquivalentPermissions(), instead of checking "p1 implies p2"  twice.
- **출처**
  - [DERBY-6716](https://issues.apache.org/jira/browse/DERBY-6716)
