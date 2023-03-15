# 05-Lifting state up

tic-tac-toe 게임을 위한 기본 블럭은 갖췄다. 이제 보드에 X와 O를 번갈아 배치하는 방법과 승자를 결정하는 방법이 필요하다.

현재 Square 컴포넌트는 state의 일부를 유지한다. 승자를 확인하려면 Board 컴포넌트는 어떻게든 9개의 Square 컴포넌트 각각의 state를 알아야 한다.

어떻게 접근해야 할까? 첫 번째로는 Board 컴포넌트가 Square 컴포넌트에 state를 알려달라고 요청하는 것이 필요하다고 추측할 수 있다. 하지만 이러한 접근 방식은 React에서 기술적으로 가능하지만, 코드를 이해하기 어렵고 버그에 취약하며 리팩터링하기 어렵기 때문에 권장하지 않는다.

대신 가장 좋은 방법은 게임의 state를 각 Square 컴포넌트 대신 Board 컴포넌트(부모)에 저장하는 것이다. Board 컴포넌트는 각 Square 컴포넌트에 prop을 전달하여 무엇을 표시할지 알려줄 수 있다.

여러 자식 컴포넌트에서 데이터를 수집하거나, 두 자식이 서로 데이터 교환이 가능하도록 하려면 공유하고자 하는 state를 부모 컴포넌트에 선언해야 한다. 부모 컴포넌트는 props를 통해 해당 state를 자식에게 다시 전달할 수 있다.

state를 부모 컴포넌트로 끌어올리는 것은 일반적인 리팩토링 방안이다.

먼저 9개의 null 배열을 기본값으로 하는 squares state 변수를 선언하도록 Board 컴포넌트를 수정해보자.

```jsx
// ...
export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));
  return (
    // ...
  );
}
```

squares state 변수에 기본값으로 설정된 9개의 배열의 각 요소는 정사각형의 value에 해당한다. 나중에 보드를 채울 때 squares 배열은 다음과 같이 표시된다.

```jsx
['O', null, 'X', 'X', 'X', 'O', 'O', null, null]
```

이제 Board 컴포넌트는 각 Square 컴포넌트에 value prop을 전달해야 한다.

```jsx
export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));
  return (
    <>
      <div className="board-row">
        <Square value={squares[0]} />
        <Square value={squares[1]} />
        <Square value={squares[2]} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} />
        <Square value={squares[4]} />
        <Square value={squares[5]} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} />
        <Square value={squares[7]} />
        <Square value={squares[8]} />
      </div>
    </>
  );
}
```

다음으로 Board 컴포넌트에서 보낸 value prop을 받을 수 있도록 Square 컴포넌트를 수정해보자. 컴포넌트가 가지고 있던 state와 onClick 핸들러를 제거한다.

```jsx
function Square({value}) {
  return <button className="square">{value}</button>;
}
```

이렇게 하면 정사각형이 빈 칸으로 보일텐데, 각 Square 컴포넌트는 X, O 또는 빈 사각형일 경우 null인 value prop을 받도록 할 것이다.

이제 Square 컴포넌트를 클릭했을 때 일어나는 일을 변경해야 한다. Board 컴포넌트는 이제 채워진 사각형을 유지해야 하므로, Square 컴포넌트가 Board 컴포넌트의 state를 업데이트하는 방법을 만들어야 한다. state는 state를 정의한 컴포넌트에 private하기 때문에 Square 컴포넌트에서 직접 Board 컴포넌트의 state를 업데이트 할 수는 없다.

대신 Board 컴포넌트에서 Square 컴포넌트로 함수를 전달하고, 사각형을 클릭할 때 Square 컴포넌트가 해당 함수를 호출하도록 한다.

먼저 Square 컴포넌트가 클릭될 때 호출하는 함수(onSquareClick)와 함께 시작한다.

```jsx
function Square({ value }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}
```

다음으로 해당 함수를 Square 컴포넌트의 props에 추가해준다.

```jsx
function Square({ value, onSquareClick }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}
```

이제 이 함수를 Board 컴포넌트의 handleClick 함수와 연결한다. 이때 Square 컴포넌트의 onSquareClick 함수를 prop으로, Board 컴포넌트의 handleClick 함수를 값으로 작성한다.

```jsx
export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));

  return (
    <>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={handleClick} />
        //...
  );
}
```

마지막으로 Board 컴포넌트 내부에 handleClick 함수를 정의하여 squares state 배열을 업데이트한다.

```jsx
export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));

  function handleClick() {
    const nextSquares = squares.slice();
    nextSquares[0] = "X";
    setSquares(nextSquares);
  }

  return (
    // ...
  )
}
```

handleClick 함수는 자바스크립트의 slice 메서드를 이용하여 squares state 배열의 복사본을 생성한다. 이후 복사본의 배열을 업데이트 하여 첫 번째 인덱스에 X를 추가한다.

