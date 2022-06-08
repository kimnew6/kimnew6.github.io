---
layout: single
title: "ReactJS useState"
---

# useState를 사용하여 컴포넌트에서 상태 관리하기

### 컴포넌트에서 보여줘야 하는 내용이 사용자 인터랙션에 따라 바뀌어야 할 때 어떻게 구현할 수 있을까?
useState 라는 함수를 사용해보게 되는데, 이게 바로 리액트의 Hooks 중 하나이다.   
간단히, 버튼을 누르면 숫자가 바뀌는 Counter 컴포넌트를 만들어 보자.    

### Counter.jsx
```js
import React from 'react';
import Button from '@mui/material/Button';
import styled from 'styled-components';

const Counter = () => {
  const onIncrease = () => {
    console.log('+1');
  };
  const onDecrease = () => {
    console.log('-1');
  };
  return (
    <>
      <h1>0</h1>
      <Div>
        <Button variant="contained" onClick={onDecrease}>
          -1
        </Button>
        <Button variant="contained" onClick={onIncrease}>
          +1
        </Button>
      </Div>
    </>
  );
};

export default Counter;
```
      
Counter 를 렌더링 해보자.    
      
<img width="396" alt="스크린샷 2022-06-04 오후 8 20 32" src="https://user-images.githubusercontent.com/84711115/171997170-ce8c5bfb-b658-4053-8e46-54f7a5e2883c.png">
    
Counter 에서 버튼이 클릭되는 이벤트가 발생 했을 때, 특정 함수가 호출되도록 설정 해 두었다.   
    
`onIncrease` 와 `onDecrease` 는 화살표 함수를 사용하여 구현 해주었다.   
함수를 만들고, button 의 `onClick` 으로 각 함수를 연결해주었다. 리액트에서 엘리먼트에 이벤트를 설정해줄때에는 `on이벤트이름={실행하고싶은함수}` 형태로 설정해주어야 한다.   

아래와 같이 함수를 실행하면 안 된다.     

```js
onClick={onIncrease()}
```

이렇게 하면 렌더링되는 시점에서 함수가 호출되어버리기 때문에, 이벤트를 설정할 때에는 함수타입의 값을 넣어주어야 한다는 것에 주의해야한다.     

<img width="307" alt="스크린샷 2022-06-04 오후 8 21 22" src="https://user-images.githubusercontent.com/84711115/171997151-efeb7236-a16f-4bb6-b38a-04af52ca15eb.png">
버튼을 누르면 위 그림과 같이 콘솔에 메시지들이 출력 된다.     

### 동적인 값 끼얹기, useState
컴포넌트에서 동적인 값을 상태(state)라고 부른다. 리액트에 `useState` 라는 함수가 있는데, 이 것을 사용하면 컴포넌트에서 상태를 관리 할 수 있다.    
    
다음과 같이 코드를 수정해보자.   

### Counter.jsx
```js
import React, { useState } from 'react';   // 리액트 패키지에서 'useState' 라는 함수를 불러와준다.
import Button from '@mui/material/Button';
import styled from 'styled-components';

const Counter = () => {
  const [number, setNumber] = useState(0);
  const onIncrease = () => {
    setNumber(number + 1);
  };
  const onDecrease = () => {
    setNumber(number - 1);
  };
  return (
    <>
      <h1>{number}</h1>
      <Div>
        <Button variant="contained" onClick={onDecrease}>
          -1
        </Button>
        <Button variant="contained" onClick={onIncrease}>
          +1
        </Button>
      </Div>
    </>
  );
};

export default Counter;
```
      

```js
const [number, setNumber] = useState(0);
```
`useState` 를 사용 할 때에는 상태의 기본값을 파라미터로 넣어서 호출해준다. 이 함수를 호출해주면 배열이 반환되는데, 여기서 첫번째 원소는 현재 상태이고, 두번째 원소는 Setter 함수이다.    

```js
const nuberState = useState(0);
const number = numberState[0];
const setNumber = numberState[1];
```
      
원래는 위와 같이 해야하지만, `배열 비구조화 할당`을 통하여 각 원소를 추출해준것이다.     

```js
<h1>{number}</h1>
```
h1 태그에서는 이제 0이 아닌 `{number}` 값을 보여주어야 한다.   
      
코드를 다 수정 한 후, 버튼을 눌러 숫자가 잘 바뀌는 지 확인 해보자.      

<img width="359" alt="스크린샷 2022-06-04 오후 8 52 13" src="https://user-images.githubusercontent.com/84711115/171997754-81251d4e-6e3b-4b92-add3-63a31da78a53.png">
      
### 함수형 업데이트
지금은 Setter 함수를 사용 할 때, 업데이트 하고 싶은 새로운 값을 파라미터로 넣어주고 있는데, 그 대신에 기존 값을 어떻게 업데이트 할 지에 대한 함수를 등록하는 방식으로도 값을 업데이트 할 수 있다.    

### Counter.jsx
```js
import React, { useState } from 'react';
import Button from '@mui/material/Button';
import styled from 'styled-components';

const Counter = () => {
  const [number, setNumber] = useState(0);
  const onIncrease = () => {
    setNumber(prevNumber => prevNumber + 1);
  };
  const onDecrease = () => {
    setNumber(prevNumber => prevNumber - 1);
  };
  return (
    <>
      <h1>{number}</h1>
      <Div>
        <Button variant="contained" onClick={onDecrease}>
          -1
        </Button>
        <Button variant="contained" onClick={onIncrease}>
          +1
        </Button>
      </Div>
    </>
  );
};

export default Counter;
```
      
`onIncrease` 와 `onDecrease` 에서 `setNumber` 를 사용 할 때 그 다음 상태를 파라미터로 넣어준 것이 아니라, 값을 업데이트 하는 함수를 파라미터로 넣어주었다.    
      
함수형 업데이트는 컴포넌트를 최적화 할 때 사용하게 된다. 다음에 더 깊이 알아보자.
[벨로퍼트와 함께하는 모던 리액트](https://react.vlpt.us/basic/07-useState.html)
