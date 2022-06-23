---
layout: single
title: "CoreJS callback"
---

## 4-1 콜백 함수란?

**콜백 함수(callback function)는 다른 코드의 인자로 넘겨주는 함수**

콜백 함수를  넘겨받은 코드는 이 콜백 함수를 필요에 따라 **적절한 시점에 실행**할 것

콜백 함수는 **제어권**과 관련이 깊다.

callback = call(부르다, 호출하다) + back(뒤돌아오다, 되돌다)

어떤 함수 X를 호출하면서 ’특정 조건일 때 함수 Y를 실행해서 나에게 알려달라’는 요청을 함께 보낸 것

콜백 함수는 다른 코드(함수 또는 메서드)에게 인자로 넘겨줌으로써 그 **제어권도 함께 위임한 함수**

## 4-2 제어권

```jsx
// 예제 4-2 콜백 함수 예제(1-2) setInterval
var count = 0;
var cbFunc = function () {
	console.log(count);
	if(++count > 4) clearInterval(timer);
};
var timer = setInterval(cbFunc, 300);

// -- 실행 결과 --
// 0  (0.3초)
// 1  (0.6초)
// 2  (0.9초)
// 3  (1.2초)
// 4  (1.5초)
```

`timer` 변수에 `setInterval`의 ID 값이 담김.

`setInterval`에 전달한 첫 번째 인자인 `cbFunc` 함수(이 함수가 콜백 함수)는 0.3초마다 자동으로 실행.

콜백 함수 내부에서는 `count` 값을 출력, `count`를 1만큼 증가, 4보다 크면 반복 실행 종료.

| code | 호출 주체 | 제어권 |
| --- | --- | --- |
| cbFunc(); | 사용자 | 사용자 |
| setInterval(cbFunc, 300); | setInterval | setInterval |

`setInterval` 이라고 하는 ‘다른 코드’에 첫 번째 인자로서 `cbFunc` 함수를 넘겨주자 넘겨받은 `setInterval`이 스스로의 판단에 따라 적절한 시점에(0.3초마다) 이 익명 함수를 실행

콜백 함수의 제어권을 넘겨받은 코드는 콜백 함수 호출 시점에 대한 **제어권**을 가진다.

### callback 함수가 map 메서드 일 때

‘알람용 침이 원하는 시각을 가리키도록 정하고 알람 스위치를 ON으로 설정해야 한다’ 라는 규칙을 따라야만 한다. 마찬가지로 map 메서드를 호출해서 원하는 배열을 얻으려면 map 메서드에 정의된 **규칙에 따라 함수를 작성** 해야함. 그 규칙에는 콜백 함수의 **인자로 넘어올 값들 및 그 순서**도 포함.

### this

**“콜백 함수도 함수이기 때문에 기본적으로 this가 전역객체를 참조하지만, 제어권을 넘겨받을 코드에서 콜백 함수에 별도로 this가 될 대상을 지정한 경우에는 그 대상을 참조하게 된다”**

this에 다른 값이 담기는 이유 : 제어권을 넘겨받을 코드에서 call/apply 메서드의 첫 번째 인자에 콜백 함수 내부에서의 this가 될 대상을 명시적으로 바인딩하기 때문

```jsx
01 setTimeout(function () { console.log(this); }, 300); //(1) Window { ... }
02
03 [1, 2, 3, 4, 5].forEach(function (x) {
04     console.log(this);                               //(2) Window { ... }
05 });
06
07 document.body.innerHTML += '<button id="a">클릭</button>';
08 document.body.querySelector('#a')
09     .addEventListener('click', function (e) {
10     console.log(this, e);                            //(3)<button id="a">클릭</button>
11   }                                             // MouseEvent { isTrusted: true, ... }
12 );
```

(1)의 setTimeout은 내부에서 콜백 함수를 호출할 때 call 메서드의 첫 번째 인자에 전역객체를 넘기기 때문에 **콜백 함수 내부에서의 this가 전역객체**

(2)의 forEach는 별도의 인자로 this를 넘겨주지 않았기 때문에 **전역객체를 가리킴**

(3)의 addEventListener 메서드의 this를 그대로 넘기도록 정의돼 있기 때문에 this가 addEventListener를 호출한 주체인 **HTML 엘리먼트를 가리킴**

## 4-3 콜백함수는 함수다

콜백 함수로 어떤 객체의 메서드를 전달하더라도 그 메서드는 **메서드가 아닌 함수**로서 호출된다.