다음으로 setState 함수를 호출하여 React가 컴포넌트의 state 변경을 알 수 있도록 한다. 이는 squares state를 사용하는 Board 컴포넌트와 자식 컴포넌트(Square)의 리렌더링을 트리거한다.

이제 보드에 X를 추가할 수 있는데, 문제는 왼쪽 상단 사각형에만 가능하다. handleClick 함수가 square state 배열 변수의 0번 째 인덱스를 업데이트 하도록 하드코딩되어 있기 때문이다. 다른 사각형도 업데이트할 수 있도록 수정해보자. handleClick 함수에 업데이트해야 하는 사각형의 인덱스를 가져오는 i 인수를 추가해보자.

```jsx
export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));

  function handleClick(i) {
    const nextSquares = squares.slice();
    nextSquares[i] = "X";
    setSquares(nextSquares);
  }

  return (
    // ...
  )
}
```

새로운 문제가 또 생기는데, onSquareClick prop을 다음과 같이 고쳐보자.

```jsx
<Square value={squares[0]} onSquareClick={handleClick(0)} />
```

handleClick(0) 호출은 Board 컴포넌트 렌더링의 일부이다. 왜냐하면 handleClick(0)이 setSquares를 호출하여 Board 컴포넌트의 state를 변경하기 때문에 전체 Board 컴포넌트가 리렌더링되기 때문이다.

이때 handleClick(0)은 Board 컴포넌트 렌더링의 일부이므로 무한 루프를 일으킨다.

왜 이 문제가 더 일찍 발생하지 않았을까?

onSquareClick={handleClick}을 전달할 땐 handleClick 함수를 prop으로 전달했다. 이때 함수 호출을 의도한 것은 아닌데 지금은 (0) 때문에 해당 함수를 즉시 호출하고 있다.

이 문제를 해결하려면 다음과 같이 화살표 함수를 이용할 수 있다.

```jsx
export default function Board() {
  // ...
  return (
    <>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        // ...
  );
}
```

화살표 문법은 함수를 정의하는 더 짧은 방법이다. 사각형을 클릭하면 화살표 뒤의 코드가 실행되어 handleClick(0)을 호출한다.

이제 다른 8개의 사각형을 수정해보자. handleClick의 각 호출에 대한 인수가 사각형의 올바른 인덱스에 해당하는 확인하자.

```jsx
export default function Board() {
  // ...
  return (
    <>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        <Square value={squares[1]} onSquareClick={() => handleClick(1)} />
        <Square value={squares[2]} onSquareClick={() => handleClick(2)} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} onSquareClick={() => handleClick(3)} />
        <Square value={squares[4]} onSquareClick={() => handleClick(4)} />
        <Square value={squares[5]} onSquareClick={() => handleClick(5)} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} onSquareClick={() => handleClick(6)} />
        <Square value={squares[7]} onSquareClick={() => handleClick(7)} />
        <Square value={squares[8]} onSquareClick={() => handleClick(8)} />
      </div>
    </>
  );
};
```

이제 보드의 사각형을 클릭하여 X를 추가할 수 있다. 이번에는 모든 state 관리가 Board 컴포넌트에 의해 처리된다.

state 처리를 Board 컴포넌트에서 진행하므로, 부모 컴포넌트는 자식 컴포넌트에 props를 전달하여 올바르게 화면에 나타날 수 있도록한다. Sqaure 컴포넌트를 클릭하면 이제 Board 컴포넌트에 state를 업데이트하도록 요청한다. Board 컴포넌트의 state가 변경되면 Board 컴포넌트와 모든 하위 컴포넌트가 모두 자동으로 리렌더링된다. Board 컴포넌트의 모든 squares state를 유지하면 나중에 승자를 결정할 수 있다.

사용자가 보드의 왼쪽 상단 사각형을 클릭하여 X를 추가하면 어떤 일이 발생하는지 요약해보자.

1. Square 컴포넌트의 \<button> 요소에서 onClick prop으로 받은 함수가 실행된다. Square 컴포넌트는 Board 컴포넌트로부터 onSquareClick prop으로 해당 함수를 받았다. Board 컴포넌트는 JSX에서 직접 해당 함수를 정의했으며 인수 0으로 handleClick을 호출한다.
2. handleClick은 인수를 사용하여 squares 배열의 첫 번째 요소를 null에서 X로 업데이트한다.
3. Board 컴포넌트의 squares state가 업데이트 되었으므로 Board 컴포넌트와 모든 자식 컴포넌트가 리렌더링된다.

결과로 사용자는 왼쪽 상단 사각형이 비어 있는 상태에서 클릭한 후 X 표시로 변경된 것을 볼 수 있다.

> Reference\
> [https://beta.reactjs.org/](https://beta.reactjs.org/)\
> **2023.03.15**
