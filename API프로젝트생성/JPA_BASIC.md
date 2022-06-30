### @Column & @Size 차이 

#### @Column(length = 40) => varchar(40)
- @Column은 DDL을 컨트롤 하기위해 사용되는 JPA annotation
https://docs.oracle.com/javaee/7/api/javax/persistence/Column.html


#### @Size(max=100)
- 유효성 체크까지 포함
- @Size Java-strandard annotation
@Size(min=2, max=240)
https://docs.oracle.com/javaee/7/tutorial/bean-validation001.htm


#### @Length
- specific to Hiberante
https://docs.jboss.org/ejb3/app-server/HibernateAnnotations/api/org/hibernate/validator/Length.html



참고 글 
https://blog.yevgnenll.me/posts/jpa-column-vs-size-what-is-different