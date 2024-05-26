# 08_Spring Data JPA 활용

## 1. JPQL

JPQL은 JPA Query Language의 줄임말로 JPA에서 사용할 수 있는 쿼리를 의미합니다. SQL과의 차이점은 SQL에서는 테이블이나 칼럼의 이름을 사용하는 것과 달리 JPQL은 엔티티 객체를 대상으로 수행하는 쿼리이기 때문에 매핑된 엔티티의 이름과 필드의 이름을 사용한다는 것입니다.

## 2. 쿼리 메서드 살펴보기

간단한 쿼리문을 작성하기 위해 사용되는 것이 쿼리 메서드입니다. 쿼리 메서드는 크게 동작을 결정하는 주제와 서술어로 구분합니다. BY를 기준으로 앞쪽은 쿼리의 주제이고 뒤쪽은 서술어의 시작을 나타냅니다. 서술어 부분은 검색 및 정렬 조건을 지정하는 영역입니다. 

쿼리 메서드는 Spring Data JPA에서 제공하는 메서드 네이밍 규칙을 활용하여 데이터베이스에서 데이터를 조회하는 방법 중 하나입니다. 쿼리 메서드는 메서드 이름을 통해 데이터베이스 쿼리를 생성하므로, 별도의 JPQL이나 Native Query를 작성하지 않고도 간단한 쿼리를 실행할 수 있습니다.

쿼리 메서드의 구성은 다음과 같습니다:

1. **쿼리의 주제 (Subject)**: 메서드 이름의 첫 부분에 위치하며, 메서드가 무엇을 조회할지를 나타냅니다. 주로 엔티티의 속성이나 관련된 엔티티의 속성을 사용합니다.

2. **서술어 (Predicate)**: 메서드 이름의 뒷 부분에 위치하며, 주제에 대한 조건을 지정합니다. 검색 조건이나 정렬 조건을 포함합니다.

예를 들어, `findByFirstNameAndLastName(String firstName, String lastName)` 메서드는 `firstName`과 `lastName`이라는 속성을 기반으로 특정 조건을 만족하는 데이터를 조회합니다. 이 메서드는 'firstName'과 'lastName'이라는 속성을 가진 엔티티를 찾는 것을 주제로 하고, 주어진 firstName과 lastName 값으로 데이터를 필터링합니다 (서술어).

## 3. 예제

1. **exists...By**

```java   
    boolean existsByUsername(String username);
```    
이 메서드는 주어진 username에 해당하는 엔티티가 존재하는지 확인합니다. 만약 해당 username을 가진 엔티티가 존재하면 `true`를, 그렇지 않으면 `false`를 반환합니다.


2. **count...By**
    
```java
    long countByAge(int age);
```  
이 메서드는 주어진 나이(age)에 해당하는 엔티티의 개수를 조회합니다. 예를 들어, 20세인 사용자의 수를 조회하고자 할 때 사용할 수 있습니다.


3. **delete...By, remove...By**

```java
    void deleteByEmail(String email);
```    
이 메서드는 주어진 이메일 주소를 가진 엔티티를 삭제합니다. 리턴 타입이 `void`이므로 삭제된 엔티티의 개수를 반환하지 않습니다.


4. **...First<number>..., ...Top<number>...**

```java
    List<User> findFirst3ByOrderByLastNameAsc();
```
이 메서드는 성(lastName)을 기준으로 오름차순으로 정렬된 상위 3개의 사용자를 조회합니다. 반환 타입이 `List<User>`이므로 결과를 리스트로 반환합니다. 만약 상위 한 명의 사용자만을 조회하려면 `findFirstByOrderByLastNameAsc()`와 같이 `<number>`를 생략할 수 있습니다.

## 4. 쿼리 메서드의 조건자 키워드

1. **Is**

값의 일치를 조건으로 사용합니다. 일반적으로 생략되며, Equals와 동일한 기능을 합니다.

```java    
    User findByUsername(String username);
```    
이 메서드는 주어진 username과 일치하는 엔티티를 조회합니다. Is 키워드가 생략되었지만, 메서드 이름으로부터 username이 일치하는지 확인하는 것을 알 수 있습니다.


2. **(Is)Not**

 값의 불일치를 조건으로 사용합니다. Is는 생략 가능합니다.

 ```java   
    List<User> findByAgeNot(int age);
 ```   
