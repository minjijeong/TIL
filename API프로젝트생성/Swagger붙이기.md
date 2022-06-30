### 0. 프로젝트
#### [Spec]
- IntelliJ
- Springboot 2.7.0
- Gradle
- JAVA11

  
#### [plug-in/dependency]  
- Spring Boot DevTools
- Lombok
- Spring Web 
- springfox-boot-starter 3.0.0

### 1. 프로젝트 생성 
![인텔리제이 - Spring Boot Starter - initialize](/스프링부트%20개발관련/pictures/springboot-starter.png)

### 2. Swagger Dependancy 추가 
Swagger 3.0.0 부터는 springfox-boot-starter만 추가하면 기존의 springfox-swagger-ui까지 포함됩니다. 

***Gradle clean->build -> SpringBootApplication 실행 후 서버 정상작동하는지부터 확인**

```
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    compileOnly 'org.projectlombok:lombok'
    developmentOnly 'org.springframework.boot:spring-boot-devtools'
    annotationProcessor 'org.projectlombok:lombok'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    // swagger dependacy
    implementation 'io.springfox:springfox-boot-starter:3.0.0'
}
```

### 3. Swagger Config 생성
springfox 3.0.0 버젼 부터는 @EnableWebMvc 어노테이션을 통해서 세팅가능
swagger2와 그리고 springfox 3버젼 미만인 경우는 @EnableSwagger2 등을 쓴다고하나..
낮은 버젼을 사용하지 않으므로 패스. 

```java
@Configuration
@EnableWebMvc
public class SwaggerConfig {

    @Bean
    public Docket swaggerAPI(){
        //Docket : swagger Bean
        return new Docket(DocumentationType.OAS_30)
                .useDefaultResponseMessages(true) //기본 응답 메시지 표시 여부
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.api.backpackerapi")) //swagger탐색 대상 패키지
                .paths(PathSelectors.any())
                .build()
                .apiInfo(apiInfo());
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("api swagger")
                .description("api description")
                .version("1.0")
                .build();
    }
}
```


### 4. 접속
**http://localhost:8080/swagger-ui/index.html**

![Swagger Ui Index view](/스프링부트%20개발관련/pictures/swagger-ui-index.png)




### 5. 스웨거 예제 
https://victorydntmd.tistory.com/341
https://wildeveloperetrain.tistory.com/3
https://daddyprogrammer.org/post/313/swagger-api-doc/