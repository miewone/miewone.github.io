---
layout: post
title: Factory Method 
description: >
 게시판 만들기 프로젝트를 진행하며 자세히 알지 못하는 부분에 대한 공부 중 팩토리 메서드 대한 공부 내용
sitemap: true
hide_last_modified: false
---

1. 개요
{:toc}

# 프로젝트 관리
[프로젝트 관리 문서](https://docs.google.com/spreadsheets/d/1xxuP3eXVIsYP-Pe4pwDcvYthXhtYNUvVXXgRPU3XWqw/edit?usp=sharing)



# Singleton Method

어디서든지 객체를 하나만이 존재해야할때 사용하는 디자인 패턴 중 하나입니다.

```java
public class SocketClient {

    private static SocketClient socketClient = null;

    private SocketClient(){

    }

    public static SocketClient getInstance(){
        if(socketClient == null){
            socketClient = new SocketClient();
        }

        return socketClient;
    }

}
```

싱글톤 클래스에서는 다른 곳에서 생성자에 접근을 할 수 없게 private 생성자를 만들어줍니다. 그리고 public static으로 getInstance 메서드를 만들어
다른 클래스에서 객채에 접근을 할때 이미 생성되어졌거나 없을경우에는 하나의 객체를 만들어 반환해줍니다.

이렇게 하게되면 해당 인스턴스에 접근한 모든 객체는 동일한 인스턴스를 가지게 됩니다.