이 메서드는 주어진 나이(age)와 일치하지 않는 엔티티들을 조회합니다. Is 키워드가 생략되었지만, 메서드 이름으로부터 age가 일치하지 않는 것을 알 수 있습니다.


3. **(Is)Null, (Is)NotNull**

값이 null인지 아닌지를 검사합니다.

```java    
    List<User> findByEmailIsNull();
    List<User> findByPhoneNumberIsNotNull();
```    
첫 번째 메서드는 이메일이 null인 사용자들을 조회하고, 두 번째 메서드는 전화번호가 null이 아닌 사용자들을 조회합니다.


4. **(Is)True, (Is)False**

boolean 타입으로 지정된 칼럼값이 참(true)인지 거짓(false)인지를 확인합니다.

```java    
    List<Payment> findByIsPaidTrue();
    List<Task> findByIsCompletedFalse();
```   
첫 번째 메서드는 결제가 완료된(payment.isPaid == true) 결제 내역을 조회하고, 두 번째 메서드는 완료되지 않은(task.isCompleted == false) 업무(task)를 조회합니다.


5. **And, Or**

여러 조건을 묶을 때 사용합니다.

```java
    List<User> findByFirstNameAndLastName(String firstName, String lastName);
    List<User> findByAgeOrCity(int age, String city);
```

첫 번째 메서드는 주어진 firstName과 lastName이 모두 일치하는 사용자를 조회하고, 두 번째 메서드는 주어진 age 또는 city 중 하나라도 일치하는 사용자를 조회합니다.


6. **(Is)GreaterThan, (Is)LessThan, (Is)Between**

숫자나 날짜시간 칼럼을 대상으로 한 비교연산에 사용됩니다. 경계값을 포함하려면 Equal 키워드를 추가하면 됩니다.

 ```java   
    List<Order> findByTotalPriceGreaterThan(double price);
    List<Product> findByStockQuantityLessThan(int quantity);
    List<Employee> findByStartDateBetween(Date startDate, Date endDate);
 ```

첫 번째 메서드는 주어진 price보다 totalPrice가 큰 주문을 조회하고, 두 번째 메서드는 주어진 quantity보다 stockQuantity가 작은 상품을 조회합니다. 세 번째 메서드는 startDate와 endDate 사이에 입사한 직원들을 조회합니다.


7. **(Is)StartingWith(==StartsWith), (Is)EndingWith(==EndsWith), (Is)Containing(==Contains), (Is)Like**

문자열의 특정 패턴과 일치 여부를 확인합니다. Containing은 문자열의 양 끝, StartingWith 키워드는 문자열의 앞, EndingWith 키워드는 문자열의 끝에 '%'가 배치됩니다.
   (Is)Containing(==Contains), (Is)Like**

```java
    List<Customer> findByFirstNameStartingWith(String prefix);
    List<Document> findByTitleEndingWith(String suffix);
    List<Book> findByAuthorContaining(String keyword);
    List<Article> findByContentLike(String pattern);
```  

각각의 메서드는 firstName이 특정 prefix로 시작하는 고객을 조회하거나, title이 특정 suffix로 끝나는 문서를 조회하거나, author에 특정 keyword를 포함하는 책을 조회하거나, content가 특정 pattern을 포함하는 글을 조회합니다.
    
이러한 키워드들을 조합하여 쿼리 메서드를 작성할 때 사용할 수 있습니다. 이것은 메서드 이름 자체를 쿼리로 해석하여 해당하는 데이터를 검색하게 됩니다.

## 5. 정렬

일반적인 쿼리문에서 정렬을 사용할 때는 order by 구문을 사용합니다. 쿼리 메서드도 정렬 기능에 동일한 키워드가 사용됩니다. 다른 쿼리 메서드들은 조건을 여러개 사용하기 위해 And와 Or 키워드를 사용했지만 정렬 구문은 우선순위를 기준으로 차례대로 작성하면 됩니다.

메서드의 이름이 길어질수록 가독성이 떨어지는 문제가 생기는데 이를 해결하기 위해 매개변수를 활용해 정렬할 수도 있습니다. 또는 Sort 부분을 하나의 메서드로 분리해서 쿼리 메서드를 호출하는 코드를 작성하는 방법도 가능합니다.

