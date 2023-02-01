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

### Class를 사용하는 예시

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

### Hook을 이용하는 예시

```jsx
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });
  
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

<mark style="color:purple;">**useEffect가 하는 일?**</mark> useEffect Hook을 사용함으로써 React component에 렌더링 이후 어떤 일을 수행해야하는지 전달할 수 있다. React는 "effect" 함수를 기억하고, DOM 업데이트 이후 호출한다. 위 예제의 경우 effect를 통해 document.title을 지정하지만, 이외에도 데이터를 가져오거나 다른 명령형 API를 호출하는 일도 할 수 있다.

<mark style="color:purple;">**useEffect를 컴포넌트 안에서 불러내는 이유?**</mark> useEffect를 컴포넌트 내부에 둠으로써 state 또는 prop에 접근할 수 있기 때문이다. 함수 스코프 내에 존재하기 때문에 이들을 사용함에 있어서 어떤 특별한 API가 필요하지 않다는 의미이다. Hooks는 React에 한정된 API를 고안하는 것보단 자바스크립트의 클로저, 즉 자바스크립트가 이미 가지고 있는 방법을 이용하여 문제를 해결한다.

<mark style="color:purple;">**useEffect는 렌더링 이후 매번 수행되는가?**</mark> Yes. 기본적으로 첫번째 렌더링과 이후 모든 업데이트에서 수행된다. effect 발생 지점을 마운팅과 업데이트 후 대신 "렌더링 후"라는 방식으로 생각하면 보다 쉽다. React는 effect가 수행되는 시점엔 이미 DOM이 업데이트 되었음을 보장한다.

### Detailed Explanation

effect에 대해 좀 더 알아보았다. 이제 아래의 코드들을 보다 쉽게 이해할 수 있다.

```jsx
function Example() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });
}
```

count state 변수를 선언한 뒤 React에 effect를 사용할 것이라고 전달하고 있다. useEffect Hook에 함수를 전달하고 있는데, 이 함수가 바로 effect이다. 이 effect 내부에서 document.title이라는 브라우저 API를 이용해 문서 타이틀을 지정한다. 같은 함수(컴포넌트) 스코프 안에 위치하기 때문에 effect 내부에서 최신 상태의 count를 읽을 수 있다. React는 컴포넌트를 렌더링할 때 effect를 기억하였다가 DOM을 업데이트한 이후 실행한다. 이는 첫 렌더링을 포함해서 이후 모든 렌더링에 적용된다.

숙련된 자바스크립트 개발자라면 useEffect에 전달되는 함수가 렌더링마다 매번 달라지는 것을 눈치챘을지도 모른다. 이는 의도된 것으로서 effect 내부 count 값이 제대로 업데이트 되는지에 대해 걱정할 필요를 없게 만든다. 리렌더링 마다 다른 effect를 계획하고, 이전 effect를 대체한다. 이 점이 effects가 보다 더 렌더링 결과의 한 부분처럼 행동하는 것처럼 보이게 한다. 즉, 각 effects는 특정한 렌더링에 속한다.

> 대부분의 effects는 동기적으로 실행될 필요가 없다. 흔하진 않지만 동기적 실행이 필요한 경우에는 useLayoutEffects라는 별도의 Hook을 사용한다.

## Clean-up을 이용하는 Effects

위에서 clean-up이 필요하지 않은 side effect는 어떻게 표현되는지 보았다. 하지만 clean-up이 필요한 effect도 있다. 예를 들어, <mark style="color:purple;">**외부 데이터에 구독(subscription)을 설정하는 경우**</mark>가 있다. 이런 경우엔 메모리 누수가 발생하지 않도록 clean-up하는 것이 매우 중요하다.

### Class를 사용하는 예시

React class에서는 흔히 componentDidMount에 구독을 설정한 뒤, componentWillUnmount에서 이를 clean-up한다. 친구의 온라인 상태를 구독할 수 있는 ChatAPI 모듈의 예를 보자.

```jsx
class FriendStatus extends React.Component {
  constructor(props) {
    super(props);
    this.state = { isOnline: null };
    this.handleStatusChange = this.handleStatusChange.bind(this);
  }
  
  componentDidMount() {
    ChatAPI.subscribeToFriendStatus(this.props.friend.id, this.handleStatusChange);
  }
  
  componentWillUnmount() {
    ChatAPI.unsubscribeFromFriendStatus(this.props.friend.id, this.handleStatusChange);
  }
  
  handleStatusChange(status) {
    this.setState({ isOnline: status.isOnline });
  }
  
