---
layout: single
title: "ReactJS useState"
---

# useState를 사용하여 컴포넌트에서 상태 관리하기


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

<img width="396" alt="스크린샷 2022-06-04 오후 8 20 32" src="https://user-images.githubusercontent.com/84711115/171997170-ce8c5bfb-b658-4053-8e46-54f7a5e2883c.png">

<img width="307" alt="스크린샷 2022-06-04 오후 8 21 22" src="https://user-images.githubusercontent.com/84711115/171997151-efeb7236-a16f-4bb6-b38a-04af52ca15eb.png">

### Counter.jsx
```js
import React, { useState } from 'react';
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
<img width="359" alt="스크린샷 2022-06-04 오후 8 52 13" src="https://user-images.githubusercontent.com/84711115/171997754-81251d4e-6e3b-4b92-add3-63a31da78a53.png">

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