예를 들어, 만약 `findBy`로 시작하는 메서드를 사용하고 있다면, 이후의 속성 이름에 따라 정렬이 가능합니다.

```java
    List<User> findByOrderByLastNameAsc();
```

위의 예시는 lastName을 기준으로 오름차순으로 정렬된 사용자들을 조회합니다. 여러 속성에 대해 정렬을 적용하려면, 속성 이름 사이에 `OrderBy`를 추가하여 사용합니다.

또한, 매개변수를 활용하여 동적으로 정렬할 수도 있습니다.

```java
    List<User> findByAge(int age, Sort sort);
```

이런 경우에는 메서드를 호출할 때 정렬 기준을 지정할 수 있습니다.

```java
    Sort sort = Sort.by(Sort.Direction.DESC, "lastName");
    List<User> users = userRepository.findByAge(30, sort);
```

이렇게 하면 30세인 사용자들을 나이에 따라 정렬하되, 성(lastName)을 기준으로 내림차순으로 정렬된 결과를 얻게 됩니다.

마지막으로, 별도의 메서드로 정렬을 수행하여 가독성을 높일 수도 있습니다.

```java
    List<User> findByAge(int age);
    List<User> findByAge(int age, Sort sort);
```

위와 같이 두 개의 메서드를 정의하고, 필요에 따라 정렬을 수행하는 방법도 있습니다. 이렇게 함으로써 호출하는 쪽에서는 정렬을 고려할 필요가 없습니다.

## 6. 페이징 처리

JPA에서는 데이터를 페이지 단위로 처리하기 위해 `Page`와 `Pageable` 인터페이스를 제공합니다. `Page`는 페이징된 데이터의 집합을 나타내며, `Pageable`은 페이징에 대한 정보를 나타내는 인터페이스입니다.

`PageRequest`는 `Pageable`의 구현체 중 하나로, 페이징 요청을 생성할 때 사용됩니다. `PageRequest.of()` 메서드를 사용하여 `PageRequest` 객체를 생성할 수 있습니다. 이 메서드는 페이지 번호, 페이지 크기, 정렬 조건 등을 매개변수로 받아서 `PageRequest` 객체를 생성합니다.

예를 들어, 1페이지부터 10개의 항목을 가져오고 싶을 때는 다음과 같이 `PageRequest.of()`를 사용할 수 있습니다.

```java
    Pageable pageable = PageRequest.of(0, 10);
```

여기서 0은 첫 번째 페이지를 의미하고, 10은 페이지당 항목의 수를 의미합니다.

그리고 JPA의 쿼리 메서드를 통해 페이징 처리를 하려면 메서드의 반환 타입을 `Page`로 설정하고, 메서드의 매개변수로 `Pageable` 객체를 받아야 합니다.

```java
    Page<User> findAll(Pageable pageable);
```

위와 같이 `findAll()` 메서드를 사용하여 사용자를 페이지 단위로 조회할 수 있습니다. 반환되는 `Page` 객체에는 페이징된 사용자 데이터가 포함되어 있습니다. 페이지의 내용을 확인하려면 `getContent()` 메서드를 사용하여 페이지의 데이터를 배열 형태로 가져올 수 있습니다.

## 7. @Query 어노테이션 사용하기

데이터베이스에서 값을 가져올 때는 쿼리 메서드를 생성할 수도 있고 @Query 어노테이션을 사용해 직접 JPQL을 작성할 수도 있습니다. JPQL을 사용하면 JPA 구현체에서 자동으로 쿼리 문장을 해석하고 실행하게 됩니다.

파라미터를 바인딩하는 형식으로 메서드를 구현하면 코드의 가독성이 높아지고 유지보수가 수월해집니다.

그리고 @Query를 사용하면 엔티티 타입이 아니라 원하는 칼럼의 값만 추출할 수 있습니다. 

@Query 어노테이션은 직접 JPQL(Java Persistence Query Language) 쿼리를 작성하여 사용할 때 유용합니다. 이를 통해 개발자가 원하는 형태의 쿼리를 작성하고 실행할 수 있습니다.

예를 들어, 사용자 엔티티에서 특정 조건을 만족하는 사용자의 이름만을 추출하는 메서드를 만들어보겠습니다.

