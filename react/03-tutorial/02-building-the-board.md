# 02-Building the board

App.js로 돌아가보자.

현재 보드는 하나의 정사각형이지만 9개가 필요하다. 정사각형을 복사하여 붙여 넣어 다음과 같이 두 개의 정사각형을 만들 수 있다.

```jsx
export default function Square() {
  return <button className="square">X</button><button className="square">X</button>;
}
```

<figure><img src="../../.gitbook/assets/image (2) (5).png" alt=""><figcaption></figcaption></figure>

위와 같은 오류가 발생할텐데, 리액트 컴포넌트는 두 개의 버튼과 같이 여러 개의 인접한 JSX 요소가 아닌 단일 JSX를 return 해야 한다.

이 문제를 해결하려면 fragments를 사용하여 다음과 같이 JSX 요소를 래핑할 수 있다.

```jsx
export default function Square() {
  return (
    <>
      <button className="square">X</button>
      <button className="square">X</button>
    </>
  );
}
```

다만 가로로 버튼이 추가되는데, 이 문제를 해결하려면 버튼을 div를 이용하여 행으로 그룹화하고 일부 CSS 클래스를 추가해야 한다.

먼저 App.js 파일에서 Square 컴포넌트를 다음과 같이 수정한다.

```jsx
export default function Square() {
  return (
    <>
      <div className="board-row">
        <button className="square">1</button>
        <button className="square">2</button>
        <button className="square">3</button>
      </div>
      <div className="board-row">
        <button className="square">4</button>
        <button className="square">5</button>
        <button className="square">6</button>
      </div>
      <div className="board-row">
        <button className="square">7</button>
        <button className="square">8</button>
        <button className="square">9</button>
      </div>
    </>
  );
}
```

다음으로 styles.css에 정의된 CSS는 className 프로퍼티가 board-row로 지정된 div의 스타일을 지정한다.

```css
.board-row:after {
  clear: both;
  content: '';
  display: table;
}
```

이렇게 하면 tic-tac-toe 보드를 볼 수 있다.

<figure><img src="../../.gitbook/assets/image (1) (2).png" alt=""><figcaption></figcaption></figure>

다음으로 Square 컴포넌트는 더 이상 정사각형이 아니니, 컴포넌트 이름을 Board로 수정하자.

```jsx
export default function Board() {
  //...
}
```

> Reference\
> [https://beta.reactjs.org/](https://beta.reactjs.org/)\
> **2023.03.14**