  render() {
    if(this.state.isOnline === null) return 'Loading...';
    
    return this.state.isOnline ? 'Online' : 'Offline';
  }
}
```

componentDidMount와 componentWillUnmount에서 같은 로직을 사용하고 있음에도 불구하고, Lifecycle 메서드는 이를 분리하게 만든다.

### Hook을 이용하는 예시

clean-up의 실행을 위해 별개의 effect가 필요하다고 생각할 수도 있다. 하지만 구독의 추가와 제거를 위한 코드는 결합도가 높기 때문에 useEffect는 이를 함께 다루도록 고안되었다. effect가 함수를 return 하면 React는 return한 함수를 clean-up이 필요할 때에 실행시킨다.

```jsx
import React, { useState, useEffect } from 'react';

function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);
  
  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }
    
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    
    return function cleanup() {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });
  
  if (isOnline === null) return 'Loading...';
  
  return isOnline ? 'Online' : 'Offline';
}
```

<mark style="color:purple;">**effect에서 함수를 return하는 이유?**</mark> clean-up을 위한 선택적 메커니즘. 모든 effect는 clean-up을 위한 함수를 return 할 수 있다. 이 점이 구독의 추가와 제거 로직을 서로 가까이에 둘 수 있게 한다. 구독의 추가와 제거가 같은 effect의 부분을 차지한다는 것.

<mark style="color:purple;">**React가 effect를 clean-up하는 정확한 시점?**</mark> React는 컴포넌트가 unmount되는 때에 clean-up을 실행한다. 하지만 effect는 한 번이 아니라 렌더링이 될 때마다 실행된다. React가 다음 차례의 effect를 실행하기 전, 이전 렌더링에서 파생된 effect 또한 정리하는 이유가 바로 이 때문이다.

## 요약

useEffect를 이용하여 컴포넌트 렌더링 이후 다양한 side effects를 표현할 수 있음을 배웠다. effect에 clean-up이 필요한 경우, 함수를 return한다.

```jsx
useEffect(() => {
  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }

  ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
  
  return () => {
    ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
  };
});
```

clean-up이 필요없는 경우, 어떠한 것도 return하지 않는다.

```jsx
useEffect(() => {
  document.title = `You clicked ${count} times`;
});
```

이렇듯 effect Hook은 두 가지 경우를 하나의 API로 통합한다.

## Tips for Using Effects

### Tip: 관심사를 구분하려면 Multiple Effects를 사용한다.

Hook이 탄생한 동기 중 하나가 class의 Lifecycle 메서드들은 관련없는 로직들은 모아놓는 반면, 관련있는 로직들은 개별 메서드에 나눠버린다는 것이다. counter와 friend status 로직을 결합한 아래 컴포넌트를 보자.

```jsx
class FriendStatusWithCounter extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0, isOnline: null };
    this.handleStatusChange = this.handleStatusChange.bind(this);
  }
  
  componentDidMount() {
    document.title = `You clicked ${this.state.count} times`;
    ChatAPI.subscribeToFriendStatus(this.props.friend.id, this.handleStatusChange);
  }
  
  componentDidUpdate() {
    document.title = `You clicked ${this.state.count} times`;
  }
  
  componentWillUnmount() {
    ChatAPI.unsubscribeFromFriendStatus(this.props.friend.id, this.handleStatusChange);
  }
  
  handleStatusChange(status) {
    this.setState({ isOnline: status.isOnline });
  }
  // ...
```

document.title을 설정하는 로직이 componentDidMount와 componentDidUpdate에 나누어져 있다. 구독 로직 또한 componentDidMount와 componentWillUnmount에 나누어져 있다. componentDidMount는 두 가지 작업을 위한 코드를 모두 가지고 있다.

Effect를 이용하여 서로 관련없는 로직들을 갈라놓을 수 있다.

```jsx
function FriendStatusWithCounter(props) {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });
  
  const [isOnline, setIsOnline] = useState(null);
  
  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }
    
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });
  // ...
}
```

Hook을 이용하면 Lifecycle 메서드에 따라서가 아니라 <mark style="color:purple;">**코드가 무엇을 하는지에 따라**</mark> 나눌 수 있다. React는 컴포넌트에 사용된 모든 effect를 지정된 순서에 맞춰 적용한다.

### Explanation: effect가 업데이트 시마다 실행되는 이유

친구가 온라인인지 아닌지 표시하는 \<FriendStatus /> 컴포넌트 예시를 생각해보자. class는 this.props로부터 friend.id를 읽어내고 컴포넌트가 마운트된 이후 친구의 상태를 구독, 언마운트 시 구독을 해지한다.

```jsx
componentDidMount() {
  ChatAPI.subscribeToFriendStatus(this.props.friend.id, this.handleStatusChange);
}
  
