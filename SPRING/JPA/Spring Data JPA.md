Docs : https://docs.google.com/document/d/1IjSKwMEsLdNXhRLvFk576VTR03AKTED_3jMsk0bHANg/edit

Reference : https://spring.io/projects/spring-data/


## 16. 스프링 데이터 Common: Repository 인터페이스 정의하기

- 특정 리포지토리 당 @RepositoryDefinition
```java
@RepositoryDefinition(domainClass = Comment.class, idClass = Long.class)
public interface CommentRepository {

    Comment save(Comment comment);
    List<Comment> findAll();
    
}

```

- 공통 인터페이스 정의 @NoRepositoryBean
  - Repository 기능이 없는 repository만 상속받아서 사용
```java
@NoRepositoryBean
public interface MyRepository<T, ID extends Serializable> extends Repository<T, ID> {

    <E extends T> E save(E entity);

    List<T> findAll();

}
```


## 18. 스프링 데이터 Common: Null 처리하기
- Optional reference로 받는 경우

```java
@RunWith(SpringRunner.class)
@DataJpaTest
public class CommentRepositoryTest {
    @Autowired
    CommentRepository commentRepository;

    @Test
    public void crud(){
        Optional<Comment> byId = commentRepository.findByIdOptional(100l);
        assertThat(byId).isEmpty();
        // 값이 미존재 시, throw exception 가능
        Comment comment = byId.orElseThrow(IllegalArgumentException::new);

        // 값이 없는 경우 - Optional 안쓰는 경우
        Comment commnetNull = commentRepository.findById(200l);
        assertThat(commnetNull).isNull();
        if(commnetNull == null){
            throw new IllegalArgumentException();
        }
    }
```

- 스프링 프레임워크 5.0부터 지원하는 Null 애노테이션 지원
  - Spring Framework에서 제공하는 Null 관련 어노테이션
    - 패키지 레벨에 붙이는 Annotation @NonNullApi 
      - 해당 패키지 않에 모든 타입이 NonNull로 정의
        - Null 허용하는 것은 @Nullable 각각 다 붙여 줘야 한다. 
      - Tool 지원을 잘 못받는다... IntelliJ가 아직은 안함

  - @Nullable, @NonNull
    - 파라미터, 리턴타입에 관한 제어 가능
  ```java 
  public interface MyRepository<T,Id extends Serializable> extends Repository {
      <E extends T> E save(@NonNull E entity);

      List<T> findAll();

      long count();

      @Nullable
      <E extends T>Optional<E> findById(Id id);
  }
  ```


## 19. 스프링 데이터 Common: 쿼리 만들기 개요
- 스프링 데이터 queryLookupStrategy 전략 선언하여 생성
```java
@SpringBootApplication
@Import(KeesunRegisterar.class)
@EnableJpaRepositories(queryLookupStrategy = Key.CREATE)
//@EnableJpaRepositories(queryLookupStrategy = Key.USE_DECLARED_QUERY)
//@EnableJpaRepositories(queryLookupStrategy = Key.CREATE_IF_NOT_FOUND)
public class DemospringdataApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemospringdataApplication.class, args);
    }

}
```

### 메소드 이름을 분석해서 쿼리 만들기 (CREATE)
- JPQL

### 미리 정의해 둔 쿼리 찾아 사용하기 (USE_DECLARED_QUERY)
JPA JPQL이 기본적으로 받아서 사용, SQL을 쓰고 싶다면 nativeQuery = true 
```java
    @Query(value = "SELECT c From Comment AS c", nativeQuery = true) 
    List<Comment> findByTitleContains(String keyword);
```

### 미리 정의한 쿼리 찾아보고 없으면 만들기 (CREATE_IF_NOT_FOUND) = CREATE + USE_DECLARED_QUERY 전략
- fromProcedureAnnotation(쿼리정의), fromMethodWithQueryString(메소드명으로 자동 쿼리생성), getNamedQueryName 순으로 작동
- 3가지 케이스를 모두 사용한 경우 주의하여서 고려
  
```java
        protected RepositoryQuery resolveQuery(JpaQueryMethod method, EntityManager em, NamedQueries namedQueries) {
            if (method.isProcedureQuery()) {
                return JpaQueryFactory.INSTANCE.fromProcedureAnnotation(method, em);
            } else if (StringUtils.hasText(method.getAnnotatedQuery())) {
                if (method.hasAnnotatedQueryName()) {
                    JpaQueryLookupStrategy.LOG.warn(String.format("Query method %s is annotated with both, a query and a query name. Using the declared query.", method));
                }

                return JpaQueryFactory.INSTANCE.fromMethodWithQueryString(method, em, method.getRequiredAnnotatedQuery(), this.getCountQuery(method, namedQueries, em), this.evaluationContextProvider);
            } else {
                String name = method.getNamedQueryName();
                if (namedQueries.hasQuery(name)) {
                    return JpaQueryFactory.INSTANCE.fromMethodWithQueryString(method, em, namedQueries.getQuery(name), this.getCountQuery(method, namedQueries, em), this.evaluationContextProvider);
                } else {
                    RepositoryQuery query = NamedQuery.lookupFrom(method, em);
                    return query != null ? query : JpaQueryLookupStrategy.NO_QUERY;
                }
            }
        }
```


### 예제 
```java 
// And, Or 조건으로 쿼리 정의 가능
// Page를 통해 Paging 처리 가능, List는 현 시점에서만 가져오고 Lazy 로딩이 어렵다
// OrderByCreatedDesc 정렬 정의 가능
// Pageable - Sort도 포함
Page<Comment> findByLikeGreaterThanAndPostOrderByCreatedDesc(int likeCount, Post post);
List<Comment> findByLikeCountGreaterThanAndPos(int likeCont, Sort sort);
```


* 주의 
- JPA primitive type - NotNull 컬럼이므로 안됨
- Like와 같은 키워드는 컬럼명을 만들면 안됨
