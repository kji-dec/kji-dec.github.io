---
layout: post
title: Flutter Study Week2
subtitle: Lec 9~14
categories: Flutter
tags: Flutter APP
sidebar: []
---



[강의 - 완전 초보용 플러터 강좌](https://www.youtube.com/watch?v=AdYRASHRKwE&list=PLQt_pzi-LLfpcRFhWMywTePfZ2aPapvyl)

### Class and Widget

- Class: 속성 및 기능에 대한 정의
- 객체: class가 정의된 후 메모리 상에 할당된 것
- 인스턴스: class의 속성과 기능을 똑같이 가지고 있고 프로그래밍 상에서 사용되는 대상

```dart
class Person{ //class
  String name;
  int age;
  String sex;   //멤버 변수들
  
  //생성자의 인자값에 중괄호 -> argument가 optional이라는 의미
  Person({String name, int age, String sex}){ //생성자
    this.name = name;
    this.age = age;
    this.sex = sex;
  }
}

// int addNumber(int n1, int n2){  //function
//   return n1 + n2;
// }

void main(){
  Person p1 = new Person(age: 30);
  Person p2 = new Person();
  print(p1.age);
  print(p2.age);
//   print(addNumber(3, 4));
}
```

- dart는 type 추론 기능이 있어서 굳이 함수 return 형식 혹은 변수 type을 지정해주지 않아도 문법적으로 오류는 없음 → 그래도 정확히 원하는 결과가 나오게 하기 위해서는 지정해주는 것이 좋음!
- 위젯이 클래스라고 봐도 되지만 인스턴스라고 볼 수도 있음

### AppBar icon buttons

1. leading: 아이콘 버튼이나 간단한 위젯을 왼쪽에 배치
2. actions: 복수의 아이콘 버튼 등을 오른쪽에 배치
3. onPressed: 함수의 형태로 일반 버튼이나 아이콘 버튼을 터치했을 때 일어나는 이벤트 정의

→ 활용은 실습 파일 혹은 [Week2] 말머리의 커밋 히스토리 참고