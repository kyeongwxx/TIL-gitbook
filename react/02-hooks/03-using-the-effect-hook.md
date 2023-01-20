# 03-Using the Effect Hook

Effect Hook을 사용하면 함수 컴포넌트에서 side effect를 수행할 수 있다.

```jsx
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    // 브라우저 API를 이용하여 문서 타이틀을 업데이트
    document.title = `You clicked ${count} times`;
  });
  
  return (
    <div>
      <p>You Clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

위는 문서의 타이틀을 클릭 횟수가 포함된 문장으로 보여주는 예시이다.

데이터 가져오기, 구독 설정하기, 수동으로 React 컴포넌트의 DOM 수정하는 것 등 이 모든 것들이 side effects이다.

React 컴포넌트에는 일반적으로 두 종류의 side effects가 있다. 정리(clean-up)가 필요한 것과 그렇지 않은 것. 이 둘을 어떻게 구분해야 할지 알아보자.

## Clean-up을 사용하지 않는 Effects

종종 <mark style="color:purple;">**React가 DOM을 업데이트한 뒤 추가로 코드를 실행해야 하는 상황**</mark>이 있다. 네트워크 리퀘스트, DOM 수동 조작, 로깅 등은 clean-up이 필요 없는 상황들이다. 이러한 상황들은 실행 이후 신경 쓸 것이 없기 때문이다.

## Class를 사용하는 예시

React의 class 컴포넌트에서 render 메서드 그 자체는 side effect를 발생시키지 않는다. 이러한 effect를 수행하는 것은 React가 DOM을 업데이트하고 난 이후.

class 컴포넌트에서 side effect를 componentDidMount와 componentDidUpdate에 두는 것이 바로 이 때문이다. React가 DOM을 바꾸고 난 뒤 문서 타이틀을 업데이트하는 클래스 컴포넌트를 살펴보자.

```javascript
class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }
  
  componentDidMount() {
    document.title = `You clicked ${this.state.count} times`;
  }
  
  componentDidUpdate() {
    document.title = `You clicked ${this.state.count} times`
  }
  
  render() {
    return (
      <div>
        <p>You clicked {this.state.count} times</p>
        <button onClick={() => this.setState({ count: this.state.count + 1})}>
          Click me
        </button>
      </div>
    );
  }
}
```

위 코드에서 <mark style="color:purple;">**두 개의 생명주기 메서드 내에 같은 코드가 중복되는 것이 집중**</mark>하자.

이렇게 작성하는 이유는 컴포넌트가 이제 막 마운트된 단계인지, 아니면 업데이트되는 것인지에 상관없이 같은 side effect를 수행해야 하기 때문이다. 렌더링 이후 항상 같은 코드가 수행되기를 바라는 것.

## Hook을 이용하는 예시