```java
    import org.springframework.data.jpa.repository.Query;
    import org.springframework.data.repository.Repository;
    
    import java.util.List;
    
    public interface UserRepository extends Repository<User, Long> {
    
        @Query("SELECT u.name FROM User u WHERE u.age > :age")
        List<String> findNamesByAgeGreaterThan(int age);
    }
```

위의 예제에서 `@Query` 어노테이션을 사용하여 직접 JPQL을 작성하였습니다. 이 쿼리는 `User` 엔티티에서 `age`가 특정 값보다 큰 사용자의 이름만을 선택합니다. 여기서 `:age`는 JPQL의 이름 매개변수로, 이를 메서드의 파라미터에 바인딩하여 사용할 수 있습니다.

이렇게 함으로써 엔티티 타입이 아닌 원하는 칼럼의 값만을 추출할 수 있습니다. 이는 필요한 경우에 데이터의 일부분만을 가져와야 할 때 유용합니다. 또한, 가독성이 높은 코드를 작성할 수 있어 유지보수도 용이합니다.

## 8. QueryDSL 적용하기

JPQL의 한계는 @Query 어노테이션을 통해 대부분 해소할 수 있지만 직접 문자열을 입력하기 때문에 컴파일 시점에 에러를 잡지 못하고 런타임 에러가 발생할 수 있습니다. 이를 위해 사용하는 것이 QueryDSL입니다. QueryDSL은 분자열이 아니라 코드로 쿼리를 작성할 수 있도록 도와줍니다.

QueryDSL은 정적 타입을 이용해 SQL과 같은 쿼리를 생성할 수 있도록 지원하는 프레임워크입니다. QueryDSL을 사용하면 다음과 같은 장점이 있습니다.

- 자동완성기능 제공
- 문법 오류 체킹 기능
- 동적 쿼리 생성
- 가독성 및 생산성 향상

## 9. QueryDSL을 사용하기 위한 프로젝트 설정

QueryDSL을 사용하기 위한 프로젝트 설정은 다음과 같습니다.

1. **의존성 추가**: 먼저, pom.xml 파일에 QueryDSL 관련 의존성을 추가해야 합니다.

```xml
    <dependency>
        <groupId>com.querydsl</groupId>
        <artifactId>querydsl-jpa</artifactId>
        <version>5.0.0</version>
    </dependency>
 ```       

2. **플러그인 설정**: QueryDSL은 Q타입 클래스를 생성하기 위해 플러그인을 사용합니다. Maven 프로젝트의 경우, `pom.xml`에 다음과 같이 QueryDSL 플러그인을 추가합니다.

```xml
    <build>
        <plugins>
            <plugin>
                <groupId>com.mysema.maven</groupId>
                <artifactId>apt-maven-plugin</artifactId>
                <version>1.1.3</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>process</goal>
                        </goals>
                        <configuration>
                            <outputDirectory>target/generated-sources/java</outputDirectory>
                            <processor>com.querydsl.apt.jpa.JPAAnnotationProcessor</processor>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
```    

3. **QueryDSL 사용하기**: QueryDSL을 사용하여 쿼리를 작성하려면 `JPAQuery` 객체를 사용합니다. 이를 위해 엔티티 매니저를 활용하여 JPAQuery 객체를 생성합니다.

    예를 들어, `User` 엔티티를 대상으로 한 쿼리를 작성해보겠습니다.

```java    
    import com.querydsl.jpa.impl.JPAQuery;
    
    import javax.persistence.EntityManager;
    import java.util.List;
    
    public class UserRepositoryImpl implements UserRepositoryCustom {
    
        private final EntityManager entityManager;
    
        public UserRepositoryImpl(EntityManager entityManager) {
            this.entityManager = entityManager;
        }
    
        @Override
        public List<User> findUsersWithAgeGreaterThan(int age) {
            QUser qUser = QUser.user;
            JPAQuery<User> query = new JPAQuery<>(entityManager);
            return query.select(qUser)
                        .from(qUser)
                        .where(qUser.age.gt(age))
                        .fetch();
        }
    }
```    

위의 예제에서는 `UserRepositoryCustom` 인터페이스를 구현하여 사용자 정의 쿼리 메서드를 정의하였습니다. `JPAQuery` 객체를 사용하여 QueryDSL을 이용하여 쿼리를 작성하고 실행할 수 있습니다.

