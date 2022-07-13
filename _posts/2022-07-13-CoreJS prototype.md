---
layout: single
title: "CoreJS prototype"
---

# 프로토타입
**자바스크립트는 프로토타입(prototype) 기반 언어이다.**   

클래스 기반 언어 : `상속`을 사용    
프로토타입 기반 언어 : 어떤 객체를 `원형(prototype)`으로 삼고 이를 복제(참조)함으로써 상속과 비슷한 효과를 얻음    

<img width="518" alt="스크린샷 2022-07-13 오전 11 09 02" src="https://user-images.githubusercontent.com/84711115/178635877-d1e85858-f9ea-4f29-a9a6-33793af6d82e.png">
위 그림은 다음 코드의 내용을 추상화한 것이다.

```jsx
var instance = new Constructor();
```

<img width="540" alt="스크린샷 2022-07-13 오전 11 10 20" src="https://user-images.githubusercontent.com/84711115/178636028-ba9e940c-93e2-4693-a214-1a4cb97cdf3f.png">

- 어떤 생성자 함수(Constructor)를 `new` 연산자와 함께 호출하면
- `Constructor`에서 정의된 내용을 바탕으로 새로운 인스턴스(instance)가 생성
- 이때 `instance`에는 `__proto__` 라는 프로퍼티가 자동으로 부여
- 이 프로퍼티는 `Constructor`의 `prototype`이라는 프로퍼티를 참조    
    
인스턴스 : 일반적으로 실행 중인 임의의 프로세스, 클래스의 현재 생성된 오브젝트를 가리킴. 객체의 인스턴스는 `데이터베이스`나 `SGA`, `백그라운드 프로세스`등 광범위한 컴퓨터시스템 자원의 접근에 할당된 물리 메모리의 일부를 가리킴.

prototype은 객체, 이를 참조하는 `__proto__` 역시 객체.   
prototype 객체 내부에는 인스턴스가 사용할 메서드를 저장한다. 그러면 인스턴스에서도 숨겨진 프로퍼티인 `__proto__` 를 통해 이 메서드들에 접근할 수 있게 된다.    

```jsx
var Person = function (name) {
  this._name = name;
};
// Person이라는 생성자 함수의 prototype에 getName이라는 메서드를 지정
Person.prototype.getName = function() {
  return this._name;
};
// Person의 인스턴스는 __proto__ 프로퍼티를 통해 getName을 호출할 수 있음
// instance의 __proto__가 Constructor의 prototype 프로퍼티를 참조하므로 둘은 같은 객체를 바라봄
Person.prototype === suzi.__proto__. // true

var suzi = new Person('Suzi');
suzi.__proto__.getName();            // undefined. getName이 실행되었지만, this에 바인딩된 대상이 잘못 지정.
```

```jsx
var suzi = new Person('Suzi', 28);
suzi.getName();     // Suzi
var iu = new Person('Jieun', 28);
iu.getName();       // Jieun
```

__proto__ 
