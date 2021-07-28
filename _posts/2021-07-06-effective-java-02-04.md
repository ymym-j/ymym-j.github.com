---
layout: post
title: "인스턴스 화를 막으려든 private 생성자를 사용하라"
date: 2021-07-06
excerpt: "effective java 2장 item 04"
category: [effective java]
tags: [effective java, private 생성]
comments: true
---


# 인스턴스 화를 막으려든 private 생성자를 사용하라

정적 메서드와 정적 필드만을 담은 클래스는 객체지향적인 방향에서 좋은 방식은 아니지만 쓰임은 있다.
예를 들면
java.lang.Math와 java.util.Arrays와 같은 기보 타입이나 배열 관련 메서드를 모아놓는다던가,
java.util.Collections 처럼 특정 인터페이스를 구현하는 객체를 생성해주는 정적메서드를 모아둘 수도 있다.

마지막으로 final 클래스와 관련한 메서드를 모아놓을 때도 사용한다.

이 때 생성자가 자동으로 생성되기 때문에 개발자 의도와 다르게 사용자가 인스턴스화를 할 수 있다.

인스턴스 화를 막는 방법은 private 생성자를 추가하는 것이다.

```
  public class UtilityClass{
    // 기본 생성자가 생기는 것을 방지
    private UtilityClass(){
      throw new AssertionError();
    }

    ...
  }
```

이 방식을 사용하면 상속도 막을 수 있다.