```jsx
01 var obj = {
02 	vals: [1, 2, 3],
03 	logValues: function(v, i) {
04 		console.log(this, v, i);
05 	  }
06 };
07 obj.logValues(1, 2);               // { vals: [1, 2, 3], logValues: f } 1 2
08 [4, 5, 6].forEach(obj.logValues);  // Window { ... } 4 0
09                                    // Window { ... } 5 1
10                                    // Window { ... } 6 2
```

obj 객체의 logValues는 메서드로 정의. 7번째 줄에서 메서드의 이름 앞에 점이 있으니 메서드로 호출 한 것. 따라서 this는 obj를 가리키고, 인자로 넘어온 1, 2가 출력 됨.

8번째 줄에서는 메서드를 forEach 함수의 콜백 함수로 전달. obj를 this로 하는 메서드를 그대로 전달한 것이 아니고, obj.logValues가 가리키는 함수만 전달.

obj와의 직접적인 연관이 없어짐. forEach에 의해 콜백이 함수로서 호출, 함수 내부에서의 **this는 전역객체**를 바라보게 됨.

**어떤 함수의 인자에 객체의 메서드를 전달하더라도 이는 결국 메서드가 아닌 함수일 뿐**

## 4-4 콜백함수의 this에 다른 값 바인딩하기

### 콜백 함수 내부에서 this가 객체를 바라보게 하고 싶다면?

별도의 인자로 this를 받는 함수의 경우에는 원하는 값을 넘겨주면 된다.

그렇지 않은 경우에는 this의 제어권도 넘겨주게 되므로 사용자가 임의로 값을 바꿀 수 없다.

전통적으로는 this를 다른 변수에 담아 콜백 함수로 활용할 함수에서는 this 대신 그 변수를 사용하게 하고, 이를 클로저로 만드는 방식이 많이 쓰임.

## 4-5 콜백 지옥과 비동기 제어

**콜백 지옥(callback hell)** : 콜백 함수를 익명 함수로 전달하는 과정이 반복되어 코드의 들여쓰기 수준이 감당하기 힘들 정도로 깊어지는 현상

주로 이벤트 처리나 서버 통신과 같이 비동기적인 작업을 수행하기 위해 이런형태가 자주 등장. 가독성이 떨어지고 코드 수정도 어려움.

비동기 ↔ 동기

**동기적인 코드** : 현재 실행 중인 코드가 완료된 후에 다음 코드를 실행하는 방식

- CPU 계산에 의해 즉시 처리가 가능한 대부분의 코드
- 계산식이 복잡해서 CPU가 계산하는 데 시간이 많이 필요한 경우라도

**비동기적인 코드** : 현재 실행 중인 코드의 완료 여부와 무관하게 즉시 다음 코드로 넘어감

- 사용자 요청에 의해 특정 시간이 경과되기 전까지 실행 보류(setTimeout)
- 사용자의 직접적인 개입이 있을 때 실행(addEventListener)
- 웹브라우저 자체가 아닌 별도의 대상에 무언가를 요청하고, 응답이 왔을 때 함수 실행(XMLHttpRequest)
- **별도의 요청, 실행 대기, 보류**

현대의 자바스크립트는 웹의 복잡도 ⬆️, 비동기적인 코드의 비중도 ⬆️

그와 동시에 콜백 지옥에 빠지기 쉬워진 셈

**자바스크립트** 진영은 비동기적인 일련의 작업을 **동기적으로 혹은 동기적인 것처럼** 보이게 노력

**ES6**에서는 **`Promise`, `Generator`** 도입, **ES2017**에서는 `async/await` 도입

```jsx
 var addCoffee = function (name) {
	return new Promise(function (resolve) {
		setTimeout(function () {
			resolve(name);
		}, 500);
	});
};
var coffeeMaker = async function () {
	var coffeeList = '';
	var _addCoffee = async function (name) {
		coffeeList += (coffeeList ? ',' : '') + await addCoffee(name);
	};
	await _addCoffee('에스프레소');
	console.log(coffeeList);
	await _addCoffee('아메리카노');
	console.log(coffeeList);
	await _addCoffee('카페모카');
	console.log(coffeeList);
	await _addCoffee('카페라떼');
	console.log(coffeeList);
};
coffeeMaker();
```

비동기 작업을 수행하고자 하는 함수 앞에 `async`를 표기

함수 내부에서 실질적인 비동기 작업이 필요한 위치마다 `await`를 표기하는 것만으로 뒤의 내용을 `Promise`로 자동 전환하고, 해당 내용이 `resolve`된 이후에야 다음으로 진행

`Promise`의 `then`과 흡사한 효과
