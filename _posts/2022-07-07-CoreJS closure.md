---
layout: single
title: "CoreJS closure"
---

클로저는 여러 함수형 프로그래밍 언어에서 등장하는 보편적인 특성이다.    
자바스크립트 고유의 개념은 아니다. ECMAScript 명세에서 정의를 다루지 않는다.        
다양한 문헌에서 제각각 클로저를 다르게 정의를 설명하고 있다.    
    
MDN에서는 “클로저는 함수와 그 함수가 선언될 당시의 lexical environment의 상호관계에 따른 현상” 이라고 설명하고 있다.      
      
**lexical environment** : 특정코드가 작성, 선언(정의)된 환경    
    
선언될 당시의 lexical environment는 실행 컨텍스트의 구성 요소 중 하나인 outerEnvironmentReference에 해당한다.        
어떤 컨텍스트 A에서 선언한 내부함수 B의 실행 컨텍스트가 활성화된 시점에는 B의 outerEnvironmentReference가 참조하는 대상인 A의 LexicalEnvironment에도 접근 가능하다.        
A에서는 B에서 선언한 변수에 접근할 수 없고, B에서는 A에서 선언한 변수에 접근 가능하다.    
    
내부함수에서 외부 변수를 참조하는 경우에 한해서만 ‘선언될 당시의 LexicalEnvironment와의 상호관계”가 성립한다.    
    
```jsx
var outer = function () {
		var a = 1;             // outer 함수에서 변수 a를 선언
								var inner = function () {
				console.log(++a);  // 내부함수인 inner 함수에서 a의 값을 1만큼 증가시킨 다음 출력. 2
		};
		inner();
};
outer();

```
    
inner 함수 내부에서 a를 선언하지 않았기 때문에 environmentRecord에서 값을 찾지 못하므로 outerEnvironmentReference에 지정된 상위 컨텍스트인 outer의 LexicalEnvironment에 접근해서 a를 찾는다.     
    
```jsx
var t = '트위치'          // 전역 global, 전역 범위 (global scope)

var outer = function () {
var a = '아리';           // outerEnvironmentReference, 외부 함수 범위(outer functions scope)

	function inner () {     // 내부 함수이며, 클로저
		var b = '브라움'      // environmentRecord, 지역 범위 (local scope)
		console.log(a);       // a -> '아리', b -> '브라움', t -> '트위치'
	}
	return inner           // inner 함수를 return
}

var outer2 = outer();    // inner 함수를 받음. 
outer2();                 // inner 함수를 실행, a 변수에 접근
```
    
`Garbage Collection, GC` 은 메모리 관리 기법 중의 하나로, 프로그램이 동적으로 할당했던 메모리 영역 중에서 필요없게 된 영역을 해제하는 기능이다. 책에서 `GC는 Garbage Collector`       
변수 a 가 가비지 컬렉팅 대상에서 제외 된다. 지역변수를 참조하는 내부함수가 외부로 전달된 경우    
    
**클로저란 어떤 함수 A(outer)에서 선언한 변수 a를 참조하는 내부함수 B(inner)를 외부로 전달할 경우 A의 실행 컨텍스트가 종료된 이후에도 변수 a가 사라지지 않는 현상**    
    
‘외부로 전달’이 return만을 의미하는 것은 아니다.   
    
- window의 메서드(setTimeout, setInterval)에 전달할 콜백 함수 내부에서 지역변수 참조. 클로저
- DOM의 메서드(addEventListener)에 등록할 handler 함수 내부에서 지역변수를 참조. 클로저
    
### 클로저와 메모리 관리

클로저는 어떤 필요에 의해 의도적으로 함수의 지역변수를 메모리를 소모하도록 함으로써 발생한다.    
참조 카운트를 0으로 만들면 **GC가 수거**해갈 것이고 소모됐던 메모리도 **회수**된다.        
식별자에 참조형이 아닌 기본형 데이터(**보통 null이나 undefined**)를 할당한다.    
    
### 접근 권한 제어(정보 은닉)
    
어떤 모듈의 내부 로직에 대해 **외부로의 노출을 최소화**해서 모듈간의 결합도를 낮추고 유연성을 높이고자 하는 현대 프로그래밍 언어의 중요한 개념 중 하나이다.    
자바스크립트는 기본적으로 변수 자체에 이러한 접근 권한을 직접 부여하도록 설계되지 않았다.    
→ 클로저를 이용하면 함수 차원에서 public한 값과 private한 값을 구분하는 것이 가능하다.    
    
```jsx
var t = '트위치'          // 전역 global, 전역 범위 (global scope)

var outer = function () {
var a = '아리';           // outerEnvironmentReference, 외부 함수 범위(outer functions scope)

	function inner () {     // 내부 함수이며, 클로저
		var b = '브라움'      // environmentRecord, 지역 범위 (local scope)
		console.log(a);       // a -> '아리', b -> '브라움', t -> '트위치'
	}
	return inner           // inner 함수를 return
}

var outer2 = outer();    // inner 함수를 받음. 
outer2();                 // inner 함수를 실행, a 변수에 접근
```
    
outer 함수는 외부(전역 스코프)로부터 철저하게 격리된 닫힌 공간. 외부에서는 outer라는 변수를 통해 outer 함수를 실행. 내부에는 어떠한 개입 X. outer 함수가 return한 정보에만 접근할 수 있다.    
- 외부에 제공하고자 하는 정보들을 모아서 return
- 내부에서만 사용할 정보들은 return 하지 않는 것