componentWillUnmount() {
  ChatAPI.unsubscribeFromFriendStatus(this.props.friend.id, this.handleStatusChange);
}
```

<mark style="color:purple;">**그런데 컴포넌트가 화면에 표시되어있는 동안 friend prop이 변한다면?**</mark> 컴포넌트는 다른 친구의 온라인 상태를 계속 표시할 것이다. 즉, 버그이다. 또한 언마운트 시의 구독 해제는 다른 친구의 ID를 사용하여 메모리 누수나 충돌이 발생할 수도 있다.

클래스 컴포넌트에서는 이러한 상황을 다루기 위해 componentDidUpdate를 사용한다.

```jsx
componentDidMount() {
  ChatAPI.subscribeToFriendStatus(this.props.friend.id, this.handleStatusChange);
}

componentDidUpdate(prevProps) {
  // 이전 friend.id에서 구독을 해지합니다.
  ChatAPI.unsubscribeFromFriendStatus(prevProps.friend.id, this.handleStatusChange);
  // 다음 friend.id를 구독합니다.
  ChatAPI.subscribeToFriendStatus(this.props.friend.id, this.handleStatusChange);
}
  
componentWillUnmount() {
  ChatAPI.unsubscribeFromFriendStatus(this.props.friend.id, this.handleStatusChange);
}
```

Hook을 사용하는 컴포넌트의 경우 더 간단하지만 버그에 시달리지 않는다.

```jsx
function FriendStatus(props) {
  // ...
  useEffect(() => {
    // ...
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });
```

useEffect는 기본적으로 업데이트를 다루기 때문에 업데이트를 위한 부가적인 코드가 필요없다. 필요 시 다음 effect를 적용하기 전, 이전 effect를 clean-up 할 수도 있다.

다음은 이를 설명하기 위해 위 컴포넌트가 호출하는 일련의 구독과 구독 해제를 보여준다.

```jsx
// { friend: { id: 100 } } state을 사용하여 마운트합니다.
ChatAPI.subscribeToFriendStatus(100, handleStatusChange);     // 첫번째 effect가 작동합니다.

// { friend: { id: 200 } } state로 업데이트합니다.
ChatAPI.unsubscribeFromFriendStatus(100, handleStatusChange); // 이전의 effect를 정리(clean-up)합니다.
ChatAPI.subscribeToFriendStatus(200, handleStatusChange);     // 다음 effect가 작동합니다.

// { friend: { id: 300 } } state로 업데이트합니다.
ChatAPI.unsubscribeFromFriendStatus(200, handleStatusChange); // 이전의 effect를 정리(clean-up)합니다.
ChatAPI.subscribeToFriendStatus(300, handleStatusChange);     // 다음 effect가 작동합니다.

// 마운트를 해제합니다.
ChatAPI.unsubscribeFromFriendStatus(300, handleStatusChange); // 마지막 effect를 정리(clean-up)합니다.
```

이러한 방식의 동작은 일관성을 유지해주며, 클래스 컴포넌트에서는 업데이트 로직을 빼먹을 시 발생할 수 있는 버그를 사전 예방한다.

### Tip: Effect를 스킵하여 성능 최적화하기

렌더링마다 effect를 적용하거나 clean-up하는 것이 때로는 성능 저하로 이어질 수 있다.

클래스 컴포넌트의 경우 componentDidUpdate에서 이전 상태값과의 비교를 통해 이를 해결할 수 있다.

```jsx
componentDidUpdate(prevProps, prevState) {
  if (prevState.count !== this.state.count) {
    document.title = `You clicked ${this.state.count} times`;
  }
}
```

이러한 요구 조건은 Hook API에 이미 내재되어 있다. 특정 값들이 리렌더링 시에 변경되지 않는다면 effect를 건너뛰도록 할 수 있다. useEffect의 선택적 인수인 두 번째 인수로 배열을 넘기면 된다.

```jsx
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]); // count가 바뀔 때만 effect를 재실행한다.
```

count가 5이고, 컴포넌트가 리렌더링된 이후에도 여전히 count가 5라면 React는 이전 렌더링 시의 값 5를 다음 렌더링 시의 5와 비교한다. 배열 내의 모든 값이 같기 때문에 React는 effect를 건너 뛴다. 이런 식으로 최적화가 가능하다.

count가 6으로 업데이트된 뒤 렌더링하면 React는 이전 렌더링 시의 값 5를 그 다음 렌더링 시의 6과 비교한다. 이때 React는 effect를 재실행한다.

배열 내 여러 개의 값이 있다면 그 중 단 하나만 달라져도 React는 effect를 재실행한다.

이는 clean-up을 사용하는 effect의 경우에도 동일하게 작용한다.

```jsx
useEffect(() => {
  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }

  ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
  
  return () => {
    ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
  };
}, [props.friend.id]); // props.friend.id가 바뀔 때만 재구독한다.
```

> effect를 실행하고 이를 clean-up하는 과정을 마운트와 언마운트 시 딱 한 번씩만 실행하고 싶다면 빈 배열을 두 번째 인수로 넘기면 된다.

> Reference
>
> [https://reactjs.org/](https://reactjs.org/)
>
> **2023.02.01**
