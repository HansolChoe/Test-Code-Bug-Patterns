## Test Code Bug Patterns
---
### JUnit3 에서 TestCase 클래스에 test method 가 없는 경우
**구성 요소** : R1, S1, P1

**출처** :
- [FindBugs - IJU: TestCase has no tests (IJU_NO_TESTS)](http://findbugs.sourceforge.net/bugDescriptions.html#IJU_NO_TESTS)

### JUnit3 에서 suite() 선언이 잘못된 경우

**구성 요소** : R1, S1, P2

**출처** :
- [FindBugs - IJU: TestCase declares a bad suite method (IJU_BAD_SUITE_METHOD)](http://findbugs.sourceforge.net/bugDescriptions.html#IJU_BAD_SUITE_METHOD)
- [FindBugs - IJU: TestCase implements a non-static suite method (IJU_SUITE_NOT_STATIC)](http://findbugs.sourceforge.net/bugDescriptions.html#IJU_SUITE_NOT_STATIC)
- [PMD - JUnitStaticSuite](https://pmd.github.io/pmd-6.0.0/pmd_rules_java_errorprone.html#junitstaticsuite)

### JUnit4 에서 suite()를 오버라이드한 경우
**구성 요소** : R1 S1 P2

- [PMD - JUnit4SuitesShouldUseSuiteAnnotation ](https://pmd.github.io/pmd-6.0.0/pmd_rules_java_bestpractices.html#junit4suitesshouldusesuiteannotation)

### JUnit4 에서 test method 에 @Test Annotation이 없는 경우
**구성 요소** : R1 S1 P4

- [PMD - JUnit4TestShouldUseTestAnnotation](https://pmd.github.io/pmd-6.0.0/pmd_rules_java_bestpractices.html#junit4testshouldusetestannotation)
- [ErrorProne - JUnit4TestNotRun](http://errorprone.info/bugpattern/JUnit4TestNotRun)

### JMock 객체를 사용하면서 동시에, 해당 Test Class 에서 JMock Runner를 사용하지 않음
**구성 요소** : R1 S1 P4

- [ErrorProne - JMockTestWithoutRunWithOrRuleAnnotation](http://errorprone.info/bugpattern/JMockTestWithoutRunWithOrRuleAnnotation)

### testPath 를 잘못 설정함
**구성 요소** : R1 S2 P1

- [JCR-524]()

### JUnit3 에서 setUp()이 오버라이드되어 있으나, super.setUp() 을 호출하지 않음
**구성 요소** : R1 S2 P3

[FindBugs - IJU: TestCase defines setUp that doesn't call super.setUp() (IJU_SETUP_NO_SUPER)](http://findbugs.sourceforge.net/bugDescriptions.html#IJU_SETUP_NO_SUPER)

### JUnit4 에서 setUp()을 정의하고 @Before Annotation이 없는 경우
**구성 요소** : R1 S2 P4

- [PMD - JUnit4TestShouldUseBeforeAnnotation](https://pmd.github.io/pmd-6.0.0/pmd_rules_java_bestpractices.html#junit4testshouldusebeforeannotation)
- [ErrorProne - JUnit4SetUpNotRun](http://errorprone.info/bugpattern/JUnit4SetUpNotRun)

### JUnit4 의 @AfterClass나 @BeforeClass annotation을 사용하면서 method가 static이 아닌 경우
**구성 요소** : R1 S2 P4

- [ErrorProne - JUnit4ClassAnnotationNonStatic](http://errorprone.info/bugpattern/JUnit4ClassAnnotationNonStatic)

### JUnit3 에서 약속된 메소드 명이 잘못된 경우
R1 S2, S6 P1

[ErrorProne - JUnit4ClassUsedInJUnit3](http://errorprone.info/bugpattern/JUnit4ClassUsedInJUnit3)
[ErrorProne - JUnitAmbiguousTestClass](http://errorprone.info/bugpattern/JUnitAmbiguousTestClass)

### JDK9 로 테스트 코드를 컴파일했을 때 오류가 나는 Mockito code pattern 을 사용한 경우

R1 S3 P1

- [ErrorProne - MockitoCast](http://errorprone.info/bugpattern/MockitoCast)

### 연속으로 동일한 Assertion 을 호출함

R1 S5 P1

DERBY-6716

### FileInputStream 을 생성하였으나 close()를 호출하지 않음

R1 S6 P1

FLUME-349

### JUnit3 에서 tearDown()이 오버라이드되어 있으나, super.tearDown() 을 호출하지 않음

R1 S6 P3


- [FindBugs - IJU: TestCase defines tearDown that doesn't call super.tearDown() (IJU_TEARDOWN_NO_SUPER)](http://findbugs.sourceforge.net/bugDescriptions.html#IJU_TEARDOWN_NO_SUPER)

### JUnit4 에서 tearDown()을 정의하고 @After Annotation 이 없는 경우
R1 S6 P4

[PMD - JUnit4TestShouldUseAfterAnnotation](https://pmd.github.io/pmd-6.0.0/pmd_rules_java_bestpractices.html#junit4testshoulduseafterannotation)
[ErrorProne - JUnit4TearDownNotRun](http://errorprone.info/bugpattern/JUnit4TearDownNotRun)

### 테스트 내에서 Thread 를 생성했으나 join()을 하지 않고 테스트가 종료됨

R1, R2 S2, S3, S6 P1

DERBY-5708

### org.junit.Test 를 import 하는 java 파일의 public class 명이 Test 로 시작되지 않음

R1, R3 S1 P4

SLIDER-41

### Exception 이 기대한 테스트케이스에서 Exception 이 발생하지 않아도 fail() 하지 않음

R1, R4 S5 P1

DERBY-6088
DERBY-3852

### SSLContext 의 getInstance 메소드에 "SSL"을 파라미터로 넘긴 경우 R2 S2, S3 P1 FLUME-2441
file path 의 경로를 '/'으로만 구분하는 경우 R2 S3 P1 MAPREDUCE-4983
### assertThrows 에 넘겨진 코드에서 Exception 을 throw 한 이후 statement 가 남아있음
R2 S4 P1
- [ErrorProne - AssertThrowsMultipleStatements](http://errorprone.info/bugpattern/AssertThrowsMultipleStatements)

### for 문의 조건문을 잘못 입력하여 test 를 실행하지 못한 경우
R2 S4 P1 HDFS-2596
### 파일을 생성한 뒤 try-catch 블록 이후 finally 블록에 close()를 호출하지 않은 경우
 R2 S6 P1 JCR-267
### Test Suite 에서 하나의 DB 를 사용하고, Table 이나 Sequence 를 생성한 뒤 Drop 하지 않은 경우
 R3 S2, S5 P2 DERBY-4393
### Socket 을 생성하고 테스트가 끝나는 시점에 close() 하지 않은 경우
 R3 S6 P1 HDFS-7282
### 테스트 실행 도중 공유 변수의 값을 임의로 변경한 후 되돌리지 않음
 R3 S6 P1, P2 FLUME-571, ACCUMULO-2198
### Ant 1.9.3 에서 ant 가 test 결과를 file 을 수정할 수 있는 권한을 설정하지 않음
 R3 S7 P4 DERBY-6685
### 모든 test 가 실행되는지 확인하지 않음
 R4 S1 P4 JENA-795
### test 가 추가되기 전에 호출되어야 할 메소드 leasechecker.interruptAndJoin()이 누락됨
 R4 S2 P1 HDFS-824
### 테스트 환경을 설정하는 동일한 메소드를 연속해 중복 실행하는 경우
 R4 S2 P1 HBASE-11698
### setup() 이나 teardown()에서 Exception 을 catch 하지 않음
 R4 S2, S6 P2 JCR-505
### Test Method 내부에서 Assertion 메소드의 호출이 없는 경우
R4 S5 P1

- [PMD - JUnitTestsShouldIncludeAssert](https://pmd.github.io/pmd-6.0.0/pmd_rules_java_bestpractices.html#junittestsshouldincludeassert)
- Fb-contrib

assertNull() 안에 boxed primitive 가 파라미터로 넘겨지는 경우 R4 S5 P1 Fb-contrib
### Test Framework를 사용하는 동시에 assert()를 사용하는 경우
R4 S5 P1
- [ErrorProne - UseCorrectAssertInTests](http://errorprone.info/bugpattern/UseCorrectAssertInTests)
- Fb-contrib

### Assertion method 에서 동일한 객체의 reference 가 서로 같은 지 확인하는 경우
R4 S5 P1
- [ErrorProne - JUnitAssertSameCheck](http://errorprone.info/bugpattern/JUnitAssertSameCheck)

### Mockito 를 사용하는 테스트에서 verify 메소드를 사용하지 않음
R4 S5 P1
- [ErrorProne - MockitoUsage](http://errorprone.info/bugpattern/MockitoUsage)

### Assertion 메소드를 사용하여 동일한 객체에 대해 equality 를 검증하는 경우
R4 S5 P1
[ErrorProne - TruthSelfEquals](http://errorprone.info/bugpattern/TruthSelfEquals)
[ErrorProne - SelfEquals](http://errorprone.info/bugpattern/SelfEquals)

### IOException 를 발생시키는 메소드를 assertNull()에서 호출함 R4 S5 P1 MAPREDUCE-5421

### Thread.run() 내부에 assertion 이 있는 경우

R4 S7 P1

- [findBugs - IJU: JUnit assertion in run method will not be noticed by JUnit (IJU_ASSERT_METHOD_INVOKED_FROM_RUN_METHOD)](http://findbugs.sourceforge.net/bugDescriptions.html#IJU_ASSERT_METHOD_INVOKED_FROM_RUN_METHOD)

### Assertion 메소드에서 test 하고자 하는 객체가 상수인 경우
R4, R5 S5 P1

- [ErrorProne - AssertEqualsArgumentOrderChecker](http://errorprone.info/bugpattern/AssertEqualsArgumentOrderChecker)
- [ErrorProne - TruthConstantAsserts](http://errorprone.info/bugpattern/TruthConstantAsserts)
- Fb-contrib

### assert 구문에서 타입이 다른 두 객체의 equality 를 비교하는 경우
R4, R6 S5 P1
[ErrorProne - TruthIncompatibleType](http://errorprone.info/bugpattern/TruthIncompatibleType)

### assertionEquals()에서 부동 소수점 비교에서 오차 허용 범위가 없는 경우 R5 S5 P1
- Fb-contrib
- [ErrorProne - JUnit3FloatingPointComparisonWithoutDelta](http://errorprone.info/bugpattern/JUnit3FloatingPointComparisonWithoutDelta)
### Try 블록 내부에서 fail()이 있으나, catch 블록에서 assertionError 를 catch 하는 경우 R5 S5 P1
- [ErrorProne - AssertionFailureIgnored](http://errorprone.info/bugpattern/AssertionFailureIgnored)

### assertTrue() 내부의 판별식에서 equals 를 호출하여 두 객체가 같음을 확인하는 경우

R6 S5 P1

- [PMD - UseAssertEqualsInsteadOfAssertTrue](https://pmd.github.io/pmd-6.0.0/pmd_rules_java_bestpractices.html#useassertequalsinsteadofasserttrue)
- Fb-contrib

### assertTrue() 내부의 판별식에서 test 하고자 하는 객체가 null 인지를 확인하는 경우

R6 S5 P1

- [PMD - UseAssertNullInsteadAssertTrue](https://pmd.github.io/pmd-6.0.0/pmd_rules_java_bestpractices.html#useassertnullinsteadofasserttrue)
- Fb-contrib
