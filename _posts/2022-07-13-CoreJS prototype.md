---
layout: single
title: "CoreJS prototype"
---

# 프로토타입
**자바스크립트는 프로토타입(prototype) 기반 언어이다.**   

`클래스` 기반 언어 : `상속`을 사용    
`프로토타입` 기반 언어 : 어떤 객체를 `원형(prototype)`으로 삼고 이를 복제(참조)함으로써 상속과 비슷한 효과를 얻음    

### constructor, prototype, instance
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
    
**인스턴스** : 일반적으로 실행 중인 임의의 프로세스, 클래스의 현재 생성된 오브젝트를 가리킴. 객체의 인스턴스는 `데이터베이스`나 `SGA`, `백그라운드 프로세스`등 광범위한 컴퓨터시스템 자원의 접근에 할당된 물리 메모리의 일부를 가리킴.

`prototype`은 객체, 이를 참조하는 `__proto__` 역시 객체.   
`prototype` 객체 내부에는 인스턴스가 사용할 메서드를 저장한다. 그러면 인스턴스에서도 숨겨진 프로퍼티인 `__proto__` 를 통해 이 메서드들에 접근할 수 있게 된다.    

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

`__proto__` 는 생략 가능한 프로퍼티이다.    

```jsx
suzi.__proto__.getName
-> suzi(.__proto__).getName
-> suzi.getName
```

`__proto__` 를 생략하지 않았을 때 : `this`는 `suzi.__proto__` 를 가리킴   
`__proto__` 를 생략 했을 때 : `suzi` 를 가리킴. `suzi.__proto__` 에 있는 메서드인 `getName`을 실행하지만 `this`는 `suzi`를 바라보게 할 수 있게 됨.    

- 자바스크립트는 함수에 자동으로 객체인 `prototype` 프로퍼티를 생성
- 해당 함수를 생성자 함수로서 사용할 경우(`new` 연산자와 함께 함수를 호출할 경우)
- 생성된 인스턴스에는 숨겨진 프로퍼티인 `__proto__` 가 자동으로 생성
- 이 프로퍼티는 생성자 함수의 `prototype` 프로퍼티를 참조

`__proto__` 프로퍼티는 생략 가능하도록 구현되어 있기 때문에 **생성자 함수의 prototype에 어떤 메서드나 프로퍼티가 있다면 인스턴스에서도 마치 자신의 것처럼 해당 메서드나 프로퍼티에 접근할 수 있음**   

### constructor 프로퍼티
생성자 함수의 프로퍼티인 `prototype` 객체 내부에 `constructor` 프로퍼티가 존재. 인스턴스의 `__proto__` 객체 내부에도 존재. 이 프로퍼티는 원래의 생성자 함수(자기 자신)를 참조.   

```jsx
var arr = [1, 2];
Array.prototype.constructor === Array  // true
arr.__proto__.constructor === Array    // true 인스턴스의 __proto__ 가 생성자 함수의 prototype 프로퍼티를 참조
arr.constructor === Array              // true (__proto__생략)인스턴스에서 직접 constructor에 접근

var arr2 = new arr.constructor(3, 4);  // 오류 없이 동작
console.log(arr2);                     // [3, 4]
```

`constructor`는 기본형 리터럴 변수 `number`, `string`, `boolean` 과 같이 읽기 전용 속성이 부여된 예외적인 경우를 제외하고는 값을 바꿀 수 있다.   

### 메서드 오버라이드
#### 인스턴스가 동일한 이름의 프로퍼티 또는 메서드를 가지고 있다면?
```jsx
var Person = function (name) {
  this.name = name;
};
Person.prototype.getName = function () {
	return this.name;
};

var iu = new Person('지금');
iu.getName = function () {
	return '바로 ' + this.name;
};
console.log(iu.getName());        // 바로 지금    
```

**메서드 오버라이드** : 메서드 위에 메서드를 덮어씌웠다는 표현. 원본이 그대로 있는 상태에서 다른 대상을 그 위에 얹는 이미지.    

### 프로토타입 체인

<img width="540" alt="스크린샷 2022-07-14 오전 10 46 06" src="https://user-images.githubusercontent.com/84711115/178870180-a495a8c0-4281-4c20-b6a9-59ed4179bc6f.png">

```jsx
var arr = [1, 2];
arr(.__proto__ ).push(3);
arr(.__proto__)(.__proto__).hasOwnProperty(2); // true
```

**프로토타입 체인(prototype chain)** : 어떤 데이터의 `__proto__` 프로퍼티 내부에 다시 `__proto__` 프로퍼티가 연쇄적으로 이어진 것. 이 체인을 따라가며 검색하는 것을 `프로토타입 체이닝(prototype chaining)` 이라고 함. 두 단계 이상의 체인을 지니는 `다중 프로토타입 체인`도 가능.   


  
