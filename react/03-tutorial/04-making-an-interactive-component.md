# 04-Making an interactive component

Square 컴포넌트를 클릭하면 사각형을 X로 채워보자.

먼저 컴포넌트 내부에 handleClick이라는 함수를 선언한다. 그 다음 컴포넌트에서 return하는 버튼의 prop에 onClick을 추가한다.

```jsx
function Square({ value }) {
  function handleClick() {
    console.log('clicked!');
  }

  return (
    <button
      className="square"
      onClick={handleClick}
    >
      {value}
    </button>
  );
}
```

이제 사각형을 클릭하면 브라우저의 콘솔 탭에서 "clicked!"라는 log를 볼 수 있다.

다음 단계로 Square 컴포넌트가 클릭된 것을 "기억"하고 X 표시로 채우길 원한다. 컴포넌트는 무언가를 기억하기 위해 state를 사용한다.

React는 컴포넌트에서 호출하여 무언가를 "기억"할 수 있는 useState라는 특수한 함수를 제공한다. Square 컴포넌트의 현재 값을 state에 저장해보고, 클릭 시 변경하도록 해보자.

파일 상단에서 useState를 Import하고, Square 컴포넌트에서 value prop을 제거하자. 대신 useState를 호출하는 Square 컴포넌트의 시작 부분에 value라는 state 변수를 추가하자.

```jsx
import { useState } from 'react';

function Square() {
  const [value, setValue] = useState(null);

  function handleClick() {
    //...
```

value는 값을 저장하는 용도로, setValue는 값을 변경하는 용도로 사용하는 함수이다. useState에 전달된 null은 이 state 변수의 초기 값으로 사용되므로 여기서 value는 null로 시작한다.

Square 컴포넌트는 더 이상 props를 받지 않으므로, Board 컴포넌트에 의해 생성된 9개의 Square 컴포넌트에서 value prop을 제거한다.

```jsx
// ...
export default function Board() {
  return (
    <>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
    </>
  );
}
```

이제 사각형 클릭 시 X 표시를 나타내도록 Square 컴포넌트 내 `console.log("clicked!");`를 `setValue("X");`로 수정한다.

```jsx
function Square() {
  const [value, setValue] = useState(null);

  function handleClick() {
    setValue('X');
  }

  return (
    <button
      className="square"
      onClick={handleClick}
    >
      {value}
    </button>
  );
}
```

onClick 핸들러에서 이 set 함수를 호출하면 \<button>이 클릭될 때마다 해당 Square 컴포넌트를 다시 렌더링하도록 React에 지시한다. 렌더링 후 Square 컴포넌트의 value는 "X"가 되어 게임 보드에 "X"가 표시된다.

각 Square 컴포넌트에는 고유한 state가 있다. 즉 value에 저장된 값이 서로 완전히 독립적이라는 의미이다. 컴포넌트에서 set 함수를 호출하면 React는 내부의 자식 컴포넌트도 자동으로 업데이트한다.

> Reference\
> [https://beta.reactjs.org/](https://beta.reactjs.org/)\
> **2023.03.14**
