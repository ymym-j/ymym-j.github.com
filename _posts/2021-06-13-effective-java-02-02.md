---
layout: post
title: "생성자에 매개변수가 많다면 빌더를 고려하라"
date: 2021-05-30
excerpt: "effective java 2장 item 01"
category: [effective java]
tags: [effective java, 생성자, 정적 팩터리 메서드, 빌더 패턴]
comments: true
---
# 생성자에 매개변수가 많다면 빌더를 고려하라

정적 팩터리나 생성자에 동일한 제약이 존재한다. 선택적 매개변수가 많을 경우 적절히 대응하기 어렵다는 점이다.

이럴 경우 사용하는 대안이 몇 가지가 있다. 그 방법을 나열해보겠다.

## 점층적 사용자 패턴

점층적 사용자 패턴이란 하나씩 생성자를 늘려가는 방식이다.

예를들어 필수 매개변수 2개와 선택 매개변수 1, 필수 매개변수 2개와 선택 매개변수 2 이렇게 하나 씩 늘려가는 방식이다.
아래의 코드가 그 예이다.

id 필드는 필수 매개변수, 나머지는 선택 매개변수로 가정하고 작성하였다.
```
public class Member{
  private final int id;
  private final String name;
  private final String address;
  
  public Member(int id){
      this(id, "");
  }
  
  public Member(int id, String name){
      this(id, name, "");
  }
  
  public Member(int id, String name, String address){
      this.id = id;
      this.name = name;
      this.address = address;
  }
}
```
이 클래스의 인스턴스를 만들려면 원하는 매개변수가 모두 포함된 생성자 중 가장 짧은 것을 골라 호출하면 된다.
만약 사용자가 원하지 않는 값도 포함이 되어있다면 어쩔 수 없이 값을 지정해줘야 한다.
```
Member member = new Member(1, "홍길동");
```

점층적 사용자 패턴은 매개변수가 많아지면 클라이언트 코드를 작성하거나 읽기 어렵다는 큰 단점이 있다.

## 자바빈즈 패턴

매개변수가 없는 생성자로 객체를 만든 후 Setter 메서드를 통해 원하는 매개변수의 값을 설정하는 방식이다.


```
public class Member{
  private final int id;
  private final String name;
  private final String address;
  
  public void setId(int id) {
      this.id = id;
  } 
  
  public void setName(String name) {
      this.name = name;
  }
  
  public void setAddress(String address) { 
      this.address = address;
  }
}
```
점층적 사용자 패턴에서의 가독성문제나 필요없는 값이 인스턴스에 포함된다는 단점이 자바빈즈 패턴에서는 해결된다.

```
Member member = new Member();
member.setId(40217);
member.setName("홍길동");
member.setAddress("서울시 동작구");
```

하지만 여기에도 큰 단점이 있다.

객체를 하나 만들기 위해 메서드를 여러 개 호출해야 하고, 객체가 완전히 생성되기 전까지는 일관성이 무너진 상태에 놓이게 된다.

점층적 생성자 패턴에서는 매개변수 유효 여부만 확인하면 일관성이 유지 가능한 반면,
자바 빈즈 패턴에서는 그 장치가 사라지게 되는 것이다.

<br>
일관성이 깨지게 된다면,

버그를 심은 코드와 그 버그 때문에 런타임에 문제를 겪는 코드가 물리적으로 멀리 떨어져 있기 때문에,
디버깅이 어려워진다.

이러한 일관성이 깨지는 문제 때문에 자바빈즈 패턴에서는 클래스를 불변으로 만들 수 없으며 스래드의 안정성을 얻으려면 프로그래머가 추가작업을 해줘야한다.

이러한 단점을 완화하기 위한 방법이 없는 것은 아니다.

생성이 끝난 객체를 수동으로 얼리고(freezing) 얼리기 전에는 사용할 수 없도록 하는 방법이 있다.

하지만 이 방법은 다루기 어려우며 사용한다 하더라도 객체 사용 전에 freeze 메서드를 확실히 호출하였는지를 컴파일러가 보증 할 방법이 없기 때문에 런타임 오류에 취약하다.

그래서 세 번째로 소개할 패턴은 위의 두 방법의 장점을 겸비한 빌더 패턴이다.
## 빌더 패턴

위에서 말한 것 과 같이,

점층적 생성자 패턴의 안정성 + 자바빈즈 패턴의 가독성

두 가지의 장점을 겸비한 빌더 패턴 이다.

클라이언트는 필요한 객체를 직접 만드는 대신 필수 매개변수 만으로 생성자를 호출해 빌더 객체를 얻을 수 있다.

그 후 빌더 객체가 제공하는 일종의 세터 메서들로 선택 매개변수를 설정한다.

마지막으로 build  메서드를 호출해 필요한 객체를 얻게 된다.


```
public class Member {
  private final int id;
  private final String name;
  private final String address;
 
  public static class Builder {
      //필수 매개변수
      private final int id;
      
      //선택 매개변수 - 기본값으로 초기화
      private String name    = "";
      private String address = "";
      
      public Builder(int id){
         this.id = id;
      }
      
      public Builder name(String name){
         this.name = name;
         return this;
      }
      
      public Builder address(String address) {
         this.address = address;
         return this;
      }
      
     private Member(Builder builder) { 
         name = builder.name;
         address = builder.address;
     }
  } 
}
```

Member 클래스는 불변이며, 빌더의 세터 메서드 들은 자신을 반환하기 때문에 연쇄적으로 호출할 수 있다.

이런 방식을 메서드 호출이 흐르듯 연결된다는 뜻으로 플루언트 API 혹은 메서드 연쇄 라 한다.

이 클래스를 사용하는 클라이언트 코드의 모습이다.
```
Member member = new Member.Builder(21302)
      .name("홍길동").address("서울시 동작구").build();
```

이 클라이언트 코드는 쓰기 쉽고 읽기 쉽다. 빌더 패턴은 파이썬과 스칼라에 있는 명명된 선택적 매개변수를 흉내낸 것이다.
![img_1.png](img_1.png)