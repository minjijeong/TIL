## 개발환경 구성
- Gradle 7.4.1
- Springboot 2.7.0
- Java11 (build.gradle 파일에서 sourceCompatibility 로 수정가능. java8, java11 두버젼 테스트 완료)
- Spring Data JPA
- H2 Database
- Spring Web
- Spring REST Docs 3.3.2

## 구현경로 
https://github.com/minjijeong/category

## 기본 동작원리 
1. mock-mvc를 기반으로 한 JUnit Test를 돌렸을 때, snippets를 생성하고 build 폴더 하위에 테스트에 대한 rest-docs 들이 생성된다. 

2. build 하위에 생성된 rest-docs를 프로젝트내에 지정 폴더로 copy 후 build 하위 rest-docs 삭제 처리

*maven의 경우 target 하위에 존재, 플러그인의 빌드산출물이 저장되는 경로에 따라 지정되는 것으로 추정*

## 환경 설정
1. build.gradle 수정
```
plugins {
    ...
    id 'org.asciidoctor.jvm.convert' version '3.3.2'
}
ext {
    set('snippetsDir', file("build/generated-snippets"))
}

bootJar {
    // 빌드 전 문서 생성 확인
    dependsOn asciidoctor
    // 생성된 문서를 static/docs 에 복사
    from ("${asciidoctor.outputDir}/html5") {
        into 'static/docs'
    }
}

dependencies {
    ...
    testImplementation 'org.springframework.restdocs:spring-restdocs-mockmvc'
}

test {
    outputs.dir snippetsDir
    useJUnitPlatform()
}

asciidoctor {
    // Snippets 디렉토리를 Input 디렉토리로 설정
    inputs.dir snippetsDir
    // 문서 생성 전 테스트가 실행되도록 test 에 종속 설정
    dependsOn test
}

asciidoctor.doFirst {
    delete file('src/main/resources/static/docs')
}

// 실제 이부분이 정상 동작 하지 않은듯.. 요 부분 추가 확인 필요함
task copyDocument(type: Copy) {
    dependsOn asciidoctor
    from file("build/docs/asciidoc")
    into file("src/main/resources/static/docs")
}

build {
    dependsOn copyDocument
}
```
2. application.yml 수정 (이부분이 REST-Docs를 위한 것인지는 더 확인해봐야함.)
```yml
spring: 
  mvc:
    view:
      suffix: .html
```

3. JUnit Rest-Docs 세팅 
```java
package com.api.category;

import static org.springframework.restdocs.operation.preprocess.Preprocessors.preprocessRequest;
import static org.springframework.restdocs.operation.preprocess.Preprocessors.preprocessResponse;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultHandlers.print;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;
import static org.springframework.restdocs.mockmvc.MockMvcRestDocumentation.documentationConfiguration;
import static org.springframework.restdocs.mockmvc.MockMvcRestDocumentation.document;
import static org.springframework.restdocs.operation.preprocess.Preprocessors.prettyPrint;

import com.fasterxml.jackson.databind.ObjectMapper;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.RegisterExtension;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.HttpStatus;
import org.springframework.http.MediaType;
import org.springframework.restdocs.RestDocumentationContextProvider;
import org.springframework.restdocs.RestDocumentationExtension;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.MvcResult;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import org.springframework.web.context.WebApplicationContext;

@AutoConfigureMockMvc
@SpringBootTest
class CategoryapiApplicationTests {

    // REST Docs copy 타겟 폴더 지정
    @RegisterExtension
    final RestDocumentationExtension restDocumentation = new RestDocumentationExtension ("custom");

    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private ObjectMapper objectMapper;

    // REST Docs 세팅
    @BeforeEach
    public void setUp(WebApplicationContext webApplicationContext
                    , RestDocumentationContextProvider restDocumentation){
        this.mockMvc = MockMvcBuilders
                .webAppContextSetup(webApplicationContext)
                .apply(documentationConfiguration(restDocumentation))
                .alwaysDo(document("{method-name}", preprocessRequest(prettyPrint()),  preprocessResponse(prettyPrint())))
                .build();
    }

    @Test
    void 테스트() throws Exception{
        MvcResult mvcResult= mockMvc.perform(
                                MockMvcRequestBuilders
                                .get("/api/hello")
                                .accept(MediaType.APPLICATION_JSON)
                                    )
                                    .andExpect(status().isOk())
                                    .andExpect(content().contentType("application/json"))
                                    .andExpect(jsonPath("$.error").value(false))
                                    .andExpect(jsonPath("$.errorCode").isEmpty())
                                    .andExpect(jsonPath("$.message").value(HttpStatus.OK.getReasonPhrase()))
                                    .andExpect(jsonPath("$.result").exists())
                                    .andReturn();
    }
    //...
}
```
