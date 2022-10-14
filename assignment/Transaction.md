## Spring framework에서의 트랜잭션

### @Transactional
스프링은 @Transactional 어노테이션이 달린 모든 클래스에 대한 프록시를 생성한다.
```java
@Service
@Transactional
public class FooService { ... }
```
- 프록시를 사용하면 트랜잭션을 시작하고 커밋하기 위해서 실행 방법 전후에 트랜잭션 로직을 주입할 수 있다.
- public 접근제어자가 아닌 경우에 @Transactional 어노테이션을 사용하면 단순히 주석처리가 됨으로 주의해야 한다.

<br>

### Isolation level 변경
아래의 방법은 Spring 4.1부터 사용 가능하다.
```java
@Transactional(isolation = Isolation.SERIALIZABLE)
```

<br>

### Read-only Transactions
readOnly 속성을 해석하지 못하는 트랜잭션 매니저에서는 이 속성이 의미가 없는데
JPA는 어떤 벤더를 사용할지 모르기 때문에 쓰기 동작이 일어나지 않는다고 보장할 수가 없다.

<br>

### Logging
스프링에서 트랜잭션 관련 패키지는 "org.springframework.transaction"이고 로깅 레벨은 TRACE로 설정되어야 한다.

<br>

### Rollback
프로그래밍 방식과 선언적 접근 방식이 있다.

<br>

**선언적 접근 방식**

선언적 접근 방식에서는 기본적으로 런타임 예외에 대해 롤백한다.

@Transactional 어노테이션에 rollbackFor 혹은 rollbackForClass 속성을 사용해서 롤백하거나
noRollbackForclassName 속성으로 나열된 예외에 대한 롤백을 방지할 수 있다.

<br>

***rollbackFor***속성을 활용해서 SQLException 예외가 발생하면 롤백을 할 수 있도록 설정한다.
```java
@Transactional(rollbackFor = { SQLException.class })
public void createCourseDeclarativeWithCheckedException(Course course) throws SQLException {
    courseDao.create(course);
    throw new SQLException("Throwing exception for demoing rollback");
}
```

<br>

***noRollbackFor***속성을 활용해서 SQLException이 발생해도 롤백하지 않도록 설정한다.
```java
@Transactional(noRollbackFor = { SQLException.class })
public void createCourseDeclarativeWithCheckedException(Course course) throws SQLException {
    courseDao.create(course);
    throw new SQLException("Throwing exception for demoing rollback");
}
```

<br>

**프로그래밍 방식**
```java
public void createCourseDefaultRatingProgramatic(Course course) {
    try {
       courseDao.create(course);
    } catch (Exception e) {
       TransactionAspectSupport.currentTransactionStatus().setRollbackOnly();
    }
}
```

