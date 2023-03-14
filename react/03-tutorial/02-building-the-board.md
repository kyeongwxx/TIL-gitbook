# 02-Building the board

App.js로 돌아가보자.

현재 보드는 하나의 정사각형이지만 9개가 필요하다. 정사각형을 복사하여 붙여 넣어 다음과 같이 두 개의 정사각형을 만들 수 있다.

```jsx
export default function Square() {
  return <button className="square">X</button><button className="square">X</button>;
}
```

