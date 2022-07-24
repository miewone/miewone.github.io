---
layout: post
title: Spring Boot Test
description: >
 게시판 만들기 프로젝트를 진행하며 자세히 알지 못하는 부분에 대한 공부 중 SpringBootTest(MovcMvc)에 대한 공부 내용
sitemap: true
hide_last_modified: false
---

1. 개요
{:toc}

# 프로젝트 관리
[프로젝트 관리 문서](https://docs.google.com/spreadsheets/d/1xxuP3eXVIsYP-Pe4pwDcvYthXhtYNUvVXXgRPU3XWqw/edit?usp=sharing)


# JUnit 5로 테스트 

## JUnit 테스트 활성화
JUnit 5는 클래스가 JUnit 테스트와 통합할 수 있는 확장 인터페이스를 정의합니다.
테스트 클래스에 `@ExtendWith` 주석을 추가하고 로드할 어노테이션 클래스를 지정하여 어노테이션을 활성활 할 수 있습니다.

Spring 테스트를 실행하기 위해서는 SpringExtension.class를 사용합니다. 그리고 Context 구성을 로그하고 테스트에서 사용할
Context 를 부트스트랩 하기 위해 `@ContextConfiguration` 어노테이션이 필요합니다.

```java
@ExtendWith(SpringExtension.class)
@ContextConfiguration(classes = { ApplicationConfig.class })
@WebAppConfiguration
public class GreetControllerIntegrationTest {
    ....
}
```

`@ContextCofiguration` 에서 이 특정 테스트에 필요한 구성을 로드하는 `ApplicationConfig.class` 구성 클래스를 제공했습니다.

여기서는 Context 구성을 지정하기 위해 Java 구성 클래슬르 사용합니다. 마찬가지로 XML 기반 구성을 사용할 수 있습니다.

> @ContextConfiguration(locations={""})

웹 애프리케이션 컨텍스트를 로드하는 `@WebAppConfiguration` 어노테이션을 테스트에 추가합니다.

기본적으로 `src/main/webapp` 경로에서 루트 웹 애플리케이션을 찾습니다. 단순히 value 속성을 전달하여 이 위치를 재정의 할 수 있습니다.
> @WebAppConfiguration(value= "")


## WebApplicationContext 개체

WebApplicationContext 는 웹 애플리케이션 구성을 제공합니다. 모든 애플리케이션 빈과 컨트롤러를 컨텍스트로 로드합니다.

```java
@Autowired
private WebApplicationContext webApplicationContext;
```

## 가상의 Web Context Beans
MockMvc 는 Spring MVC 테스트를 지원하고 모든 웹 애플리케이션의 Bean을 캡슐화하며 테스트에 사용할 수 있도록 합니다.

```java
privaet MockMvc mockMvc;
@BeforeEach
public void setup() throws Exception {
    this.mockMvc = MockMvcBuilders.webAppContextsetup(this.webApplicationContext).build();
}
```

`@BeforeEacth` 어노테이션에서 mockMvc 개체를 초기화 하므로 모든 테스트내에서 초기화할 필요가 없습니다.

WebApplicationContext 개체(webApplicationContext)를 올바르게 로드하고 있는지 확인하고 올바른 servletContext가 연결되고 있는지 확인합니다.

```java
@Test
public void givenWac_whenServletContext_thenItProvidesGreetController() {
    ServletContext servletContext = webApplicationContext.getServletContext();
    
    Assert.assertNotNull(servletContext);
    Assert.assertTrue(servletContext instanceof MockServletContext);
    Assert.assertNotNull(webApplicationContext.getBean("greetController"));
}
```

WebContext에 GreetController.java 빈이 존재 하는지 확인하고 있습니다. 이렇게 하면 Spring Bean이 제대로 로드 할 수 있습니다.
이 시점에서 통합 테스트의 설정이 완료됩니다. 이제 MockMvc 개체를 사용하여 리소스 메서드를 테스트하는 할 수 있습니다.


# MockMvc 제한 사항
MockMvc 는 웹 엔드포인트를 호출하고 동시에 응답을 검사하고 주장할 수 있게 사용하기 쉬운 API를 제공합니다. 하지만 제한 사항도 존재합니다.

우선 테스트 요청을 처리하기 위해 **DispatcherServlet의 하위 클래스**를 사용합니다. TestDispatcherServlet 은 컨틀롤러
를 호출하고 Spring에서의 역할을 수생합니다.

MockMvc 클래스는 이 TestDispatcherServlet을 내부적으로 래핑합니다. 따라서 `perform()` 메서드를 사용하여 요청을 보낼 때 마다
 MockMvc 는 기본 TestDispatcherSErvlet을 직접 사용합니다. 따라서 실제 네트워크 연결이 이뤄지지 않으므로 MockMvc 를 사용하는
도안 전체 네트워크 스택을 테스트하지 않습니다.

[게시판 만들기 프로젝트 - API 구현 테스트](https://github.com/miewone/fastcampus-project-board/pull/16/commits/09ef2ae6463b682ea8c89fe442a9a404c0ae313f)
```java
@DisplayName("[api] 게시글 리스트 조회")
    @Test
    void givenNothing_whenRequestingArticles_thenReturnsArticlesJsonResponse() throws Exception {
        // Given

        // When & Then
        mvc.perform(get("/api/articles"))
                .andExpect(status().isOk())
                .andExpect(MockMvcResultMatchers.content().contentType(MediaType.valueOf("application/hal+json")));
```

또한 Spring은 HTTP 요청 및 응답을 모의하기 위해 까자 웹 애플리케이션 Context를 준비하기 때문에 완전한 Spring 애플리케이션의 모든 기능을 지원하지 않을 수 있습니다.

예를 들어 이 모의 설정은 HTTP 리디렉션을 지원하지 않습니다. 이것은 처음에는 그렇게 중요해 보이지 않을 수 있습니다.
그러나 Spring boot는 현재 요청을 `/error` 엔드포인트로 리디렉션하여 일부 오류를 처리합니다. 따라서 MockMvc 를 사용하는 경우 일부 API 오류를 테스트 하지 못할 수 있습니다.

MockMvc의 대안으로 더 실제적인 애플리케이션 Context를 설정한 `RestTemplate`또는 `Rest-assured`를 사용하여 애플리케이션을 테스트할 수 있습니다.

```java
@SpringBootTest(webEnvironment = DEFINED_PORT)
public class GreetControllerRealIntegrationTest{
    @Before
    public void setUp(){
        RestAssured.port = DEFAUT_PORT;
    }
    
    @Test
    public void givenGreetURI_whenSendingReq_thenVerifyResponse(){
        given().get("/greet")
          .then()
          .statusCode(200);
    }
}
```

여기에는 `@ExtendWith(SpringExtension.class)`를 추가할 필요 조차 없습니다.