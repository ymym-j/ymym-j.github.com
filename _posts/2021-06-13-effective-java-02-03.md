---
layout: post
title: "private 생성자나 열거 타입으로 싱글턴임을 보증하라"
date: 2021-06-13
excerpt: "effective java 2장 item 03"
category: [effective java]
tags: [effective java, 싱글턴]
comments: true
---


# private 생성자나 열거 타입으로 싱글턴임을 보증하라

싱글턴이란 인스턴스를 오직 하나만 생성할 수 있는 클래스를 말함.

클래스를 싱글턴으로 만들면 이를 사용하는 클라이언트를 테스트하기가 어려워 질 수 있다.
> 타입을 인스턴스로 정의한 다음 그 인터페이스를 구현해서 만든 싱글턴이 아니라면 싱글턴 인스턴스를 가짜 구현으로 대체할 수 없기 때문.

싱글턴을 만드는 방식은 보통 두 가지가 있다.

두 방식 모두 생성자는 private 로 감춰두고 유일하게 인스턴스에 접근할 수 있는 수단으로 Public static 멤버를 하나 마련해둔다.

## public 필드 방식
```
public class Elvis {
  public static final Elvis INSTANCE = new Elvis();
  private Elvis(){...}

  public void leaveTheBuilding(){...}
}
```
private 생성자는 public static final 필드인 Elvis.INSTANCE를 초기화 할 때 딱 한번만 호출된다.
public이나 protected 생성자가 없음으로 초기화될 때 만들어진 인스턴스가 전체 시스템에서 하나뿐임이 보장된다.

## 정적 팩터리 메서드 방식

```
public class Elvis {
  private static final Elves INSTANCE = new Elvis();
  private Elvis()
  {...}
  public static Elvis getInstance(){ return INSTANCE; }

  public void leaveTheBuilding() { ... }
}
```

첫 번째 코드의 가장 큰 장점은 해당 클래스가 싱글턴임이 명백히 들어난다는 것이다.

두 번째 코드의 장점은 api를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다는 점 등이 있는데
이러한 장점이 굳이 필요 없는 상황이라면 첫 번째 코드인 public 필드 방식이 좋다.

위의 방식으로 만든 싱글턴 클래스를 직렬화 하려면 단순히 Serializable을 구현한다고 선언하는 것 만으로는 부족하다.
모든 인스턴스 필드를 일시적이라고 선언하고 readResolve 메서드를 제공해야한다.
이렇게 하지 않으면 역직렬화 할 때 마다 새로운 인스턴스가 만들어진다.

```
  private Object readResolve() {
    returm INSTANCE;
}
```

## 열거 타입을 선언하는 방식

```
public enum Elvis {
    INSTANCE;

    public void leaveTheBuilding() { ... }
}
```
public 필드 방식과 비슷하지만, 더 간결하고 추가 노력 없이 직렬화 할 수 있다.
