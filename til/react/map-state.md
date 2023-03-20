# map 내부에서 사용되는 state를 조건부 또는 독립적으로 만드는 방법

## TL;DR

* 자바스크립트 map 메서드의 index 인수를 활용하여 state를 조건적으로 가질 수 있게 만들 수 있다.
* map 메서드 내부를 컴포넌트화하여 각 배열 요소가 갖는 state를 독립적으로 만들 수 있다.

```jsx
import { useState } from "react";

export default function App() {
  const [arrayList] = useState(Array(3).fill(null));
  const [message, setMessage] = useState(false);

  const onClickOpenMessage = () => setMessage((prev) => !prev);

  return (
    <>
      {arrayList.map((_, index) => (
        <>
          <div>array - {index}</div>
          <button onClick={onClickOpenMessage}>Click me!</button>
          {message && (
            <div
              style={{
                width: "150px",
                height: "50px",
                background: "yellowgreen",
                textAlign: "center",
                lineHeight: "50px",
              }}
            >
              I am message - {index}
            </div>
          )}
        </>
      ))}
    </>
  );
}
```

위 코드에서 의도한 건 각 버튼 클릭시 이에 해당하는 메세지가 하나씩만 열리는 것이다.

하지만 일어나는 일은? 어떤 버튼을 누르든 모든 모달이 다 열림.

<figure><img src="../../.gitbook/assets/image (9) (2).png" alt=""><figcaption></figcaption></figure>

컴포넌트를 분리하지 않고 작업을 하게 되면 마주하게 될 상황이다. 가령 List 페이지에서 카드 형식의 게시물을 뿌려줄 때 찜하기를 누르면 모든 게시물의 찜하기가 눌린다거나, 게시물 상단에 툴팁을 여는 버튼을 달아줄 때 버튼 클릭 시 모든 게시물의 툴팁이 열린다거나..

코드의 문제는 모든 버튼이 동일한 onClickOpenMessage 함수를 사용하기 때문에 클릭 시 각 배열 요소에 대한 모든 상태 변수가 업데이트 된다는 것인데, 해결 방안은 두 가지가 있다.

먼저 <mark style="color:purple;">**첫 번째로는 자바스크립트 map 메서드의 index 인수를 활용하는 방법**</mark>이다. 버튼 클릭시 OnClickOpenMessage(index) 함수를 실행시키는데, 이 함수는 초기값이 null인 openMessageIndex state에 index를 값으로 담고, 만약 현재 state와 index 값이 같다면 null을 값으로 담는다.(toggle)

클릭 후 openMessageIndex state에 담긴 값과 map 메서드의 index 인수를 비교하여 값이 같으면 message를 보여주게 된다.

```jsx
import { useState } from "react";

export default function App() {
  const [arrayList] = useState(Array(3).fill(null));
  const [openMessageIndex, setOpenMessageIndex] = useState(null);

  const onClickOpenMessage = (index) =>
    setOpenMessageIndex(index === openMessageIndex ? null : index);

  return (
    <>
      {arrayList.map((_, index) => (
        <>
          <div>array - {index}</div>
          <button onClick={() => onClickOpenMessage(index)}>Click me!</button>
          {openMessageIndex === index && (
            <div
              style={{
                width: "150px",
                height: "50px",
                background: "yellowgreen",
                textAlign: "center",
                lineHeight: "50px",
              }}
            >
              I am message - {index}
            </div>
          )}
        </>
      ))}
    </>
  );
}
```

두 번째 방법은 <mark style="color:purple;">**map 메서드 내부를 컴포넌트화 하여 각 배열 요소가 갖는 state를 독립적으로 만드는 것**</mark>이다. 이 방법은 map 내부가 복잡해졌을 시 코드를 더 깔끔하고 읽기 쉽게한다. 모듈화와 재사용에도 이점이 있다.

ArrayItem 컴포넌트를 작성하고, 이 내부에서 messageOpen state와 onClickOpenMessage 함수를 관리한다.

즉, map으로 뿌린 각각의 ArrayItem 컴포넌트는 독립된 messageOpen state와 onClickOpenMessage 함수를 갖게되는 것이다.

```jsx
import { useState } from "react";

export const ArrayItem = (props) => {
  const { index } = props;

  const [messageOpen, setMessageOpen] = useState(false);
  const onClickOpenMessage = () => setMessageOpen((prev) => !prev);

  return (
    <>
      <div>array - {index}</div>
      <button onClick={onClickOpenMessage}>Click me!</button>
      {messageOpen && (
        <div
          style={{
            width: "150px",
            height: "50px",
            background: "yellowgreen",
            textAlign: "center",
            lineHeight: "50px",
          }}
        >
          I am message - {index}
        </div>
      )}
    </>
  );
};

export default function App() {
  const [arrayList] = useState(Array(3).fill(null));

  return (
    <>
      {arrayList.map((_, index) => (
        <ArrayItem key={index} index={index} />
      ))}
    </>
  );
}
```

> Reference\
> [chat.openai.com](https://chat.openai.com/chat)\
> **2023.03.16**
