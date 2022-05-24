---
layout: single
title:  "Welcome to Luluzoe'Blog"
---

# 조건부 렌더링
조건부 렌더링이란, 특정 조건에 따라 다른 결과물을 렌더링 하는 것을 의미한다.  
React에서 조건부 렌더링은 JavaScript에서의 조건 처리와 같이 동작한다. 예를 들어, App 컴포넌트에서 Hello 컴포넌트를 사용 할 때, `isSpecial` 이라는 props를 설정 해 보았다.

### Home.js
```js
import React from 'react';
import Hello from './Hello';

const Home = () => {
  return (
    <>
      <Hello name="LuluZoe" color="#800080" isSpecial={true} />
      <Hello color="pink" />
    </>
  );
};

export default Home;

```
`isSpecial` 의 `true` 는 자바스크립트 값이므로 중괄호로 감싸준다.   
Hello 컴포넌트에서는 isSepcial이 `true` 냐 `false` 냐에 따라서 컴포넌트의 좌측에 * 표시를 보여주도록 하겠다.   
이를 처리하는 가장 기본적인 방법은, 삼항연산자를 사용하는 것이다.


### Hello.js
```js
import React from 'react';

const Hello = ({ color, name, isSpecial }) => {
  return (
    <h1 style={{ color }}>
      {isSpecial ? <b>*</b> : null}
      {name}'s React Hooks
    </h1>
  );
};

Hello.defaultProps = {
  name: '이름 없음',
};

export default Hello;
```

`isSpecial` 값이 `true` 라면 `<b>*</b>` 를, 그렇지 않다면 `null` 을 보여주도록 했다. 참고로 JSX에서 null, false, undefined 를 렌더링하게 된다면 아무것도 나타나지 않게 된다.

<img width="466" alt="스크린샷 2022-05-24 오후 6 38 55" src="https://user-images.githubusercontent.com/84711115/170001531-ee6d5faa-7403-4565-b7a7-0adcb2942d6c.png">

보통 삼항연산자를 사용한 조건부 렌더링을 주로 특정 조건에 따라 보여줘야 하는 내용이 다를 때 사용한다.    
지금은 내용이 달라지는게 아니라, 단순히 특정 조건이 `true`면  보여주고, 그렇지 않다면 숨겨주고 있는데, 이러한 상황에서는 `&&` 연산자를 사용해서 처리하는 것이 더 간편하다.

### Hello.js
```js
import React from 'react';

const Hello = ({ color, name, isSpecial }) => {
  return (
    <h1 style={{ color }}>
      {isSpecial && <b>*</b>}
      {name}'s React Hooks
    </h1>
  );
};

Hello.defaultProps = {
  name: '이름 없음',
};

export default Hello;
```

`isSpecial && <b>*</b>` 의 결과는 `isSpecial` 이 `false` 일땐 `false` 이고, `isSpecial` 이 `true` 일 땐 `<b>*</b>` 가 된다.    
## props 값 설정을 생략하면 ={true}
컴포넌트의 props 값을 설정하게 될 때 만약 props 이름만 작성하고 값 설정을 생략한다면, 이를 `true` 로 설정한 것으로 간주한다.
### Home.js

```js
import React from 'react';
import Hello from './Hello';

const Home = () => {
  return (
    <>
      <Hello name="LuluZoe" color="#800080" isSpecial />
      <Hello color="pink" />
    </>
  );
};

export default Home;

```
이렇게 `isSpecial` 이름만 넣어주면 `isSpecial={true}` 와 동일한 의미이다.

#### 참조
[벨로퍼트와 함께하는 모던 리액트](https://react.vlpt.us/basic/06-conditional-rendering.html)    
[React 공식문서](https://ko.reactjs.org/docs/conditional-rendering.html)
