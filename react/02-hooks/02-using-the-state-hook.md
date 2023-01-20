# 02-Using the State Hook

## Hook과 같은 기능을 하는 클래스 컴포넌트

```jsx
class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }
  
  render() {
    return (
      <div>
        <p>You clicked {this.state.count} times</p>
        <button onClick={() => this.setState({ count: this.state.count + 1})}>
          Click me
        </button
      </div>
    );
  }
}
```

위 코드에서 state는 { count: 0 } 이며 사용자가 this.setState()를 호출하는 버튼을 클릭하면 state.count를 증가시킨다.

## Hook과 함수 컴포넌트

React의 함수 컴포넌트는 다음과 같이 생겼다.

```jsx
const Example = (props) => {
  // 여기서 Hook을 사용할 수 있다.
  return <div />;
}
```

또는 이렇게 생겼다.

```jsx
function Example(props) {
  // 여기서 Hook을 사용할 수 있다.
  return <div />;
}
```

이 Hook은 클래스 안에서 동작하지 않는다. 하지만 클래스를 작성하지 않고 사용할 수 있다.

## Hook이란?

React의 <mark style="color:purple;">**useState**</mark> Hook을 사용해보자.

```jsx
import React, { useState } from 'react';

function Example() {
  // ...
}

```

<mark style="color:purple;">**Hook이란?**</mark> Hook은 리액트 기능에 말 그대로 "hook into(연결)"할 수 있는 특별한 함수이다. 예를 들어 useState는 state를 함수 컴포넌트에 연결해서 그 안에서 사용할 수 있게 해준다.

## state 변수 선언하기

클래스 컴포넌트에서는 constructor 안에서 this.state를 { count: 0 } 으로 설정함으로써 count를 0으로 초기화했다.

```javascript
class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }
...
```

반면 함수 컴포넌트는 this를 가질 수 없기 때문에 this.state를 할당하거나 읽을 수 없다. 대신, useState Hook을 직접 컴포넌트에 호출한다.

```jsx
import React, { useState } from 'react';

function Example() {
  // 새로운 state 변수 선언
  const [count, setCount] = useState(0);
...
```

<mark style="color:purple;">**useState를 호출하는 것의 의미?**</mark> state 변수 선언.

<mark style="color:purple;">**useState의 인자로 무엇을 넘겨주어야 할까?**</mark> state의 초기 값.

<mark style="color:purple;">**useState는 무엇을 반환할까?**</mark> state 변수, setState 함수.

즉, count 변수를 선언하고 0으로 초기화한다. React는 해당 변수의 현재 값을 기억하고, 값이 바뀌면 리렌더링과 함께 가장 최근에 업데이트된 값을 제공한다. count 변수의 값을 업데이트 하려면 setCount를 호출하면 된다.

## Reading State

클래스 컴포넌트는 count state를 보여주기 위해 this.state.count를 사용한다.

```jsx
<p>You Clicked {this.state.count} times</p>
```

반면 함수 컴포넌트는 count를 직접 사용할 수 있다.

```jsx
<p>You Clicked {count} times</p>
```

## Updating State

클래스 컴포넌트에서는 count를 갱신하기 위해 this.setState()를 호출한다.

```jsx
<button onClick={() => this.setState({ count: this.state.count + 1})}>
  Click me
</button>
```

반면 함수 컴포넌트는 setCount와 count 변수를 가지고 있으므로 this를 호출하지 않아도 된다.

```jsx
<button onClick={() => setCount(count + 1)}>
  Click me
</button>
```

## 요약

```jsx
 1:  import React, { useState } from 'react';
 2:
 3:  function Example() {
 4:    const [count, setCount] = useState(0);
 5:
 6:    return (
 7:      <div>
 8:        <p>You clicked {count} times</p>
 9:        <button onClick={() => setCount(count + 1)}>
10:         Click me
11:        </button>
12:      </div>
13:    );
14:  }
```

* Line 1: useState Hook을 React에서 가져온다.
* Line 4: useState Hook을 state 변수와 해당 state를 업데이트할 수 있는 함수가 만들어진다. 또한 useState의 인자값으로 0을 넘겨주면 count 값을 0으로 초기화할 수 있다.
* Line 9: 사용자가 버튼을 클릭하면 setCount 함수를 호출하여 state 변수를 업데이트 한다. React는 새로운 count 변수를 \<Example /> 컴포넌트에 넘기며 해당 컴포넌트를 리렌더링한다.

> Reference
>
> [https://reactjs.org/](https://reactjs.org/)
>
> **2023.01.20**
