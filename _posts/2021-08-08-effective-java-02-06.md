---
layout: post
title: "불필요한 객체 생성을 피하라"
date: 2021-08-08
excerpt: "effective java 2장 item 06"
category: [effective java]
tags: [effective java]
comments: true
---

# 불필요한 객체 생성을 피하라

똑같은 객체를 매번 생성하기 보다는 객체 하나를 재사용 하는 편이 나을 때가 많다.

특히 불변객체는 언제든 재사용 할 수 있다.

다음 예는 하지 말아야 할 예이다

```
String s = new String("test");
```

위의 소스는 실행될 때 마다 인스턴스를 새로 만든다.

생성자에 넘겨진 "test" 자체가 이 생성자로 만들어내려는 String과 기능적으로 똑같다.

이 문장이 반복문과 같은 빈번히 호출되는 메서드 안에 있다면, 쓸데없는 String 인스턴스가 여러개 만들어 질 수도 있다.

```
String s = "test";
```

위의 코드는 매번 인스턴스를 만드는 대신 하나의 String 인스턴스를 사용한다.

나아가 이 방식을 사용한다면 똑같은 문자열 리터럴을 사용하는 모든 코드가 같은 객체를 재사용 함이 보장된다.

생성자 대신 정적 팩터리 메서드를 제공하는 불변 클래스에서
제공하는 정적 팩터리 메서드를 사용해 불필요한 객체 생성을 피할 수 있다.

```
Boolean(String)
```

대신

```
Boolean.valueOf(String)
```

팩터리 메서드를 사용하는 것이 좋다.

또한 생성 비용이 비싼 객체가 더러 있는데, 이런 객체가 반복되서 사용된다면 캐싱하여 재사용하길 권한다.

아래의 예를 살펴보겠다.

```
static boolean IsRomanNumeral(String s) {
  return s.match("^)?=.)M&(C[MD]|D?C{0.3})"
        + "(X{CL]|L?X{0,3})(I[XV]\V?I{0,3}$");
}
```

이 문제는 String.matches 메서드를 사용하는 데 있다.
String.matches는 정규식으로 문자열 형태를 확인하는 가장 쉬운 방법이지만,

성능이 중요한 상황에서 반복해 사용하기엔 적합하지 않다.

정규표현식용 Pattern 메서드는 한 번 쓰고 버려져 가비지 컬렉션 대상이 된다.

성능을 개선하려면 Pattern 인스턴스를 초기화 과정에서 직접 생성해 캐싱해두고, 나중에 이 인스턴스를 재사용한다.


```
Public class RomanNumerals {
  private static final Pattern ROMAN = Pattern.compile(
        "("^)?=.)M&(C[MD]|D?C{0.3})"
        + "(X{CL]|L?X{0,3})(I[XV]\V?I{0,3}$");
        
  static boolean isRomanNumeral(String s){
    return ROMAN.matcher(s).matches();  
  }
}
```
