---
layout: post
title: "자원을 직접 명시하지 말고 의존 객체 주입을 사용하라"
date: 2021-08-07
excerpt: "effective java 2장 item 05"
category: [effective java]
tags: [effective java, private 생성]
comments: true
---

# 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라


### 정적 유틸리티를 잘못 사용한 예
```
  public class SpellChechker {
    private static final Lexicon dictionary = ...;
    
    //객체 생성 방지
    private SpellChecker() {
    
    }
    
    public static boolean isValid(String word) {
      ...
    }
    
    public static List<String> suggestions(String type){
      ...
    }
  
  }
```

### 싱글턴을 잘못 사용한 예

```
  puvlic class SpellChecker {
    private final Lexicon dictionary = ...;
    
    private SpellChecker(...) {}
    
    public static SpellCheker INSTANCE = new Spellchecker(...);
    
    public boolean isValid(String word) {
      ...
    }
    
    public List<String> suggestions(String type){
      ...
    }
  }
```

위의 두 방식 모두 유연하지 않고 테스트 하기 어렵다는 단점이 있다. <br>

사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글턴 방식은 접합하지 않다. <br>

대신 클래스가 여러 자원 인스턴스를 지원해야 하며, 클라이언트가 원하는 자원을 사용해야 한다.<br>

이 조건을 만족하는 방식이 바로, 의존 객체 주입 패턴이다.<br>

의존 객체 주입 패턴을 사용함으로서 유연성, 재사용성, 테스트 용의성을 개선할 수 있다.<br>
<br>
### 의존 객체 주입 패턴을 사용 한 예
```
  public class SpellChecker{
    private final Lexicon distional;
    
    public SpellChecker(Lexicon dictionary) {
      this.dictionary = Objects.requireNonNull(dictionary);
    }
    
    public boolean isValid(String word) {
      ...
    }
    
    public List<String> suggestions(String type){
      ...
    }
  }

```


의존 객체 주입이 유연성 및 테스트 용이성을 개선해주긴 하지만, 대형 프로젝트에서는 코드를 어지럽게 만들기도 한다.<br>

대거, 스프링 같은 의존 객체 주입 프레임워크를 사용하면 깔끔하게 개발할 수 있다.