4. **컨피그 클래스 생성**: QueryDSL을 실제 비즈니스 로직에서 활용하기 위해서는 컨피그 클래스를 생성하여 설정해야 합니다. 주로 QueryDSL 관련 빈을 정의하고, EntityManager나 QueryFactory 등을 주입합니다.

```java    
    import com.querydsl.jpa.impl.JPAQueryFactory;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    
    import javax.persistence.EntityManager;
    import javax.persistence.PersistenceContext;
    
    @Configuration
    public class QueryDSLConfig {
    
        @PersistenceContext
        private EntityManager entityManager;
    
        @Bean
        public JPAQueryFactory jpaQueryFactory() {
            return new JPAQueryFactory(entityManager);
        }
    }
```

이렇게 설정하면 QueryDSL을 사용하여 JPA 기반의 데이터베이스 쿼리를 더 쉽게 작성하고 실행할 수 있습니다.

## 10. QuerydslPredicateExecutor 인터페이스

`QuerydslPredicateExecutor` 인터페이스는 Spring Data JPA에서 제공하는 인터페이스 중 하나입니다. 이 인터페이스를 사용하면 리포지토리에서 QueryDSL을 사용하여 동적인 쿼리를 쉽게 작성할 수 있습니다. 그러나 이 인터페이스의 메서드는 대부분 `Predicate` 타입을 매개변수로 받습니다.

`Predicate`는 QueryDSL에서 조건을 표현하기 위한 인터페이스로, 여러 조건을 조합하여 동적인 쿼리를 생성할 수 있습니다. 하지만 `Predicate`를 사용하여 조건을 표현하면 join이나 fetch와 같은 기능을 사용할 수 없다는 단점이 있습니다. 이는 QueryDSL의 제약사항 중 하나입니다.

예를 들어, `QuerydslPredicateExecutor`를 사용하여 사용자 엔티티를 조회하는 메서드를 작성해보겠습니다.

```java
    import org.springframework.data.querydsl.QuerydslPredicateExecutor;
    import org.springframework.data.repository.Repository;
    
    public interface UserRepository extends Repository<User, Long>, QuerydslPredicateExecutor<User> {
    }
```

위의 예제에서는 `UserRepository` 인터페이스가 `QuerydslPredicateExecutor<User>` 인터페이스를 상속받고 있습니다. 따라서 `UserRepository`에서는 `Predicate`를 사용하여 동적인 쿼리를 작성할 수 있게 됩니다.

하지만, 이 방법을 사용할 때는 QueryDSL의 모든 기능을 활용할 수는 없으며, join이나 fetch와 같은 기능을 사용할 수 없다는 점을 주의해야 합니다. 만약 join이나 fetch와 같은 기능을 사용해야 할 경우에는 QueryDSL의 `JPAQuery`나 `JPAQueryFactory`를 사용하여 직접 쿼리를 작성해야 합니다.

## 11. QuerydslRepositorySupport 추상 클래스 사용하기

`QuerydslRepositorySupport` 클래스는 Spring Data JPA에서 제공하는 추상 클래스 중 하나입니다. 이 클래스를 사용하면 QueryDSL을 활용하여 리포지토리를 보다 유연하게 구현할 수 있습니다.

가장 보편적으로 사용하는 방식은 Custom Repository를 구현하는 것입니다. Custom Repository는 Spring Data JPA의 기본 리포지토리에 추가적인 메서드를 정의하여 사용할 수 있도록 합니다. `QuerydslRepositorySupport`를 활용하여 Custom Repository를 구현할 때는 다음과 같은 방식으로 사용할 수 있습니다.

1. 우선 Custom Repository를 구현할 인터페이스를 정의합니다.

```java
   import org.springframework.data.jpa.repository.JpaRepository;
   
   public interface CustomUserRepository {
       List<User> findUsersByAge(int age);
   }
```

2. 해당 인터페이스를 구현하는 구현체를 작성합니다. 이 때 `QuerydslRepositorySupport`를 상속받습니다.

