---
layout: post
title: Slice Test
description: >
 게시판 만들기 프로젝트를 진행하며 자세히 알지 못하는 부분에 대한 공부 중 Slice Test에 대한 공부 내용
sitemap: true
hide_last_modified: false
---

1. 개요
{:toc}

# 프로젝트 관리
[프로젝트 관리 문서](https://docs.google.com/spreadsheets/d/1xxuP3eXVIsYP-Pe4pwDcvYthXhtYNUvVXXgRPU3XWqw/edit?usp=sharing)

[Spring Boot 1.4를 사용한 사용자 정의 테스트 슬라이스](https://spring.io/blog/2016/08/30/custom-test-slice-with-spring-boot-1-4)

> ApplicationContext 테스트의 Slice는 테스트를 위해 생성된 레이어에 관한 것입니다. 일반적으로 MockMvc를 사용하여 컨트롤러를 테스트 하려는
경우 데이터 계층에 신경을 쓰지 않을 것입니다. 대신 컨트롤러가 사용하는 서비스를 Mocking하여야하고 모든 웹 관련 상호 작용이 예상대로 작동하는지 확인을 해야합니다.

[Slice Test-게시판 프로젝트](https://github.com/miewone/fastcampus-project-board/pull/16/commits/09ef2ae6463b682ea8c89fe442a9a404c0ae313f)

```java
...
@DisplayName("[api] 게시글 단건 조회")
@Test
void givenNothing_whenRequestingArticle_thenReturnsArticleJsonResponse() throws Exception {
    // Given

    // When & Then
    mvc.perform(get("/api/articles/1"))
            .andExpect(status().isOk())
            .andExpect(MockMvcResultMatchers.content().contentType(MediaType.valueOf("application/hal+json")));
}

@DisplayName("[api] 게시글 -> 댓글 리스트 조회")
@Test
void givenNothing_whenRequestingArticleCommentsFromArticle_thenReturnsArticleCommentsJsonResponse() throws Exception {
    // Given

    // When & Then
    mvc.perform(get("/api/articles/1/articleComments"))
            .andExpect(status().isOk())
            .andExpect(MockMvcResultMatchers.content().contentType(MediaType.valueOf("application/hal+json")));
}
...
```

1. 게시글 단건 조회.
2. 게시글에 있는 댓글 리스트 조회

위 2가지의 로직을 테스트할때 위 테스트에 대한 요청에 대한 검증이 필요합니다. 이때 상황에 따라 필요한
값이 다른데(성공,실패) 검증에 필요한 내용을 mock에 주입하여 테스트에 충족할 수 있는 결과를 나오도록 하면 됩니다.

테스트를 진행할 대 여러 로직을 거치며 문제 없이 동작하는지, 로직이 의도한 대로 동작하는지 여부를 편하게 테스트 할 수 있습니다.
