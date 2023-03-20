# 03-Passing data through props

사용자가 사각형을 클릭할 때 사각형의 값을 빈 값에서 "X"로 변경하려고 한다. 지금까지 보드를 만든 방법인 9번의 복사 붙여넣기 방식 대신 React의 컴포넌트 아키텍쳐를 사용하면 지저분하고 중복되는 코드를 피할 수 있는 재사용 가능한 컴포넌트를 만들 수 있다.

먼저, Board 컴포넌트에서 첫 번째 사각형을 정의하는 줄을 새 Square 컴포넌트로 만든다.

```jsx
function Square() {
  return <button className="square">1</button>;
}

export default function Board() {
  // ...
}
```

다음으로 JSX 구문을 사용하여 해당 Square 컴포넌트를 렌더링하도록 Board 컴포넌트를 수정한다.

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

div처럼 일반적인 HTML 요소와는 다르게 컴포넌트인 Board와 Square는 대문자로 시작해야 한다.

확인해보면?

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

이 전에 가지고 있던 번호가 매겨진 사각형이 사라지고, 각 사각형에는 1만 표시되었다. 이 문제를 해결하려면 props를 사용하여 각 사각형이 가져야 하는 값을 상위 컴포넌트(Board)에서 하위 컴포넌트(Square)로 전달해야한다.

그럼 Board 컴포넌트에서 보낼 value prop을 받도록 Square 컴포넌트를 수정해보자.

```jsx
function Square({ value }) {
  return <button className="square">1</button>;
}
```

<mark style="color:purple;">**function Square({ value })**</mark>는 Square 컴포넌트가 value라는 prop을 받을 수 있음을 나타낸다.

이제 모든 사각형 안에 1 대신 해당 value를 표시하려고 한다.

```jsx
function Square({ value }) {
  return <button className="square">value</button>;
}
```

확인해보면?

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

렌더링하려고 한 건 "value"라는 단어가 아니라 value 변수이다. JSX에서 자바스크립트로 탈출하려면 중괄호가 필요하다.

```jsx
function Square({ value }) {
  return <button className="square">{value}</button>;
}
```

이렇게 하면 빈 보드를 볼 수 있는데, 이는 Board 컴포넌트에서 각 Square 컴포넌트에 value prop을 전달하지 않았기 때문이다. Board 컴포넌트가 렌더링하는 각 Square 컴포넌트에 value prop을 추가해보자.

```jsx
export default function Board() {
  return (
    <>
      <div className="board-row">
        <Square value="1" />
        <Square value="2" />
        <Square value="3" />
      </div>
      <div className="board-row">
        <Square value="4" />
        <Square value="5" />
        <Square value="6" />
      </div>
      <div className="board-row">
        <Square value="7" />
        <Square value="8" />
        <Square value="9" />
      </div>
    </>
  );
}
```

이제 숫자 그리드를 다시 볼 수 있다.

> Reference\
> [https://beta.reactjs.org/](https://beta.reactjs.org/)\
> **2023.03.14**
