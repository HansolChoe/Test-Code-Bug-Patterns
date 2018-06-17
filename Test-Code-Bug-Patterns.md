## Test Code Bug Patterns

1. JUnit3 에서 TestCase 클래스에 test method 가 없는 경우
    - **구성 요소** : R1, S1, P1
    - **설명** : JUnit JUnit TestCase를 상속하여 Test Class이나, 어떠한 test method도 구현되어 있지 않다.
    - **출처**
        - [FindBugs - IJU: TestCase has no tests (IJU_NO_TESTS)](http://findbugs.sourceforge.net/bugDescriptions.html#IJU_NO_TESTS)

2. JUnit3 에서 suite() 선언이 잘못된 경우
    - **구성 요소** : R1, S1, P2
    - **설명** : ‘suite’ 메소드는 반드시 public static Test suite() 혹은 public static TestDuite suite() 로 정의되어야 한다.
    - **출처**
        - [FindBugs - IJU: TestCase declares a bad suite method (IJU_BAD_SUITE_METHOD)](http://findbugs.sourceforge.net/bugDescriptions.html#IJU_BAD_SUITE_METHOD)
        - [FindBugs - IJU: TestCase implements a non-static suite method (IJU_SUITE_NOT_STATIC)](http://findbugs.sourceforge.net/bugDescriptions.html#IJU_SUITE_NOT_STATIC)
        - [PMD - JUnitStaticSuite](https://pmd.github.io/pmd-6.0.0/pmd_rules_java_errorprone.html#junitstaticsuite)

3. JUnit4 에서 suite()를 오버라이드한 경우
    - **구성 요소** : R1, S1, P2
    - **설명** : JUnit3 프레임워크는 suite() method를 사용하여 test suite를 구성하는데, JUnit4에서는 @RunWith(Suite.class) annotation을 사용하여야 한다.
    - **출처**
        - [PMD - JUnit4SuitesShouldUseSuiteAnnotation ](https://pmd.github.io/pmd-6.0.0/pmd_rules_java_bestpractices.html#junit4suitesshouldusesuiteannotation)

4. JUnit4 에서 test method 에 @Test Annotation이 없는 경우
    - **구성 요소** : R1, S1, P4
    - **설명** : JUnit4에서는 test method에는 반드시 @Test annotation을 사용하여야 한다.
    - **출처**
        - [PMD - JUnit4TestShouldUseTestAnnotation](https://pmd.github.io/pmd-6.0.0/pmd_rules_java_bestpractices.html#junit4testshouldusetestannotation)
        - [ErrorProne - JUnit4TestNotRun](http://errorprone.info/bugpattern/JUnit4TestNotRun)

5. JMock 객체를 사용하면서 동시에, 해당 Test Class 에서 JMock Runner를 사용하지 않음
    - **구성 요소** : R1, S1, P4
    - **설명** : jMock 테스트는 반드시 JMock Runner를 사용하여야 한다. 그렇지 않은 경우 항상 테스트가 성공하게 된다.
    - **출처**
        - [ErrorProne - JMockTestWithoutRunWithOrRuleAnnotation](http://errorprone.info/bugpattern/JMockTestWithoutRunWithOrRuleAnnotation)
6. testPath 를 잘못 설정함
    - **구성 요소** : R1, S2, P1
    - **설명** : test하는 폴더의 path를 잘못 설정하였다.
    - **출처**
        - [JCR-524]()

7. JUnit3 에서 setUp()이 오버라이드되어 있으나, super.setUp() 을 호출하지 않음
    - **구성 요소** : R1, S2, P3
    - **설명** : JUnit3 에서 setUp() method를 override할 때 반드시 super.setUp()을 호출하여야 한다.
    - **출처**
        - [FindBugs - IJU: TestCase defines setUp that doesn't call super.setUp() (IJU_SETUP_NO_SUPER)](http://findbugs.sourceforge.net/bugDescriptions.html#IJU_SETUP_NO_SUPER)

8. JUnit4 에서 setUp()을 정의하고 @Before Annotation이 없는 경우
    - **구성 요소** : R1, S2, P4
    - **설명** : JUnit4 에서는 @Before Annotation을 사용하여 테스트가 실행되기 전에 실행되는 메소드를 정의한다.
    - **출처**
        - [PMD - JUnit4TestShouldUseBeforeAnnotation](https://pmd.github.io/pmd-6.0.0/pmd_rules_java_bestpractices.html#junit4testshouldusebeforeannotation)
        - [ErrorProne - JUnit4SetUpNotRun](http://errorprone.info/bugpattern/JUnit4SetUpNotRun)

9. JUnit4 의 @AfterClass나 @BeforeClass annotation을 사용하면서 method가 static이 아닌 경우
    - **구성 요소** : R1, S2, P4
    - **설명** : Beforeclass나 AfterClass annotation을 사용하는 메소드는 반드시 static 메소드로 선언되어야 한다.
    - **출처**
        - [ErrorProne - JUnit4ClassAnnotationNonStatic](http://errorprone.info/bugpattern/JUnit4ClassAnnotationNonStatic)

10. JUnit3 에서 약속된 메소드 명이 잘못된 경우
    - **구성 요소** : R1, S2, S6, P1
    - **설명** : JUnit3 에서는 반드시 test* 형식으로 메소드 명이 정의되어야 하는데, 이를 지키지 않는 경우, 테스트가 실행되지 않는다.
    - **출처**
        - [ErrorProne - JUnit3TestNotRun](http://errorprone.info/bugpattern/JUnit3TestNotRun)

11. JDK9 로 테스트 코드를 컴파일했을 때 오류가 나는 Mockito code pattern 을 사용한 경우
    - **구성 요소** : R1, S3, P1
    - **설명** : Jdk9으로 해당 테스트 코드를 컴파일 하는 경우 성공해야할 test가 ClassCastException이 발생하게 되어 fail하게 된다.
    - **출처**
        - [ErrorProne - MockitoCast](http://errorprone.info/bugpattern/MockitoCast)

12. 연속으로 동일한 Assertion 을 호출함
    - **구성 요소** : R1, S5, P1
    - **설명** : Check both "p1 implies p2" and "p2 implies p1" in  assertEquivalentPermissions(), instead of checking "p1 implies p2"  twice.
    - **출처**
        - DERBY-6716

13. FileInputStream 을 생성하였으나 close()를 호출하지 않음
    - **구성 요소** : R1, S6, P1
    - **설명** : InputStream을 생성한 뒤 테스트 종료 전 close를 하지 않은 경우이다. (windows에서는 close 되지 않음)
    - **출처**
        - FLUME-349

14. JUnit3 에서 tearDown()이 오버라이드되어 있으나, super.tearDown() 을 호출하지 않음
    - **구성 요소** : R1, S6, P3
    - **설명** : JUnit3 에서 setUp() method를 오버라이드할 때 반드시, super.tearDown()을 호출하여야 한다.
    - **출처**
        - [FindBugs - IJU: TestCase defines tearDown that doesn't call super.tearDown() (IJU_TEARDOWN_NO_SUPER)](http://findbugs.sourceforge.net/bugDescriptions.html#IJU_TEARDOWN_NO_SUPER)

15. JUnit4 에서 tearDown()을 정의하고 @After Annotation 이 없는 경우
    - **구성 요소** : R1 S6 P4
    - **설명** : JUnit4 에서는 @After Annotation을 사용하여 테스트가 실행된 후에 실행되는 메서드를 정의한다.
    - **출처**
        - [PMD - JUnit4TestShouldUseAfterAnnotation](https://pmd.github.io/pmd-6.0.0/pmd_rules_java_bestpractices.html#junit4testshoulduseafterannotation)
        - [ErrorProne - JUnit4TearDownNotRun](http://errorprone.info/bugpattern/JUnit4TearDownNotRun)

16. 테스트 내에서 Thread 를 생성했으나 join()을 하지 않고 테스트가 종료됨
    - **구성 요소** : R1, R2, S2, S3, S6, P1
    - **설명** : Simple Thread의 constructor부분, thread 생성 후 connection을 close하지 않는다.
    - **출처**
        - DERBY-5708

17. org.junit.Test 를 import 하는 java 파일의 public class 명이 Test 로 시작되지 않음
    - **구성 요소** : R1, R3, S1, P4
    - **설명** : Maven에서 테스트명이 Test로 시작해야 Test Class로 인식한다 (Default).
    - **출처**
        - SLIDER-41

18. Exception 이 기대한 테스트케이스에서 Exception 이 발생하지 않아도 fail() 하지 않음
    - **구성 요소** : R1, R4, S5, P1
    - **설명** : Exception을 의도한 테스트케이스에서 의도치한게 Exception이 발생하지 않은 경우 fail()이 없어서 테스트가 해당 부분을 넘어가게 된다.
    - **출처**
        - DERBY-6088
        - DERBY-3852

19. SSLContext 의 getInstance 메소드에 "SSL"을 파라미터로 넘긴 경우 R2 S2, S3 P1 FLUME-2441
file path 의 경로를 '/'으로만 구분하는 경우
    - **구성 요소** : R2, S3, P1
    - **설명** : The prior code for path construction fails on Windows due to the drive spec and backslashes. Using FileSystem#makeQualified against the local file system works cross-platform.
    - **출처**
        - MAPREDUCE-4983

20. assertThrows 에 넘겨진 코드에서 Exception 을 throw 한 이후 statement 가 남아있음
    - **구성 요소** : R2 S4 P1
    - **설명** : The prior code for path construction fails on Windows due to the drive spec and backslashes. Using FileSystem#makeQualified against the local file system works cross-platform.
    - **출처**
        - [ErrorProne - AssertThrowsMultipleStatements](http://errorprone.info/bugpattern/AssertThrowsMultipleStatements)
21. for 문의 조건문을 잘못 입력하여 test 를 실행하지 못한 경우
    - **구성 요소** : R2, S4, P1
    - **설명** : for문을 조건문을 잘못 입력하여 test가 실행되지 않았다.
    - **출처**
        - HDFS-2596

22. 파일을 생성한 뒤 try-catch 블록 이후 finally 블록에 close()를 호출하지 않은 경우
    - **구성 요소** : R2, S6, P1
    - **설명** : FileInputStream을 생성한 뒤 테스트가 끝나기 전 try블락 안에 exception이 throw될 수 있는  메서드를 사용한 이후, finally 블록 안에 FileInputStream의 close()를 호출하지 않은 경우
    - **출처**
        - JCR-267

23. Test Suite 에서 하나의 DB 를 사용하고, Table 이나 Sequence 를 생성한 뒤 Drop 하지 않은 경우
    - **구성 요소** : R3, S2, S5, P2
    - **설명** : ‘alpha_seq’ 라는 sequence가 만들어지고 drop 되지 않아, 다음 테스트의 실행에 영향을 미친다.
    - **출처**
        - DERBY-4393

24. Socket 을 생성하고 테스트가 끝나는 시점에 close() 하지 않은 경우
    - **구성 요소** : R3, S6, P1
    - **설명** : socket을 생성하고 close를 하지 않아 테스트가 실패한다.
    - **출처**
        - HDFS-7282

25. 테스트 실행 도중 공유 변수의 값을 임의로 변경한 후 되돌리지 않음
    - **구성 요소** : R3, S6, P1, P2
    - **설명** : 이전 테스트에서의 설정이 다음 테스트에서도 적용되어 테스트 결과가 바뀌는 경우이다.
    - **출처**
        - FLUME-571
        - ACCUMULO-2198

26. Ant 1.9.3 에서 ant 가 test 결과를 file 을 수정할 수 있는 권한을 설정하지 않음
    - **구성 요소** : R3, S7, P4
    - **설명** : Ant 1.9.3 버전부터 Test result를 file에 쓰기 위해 ant.jar에 write permission이 필요해졌는데 관련 권한 파일 설정이 추가되지 않았다.
    - **출처**
        - DERBY-6685
27. 모든 test 가 실행되는지 확인하지 않음
    - **구성 요소** : R4, S1, P4
    - **설명** : 테스트를 실행시키는 부분에서 실제 의도하는대로 테스트가 실행되는지 확인하는 코드가 추가되지 않았다.
    - **출처**
        - JENA-795

28. test 가 추가되기 전에 호출되어야 할 메소드 leasechecker.interruptAndJoin()이 누락됨
    - **구성 요소** : R4, S2, P1
    - **설명** : Test가 추가되기전에 반드시 호출되어야할 메소드가 누락되었다.
    - **출처**
        - HDFS-824

29. 테스트 환경을 설정하는 동일한 메소드를 연속해 중복 실행하는 경우
    - **구성 요소** : R4, S2, P1
    - **설명** : 테스트 환경을 설정하는 메소드를 연속해서 중복 호출하엿다.
    - **출처**
        - HBASE-11698

30. setup() 이나 teardown()에서 Exception 을 catch 하지 않음
    - **구성 요소** : R4, S2, S6, P2
    - **설명** : 테스트 환경을 초기화하는 메서드에서 적절한 오류 처리가 이루어지지 않았다.
    - **출처**
        - JCR-505

31. Test Method 내부에서 Assertion 메소드의 호출이 없는 경우
    - **구성 요소** : R4, S5, P1
    - **설명** : 테스트에서 Assertion 메소드가 없는 경우이다. NullPointerException을 기대하는 경우 assertNotNull을 사용하는게 더 낫다.
     [JUnitTestsShouldIncludeAssert](https://pmd.github.io/pmd-6.0.0/pmd_rules_java_bestpractices.html#junittestsshouldincludeassert)
    - **출처**
        - [Fb-contrib - UTAO_JUNIT_ASSERTION_ODDITIES_NO_ASSERT](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_JUNIT_ASSERTION_ODDITIES_NO_ASSERT)
        - [Fb-contrib - UTAO_TESTNG_ASSERTION_ODDITIES_NO_ASSERT](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_TESTNG_ASSERTION_ODDITIES_NO_ASSERT)

32. assertNull() 안에 boxed primitive 가 파라미터로 넘겨지는 경우
    - **구성 요소** : R4, S5, P1
    - **설명** : Assertion method의 두번째 파라미터에 constant value를 넘긴 경우이다. 두번째 파라미터는 테스트하는 대상의 reference를 파라미터로 넘겨야 한다.
    - **출처**
        - [Fb-contrib - UTAO_JUNIT_ASSERTION_ODDITIES_ACTUAL_CONSTANT](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_JUNIT_ASSERTION_ODDITIES_ACTUAL_CONSTANT)
        - [Fb-contrib -   UTAO_TESTING_ASSERTION_ODDITIES_ACTUAL_CONSTANT](http://fb-contrib.sourceforge.net/UTAO_TESTING_ASSERTION_ODDITIES_ACTUAL_CONSTANT)

33. Test Framework를 사용하는 동시에 assert()를 사용하는 경우
    - **구성 요소** : R4, S5, P1
    - **설명** : Test framework를 사용하는 경우 framework에서 제공하는 assertion method를 사용해야한다.
    - **출처**
        - [ErrorProne - UseCorrectAssertInTests](http://errorprone.info/bugpattern/UseCorrectAssertInTests)
        - [Fb-contrib - UTAO_JUNIT_ASSERTION_ODDITIES_ASSERT_USED](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_JUNIT_ASSERTION_ODDITIES_ASSERT_USED)
        - [Fb-contrib - UTAO_TESTNG_ASSERTION_ODDITIES_ASSERT_USED](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_TESTNG_ASSERTION_ODDITIES_ASSERT_USED)

34. Assertion method 에서 동일한 객체의 reference 가 서로 같은 지 확인하는 경우
    - **구성 요소** : R4, S5, P1
    - **설명** : 같은 객체에대해 reference가 같은지 확인하는 경우 assertion은 반드시 pass하게 된다.
    - **출처**
        - [ErrorProne - JUnitAssertSameCheck](http://errorprone.info/bugpattern/JUnitAssertSameCheck)

35. Mockito 를 사용하는 테스트에서 verify 메소드를 사용하지 않음
    - **구성 요소** : R4, S5, P1
    - **설명** : Mockito를 사용하는 테스트에서 verify 메소드를 사용하지 않아, 테스트가 항상 성공하게 된다.
    - **출처** :
        - [ErrorProne - MockitoUsage](http://errorprone.info/bugpattern/MockitoUsage)

36. Assertion 메소드를 사용하여 동일한 객체에 대해 equality 를 검증하는 경우
    - **구성 요소** : R4, S5, P1
    - **설명** :  같은 객체에대해 equality를 검증하는 경우 항상 pass하게 된다.
    - **출처**
        - [ErrorProne - TruthSelfEquals](http://errorprone.info/bugpattern/TruthSelfEquals)
        - [ErrorProne - SelfEquals](http://errorprone.info/bugpattern/SelfEquals)

37. IOException 를 발생시키는 메소드를 assertNull()에서 호출함
  - **구성 요소** : R4, S5, P1
  - **설명** : 테스트 대상 코드가 null을 리턴하는 것에서 IOException을 throw하는 것으로 변경되어 Test code도 변경되어야 하는데 변경되지 않아 Test가 실패한다.
  - **출처**
      - MAPREDUCE-5421

38. Thread.run() 내부에 assertion 이 있는 경우
    - **구성 요소** : R4, S7, P1
    - **설명** : JUnit의 경우, Test를 실행하는 스레드가 assertion method를 실행해야 한다. 그렇지 않은 경우 test의 실패로 이어지지 않는다.
    - **출처**
        - [findBugs - IJU: JUnit assertion in run method will not be noticed by JUnit (IJU_ASSERT_METHOD_INVOKED_FROM_RUN_METHOD)](http://findbugs.sourceforge.net/bugDescriptions.html#IJU_ASSERT_METHOD_INVOKED_FROM_RUN_METHOD)

39. Assertion 메소드에서 test 하고자 하는 객체가 상수인 경우
    - **구성 요소** : R4, R5, S5, P1
    - **설명** : JUnit의 assertEquals는 파라미터로 기대하는 값(상수)을 먼저, 실제 테스트 하고자 하는 값을 다음에 받아야 한다.
    - **출처**
        - [ErrorProne - AssertEqualsArgumentOrderChecker](http://errorprone.info/bugpattern/AssertEqualsArgumentOrderChecker)
        - [ErrorProne - TruthConstantAsserts](http://errorprone.info/bugpattern/TruthConstantAsserts)
        - [Fb-contrib - UTAO_JUNIT_ASSERTION_ODDITIES_ACTUAL_CONSTANT](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_JUNIT_ASSERTION_ODDITIES_ACTUAL_CONSTANT)
        - [Fb-contrib - UTAO_TESTNG_ASSERTION_ODDITIES_ACTUAL_CONSTANT](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_TESTNG_ASSERTION_ODDITIES_ACTUAL_CONSTANT)

40. assert 구문에서 타입이 다른 두 객체의 equality 를 비교하는 경우
    - **구성 요소** : R4, R6, S5, P1
    - **설명** : Truth framework에서 서로 타입이 다른 두 객체의 equality 를 비교하는 경우이다.
    - **출처**
        - [ErrorProne - TruthIncompatibleType](http://errorprone.info/bugpattern/TruthIncompatibleType)

41. assertionEquals()에서 부동 소수점 비교에서 오차 허용 범위가 없는 경우
    - **구성 요소** : R5, S5, P1
    - **설명**
        - JUnit3에서 오류 허용 범위 없이 assertionEquals()를 사용할 수 있다. 부동소수점의 특성상 오류 허용 범위 없이 비교를 할 때 의도하지 않았던 테스트의 실패로 이어질 수 있다.
    - **출처**
        - [Fb-contrib - UTAO_JUNIT_ASSERTION_ODDITIES_INEXACT_DOUBLE](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_JUNIT_ASSERTION_ODDITIES_INEXACT_DOUBLE)
        - [Fb-contrib - UTAO_TESTNG_ASSERTION_ODDITIES_INEXACT_DOUBLE](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_TESTNG_ASSERTION_ODDITIES_INEXACT_DOUBLE)
        - [ErrorProne - JUnit3FloatingPointComparisonWithoutDelta](http://errorprone.info/bugpattern/JUnit3FloatingPointComparisonWithoutDelta)

42. Try 블록 내부에서 fail()이 있으나, catch 블록에서 assertionError 를 catch 하는 경우
    - **구성요소** : R5, S5, P1
    - **설명** : try block 안에서 fail()을 호출하고, assertionError catch하는 경우, assertion error가 catch하여, 테스트가 fail하지 않게 된다.
    - **출처**
        - [ErrorProne - AssertionFailureIgnored](http://errorprone.info/bugpattern/AssertionFailureIgnored)

43. assertTrue() 내부의 판별식에서 equals 를 호출하여 두 객체가 같음을 확인하는 경우
    - **구성 요소** : R6, S5, P1
    - **설명** : AssertTrue() eqauls를 사용하여 두 객체가 같은지 확인하고 있다. 이를 위해 만들어진 AssertSame()을 사용하는 것이 좋다.
    - **출처**
        - [PMD - UseAssertEqualsInsteadOfAssertTrue](https://pmd.github.io/pmd-6.0.0/pmd_rules_java_bestpractices.html#useassertequalsinsteadofasserttrue)
        - [Fb-contrib - UTAO_JUNIT_ASSERTION_ODDITIES_USE_ASSERT_EQUALS](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_JUNIT_ASSERTION_ODDITIES_USE_ASSERT_EQUALS)
        - [Fb-contrib - UTAO_TESTNG_ASSERTION_ODDITIES_USE_ASSERT_EQUALS](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_JUNIT_ASSERTION_ODDITIES_USE_ASSERT_EQUALS)
44. assertTrue() 내부의 판별식에서 test 하고자 하는 객체가 null 인지를 확인하는 경우
    - **구성 요소** : R6, S5, P1
    - **설명** : AssertTrue() 내부에 null과의 비교하는 연산 사용하여 객체가 null인지 확인하고 있다. 이를 위해 만들어진 AssertNull()을 사용하는 것이 좋다.
    - **출처**
        - [PMD - UseAssertNullInsteadAssertTrue](https://pmd.github.io/pmd-6.0.0/pmd_rules_java_bestpractices.html#useassertnullinsteadofasserttrue)
        - [Fb-contrib - UTAO_JUNIT_ASSERTION_ODDITIES_USE_ASSERT_NULL](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_JUNIT_ASSERTION_ODDITIES_USE_ASSERT_NULL)
        - [Fb-contrib - UTAO_TESTNG_ASSERTION_ODDITIES_USE_ASSERT_NULL](http://fb-contrib.sourceforge.net/bugdescriptions.html#UTAO_TESTNG_ASSERTION_ODDITIES_USE_ASSERT_NULL)