```java
   import com.querydsl.jpa.impl.JPAQueryFactory;
   import org.springframework.data.jpa.repository.support.QuerydslRepositorySupport;
   
   import java.util.List;
   
   public class CustomUserRepositoryImpl extends QuerydslRepositorySupport implements CustomUserRepository {
   
       private final JPAQueryFactory queryFactory;
   
       public CustomUserRepositoryImpl(JPAQueryFactory queryFactory) {
           super(User.class);
           this.queryFactory = queryFactory;
       }
   
       @Override
       public List<User> findUsersByAge(int age) {
           QUser qUser = QUser.user;
           return queryFactory.selectFrom(qUser)
                   .where(qUser.age.eq(age))
                   .fetch();
       }
   }
```

위의 예제에서는 `CustomUserRepository` 인터페이스를 구현하는 `CustomUserRepositoryImpl` 클래스를 작성하였습니다. 이 클래스는 `QuerydslRepositorySupport`를 상속받아 구현되었으며, QueryDSL을 사용하여 동적인 쿼리를 작성할 수 있습니다.

이제 Custom Repository를 사용하여 동적인 쿼리를 호출할 수 있게 됩니다. 이 방식은 Spring Data JPA의 기본적인 기능을 확장하여 사용할 때 유용하게 활용됩니다.

### 12. JPA Auditing 적용

엔티티 클래스에는 공통적으로 들어가는 필드가 있습니다. Spring Data JPA에서는 이러한 값을 자동으로 넣어주는 기능을 제공합니다. 기능을 활성화하기 위해 main()메서드가 있는 클래스에 @EnableJpaAuditing 어노테이션을 추가하면 됩니다.

그러나 어노테이션을 추가하는 방법은 일부 테스트 상황에서는 오류가 발생할 수 있습니다. 따라서 Configuration 클래스를 별도로 생성하는 방법이 더 좋습니다.

```java
   import org.springframework.context.annotation.Configuration;
   import org.springframework.data.jpa.repository.config.EnableJpaAuditing;
   
   @Configuration
   @EnableJpaAuditing
   public class JpaAuditingConfig {
   // 이 클래스는 빈을 정의하는 Configuration 클래스입니다.
   // @EnableJpaAuditing 어노테이션을 통해 JPA auditing 기능을 활성화합니다.
   }
```

### 13. BaseEntity

코드의 중복을 없애기 위해서는 각 엔티티에 공통으로 들어가게 되는 칼럼을 하나의 클래스로 빼는 작업을 수행해야 합니다.

이를 통해 코드의 재사용성을 높이고 중복을 제거할 수 있습니다.

일반적으로 이러한 공통 필드를 포함한 클래스를 생성할 때는 `@MappedSuperclass` 어노테이션을 사용합니다. 이 어노테이션을 사용하면 해당 클래스가 데이터베이스 테이블과 매핑되지 않고, 단순히 엔티티 클래스에서 공통으로 사용할 수 있는 속성을 제공하는 역할을 합니다.

예를 들어, 생성일시(created_at), 수정일시(updated_at)와 같은 공통 필드를 포함한 BaseEntity 클래스를 생성할 수 있습니다.

```java
   import javax.persistence.*;
   import java.time.LocalDateTime;
   
   @MappedSuperclass
   public abstract class BaseEntity {
       
       @Column(name = "created_at", nullable = false, updatable = false)
       private LocalDateTime createdAt;
   
       @Column(name = "updated_at")
       private LocalDateTime updatedAt;
   
       // Getter and Setter methods
   }
```

위의 BaseEntity 클래스는 @MappedSuperclass 어노테이션을 사용하여 매핑되지 않는 베이스 클래스입니다. 이 클래스를 상속받는 모든 엔티티 클래스에서 createdAt과 updatedAt 필드를 공통으로 사용할 수 있습니다.

그리고 이러한 BaseEntity를 상속받아 실제 엔티티 클래스를 작성할 때는 상속을 받고 싶은 엔티티 클래스에 대해 extends 키워드를 사용하여 BaseEntity를 상속받으면 됩니다.

```java
   import javax.persistence.Entity;
   
   @Entity
   public class User extends BaseEntity {
       // User 엔티티의 추가적인 필드 및 메서드 정의
   }
```

이렇게 함으로써 BaseEntity에 정의된 createdAt과 updatedAt 필드를 User 엔티티에서도 사용할 수 있게 됩니다. 이를 통해 중복 코드를 제거하고 코드의 재사용성을 높일 수 있습니다.