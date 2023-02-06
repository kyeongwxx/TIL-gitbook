# 05-Building Your Own Hooks

자신만의 Hook을 만들면 컴포넌트 로직을 함수로 뽑아내어 재사용할 수 있다.

Effect Hook에 대해 배울 때, 채팅 앱에서 친구가 온라인 상태인지 아닌지에 대한 메세지를 표시하는 컴포넌트를 작성했다.

```jsx
import React, { useState, useEffect } from 'react';

function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);
  
  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }
    
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    
    return () => {
      ChatAPI.unsubscribeToFriendStatus(props.friend.id, handleStatusChange);
    };
  });
  
  if (isOnline === null) return 'Loading...';
  
  return isOnline ? 'Online' 'Offline';
}
```

이제 연락처 목록이 있고, 그 중에서 온라인 상태인 사용자들의 이름을 초록색으로 표시하는 상황을 가정해보자. 위의 코드와 비슷한 로직을 복사하여 \<FriendListItem /> 컴포넌트 안에 붙여 넣을 수도 있지만, 이상적인 방법이라고 할 순 없다.

```jsx
import React, { useState, useEffect } from 'react';

function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);
  
  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }
    
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    
    return () => {
      ChatAPI.unsubscribeToFriendStatus(props.friend.id, handleStatusChange);
    };
  });
  
  return (
    <li style={{ color: isOnline ? 'green' : 'black' }}>
      {props.friend.name}
    </li>
  );
}
```

대신, 이 로직을 \<FriendStatus /> 컴포넌트와 \<FriendListItem /> 컴포넌트에서 공유하도록 할 것이다.

React에서는 상태 관련 로직을 컴포넌트에서 공유하는 두 가지 방법이 있는데, 바로 render props와 고차 컴포넌트이다. Hook을 사용하여 트리에 컴포넌트를 추가하지 않고 위 문제를 해결하는 방법을 찾아보자.

## Custom Hook 추출하기

두 개의 자바스크립트 함수에서 같은 로직을 공유하고자 할 땐, 또 다른 함수로 분리한다. 컴포넌트와 Hook 또한 함수이기 때문에 같은 방법을 사용할 수 있다.

<mark style="color:purple;">**Custom Hook은 이름이 use로 시작하는 자바스크립트 함수이며, 다른 Hook을 호출할 수 있다.**</mark> 예를 들면, 아래 useFriendStatus가 Custom Hook이다.

```jsx
import { useState, useEffect } from 'react';

function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subscribeToFriendStatus(friendID, handleStatusChange);
    
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(friendID, handleStatusChange);
    };
  });

  return isOnline;
}
```

위 컴포넌트로부터 복사해온 코드이기 때문에 딱히 새로운 것은 없다. 다른 컴포넌트와 동일하게 Hook들은 Custom Hook의 최상위에서 비조건적으로 실행되어야 한다.

React 컴포넌트와 다른 점은 Custom Hook은 특정한 signature가 필요 없다. 무엇을 인수로 받아야 하며, 무엇을 반환해야 하는지를 사용자가 정할 수 있다. 다시 말해 보통의 함수와 동일하다라는 것. 다만 이름은 반드시 use로 시작해야 한다.

useFriendStatus Hook의 목표는 친구의 상태를 구독하기 위함이다. 이를 위해 friendID를 인수로 받고, 온라인 상태를 반환한다.

```jsx
function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  // ...

  return isOnline;
}
```

이제 Custom Hook 사용법을 살펴보자.

## Custom Hook 사용하기

처음 목표는 친구의 온라인 상태를 알아야 하는 \<FriendStatus /> 컴포넌트와 \<FriendListItem /> 컴포넌트에 중복된 로직을 제거하는 것이었다.

이 중복 로직을 useFriendStatus hook으로 뽑아내었으니 바로 사용할 수 있다.

```jsx
function FriendStatus(props) {
  const isOnline = useFriendStatus(props.friend.id);
  
  if (isOnline === null) return 'Loading...';
  
  return isOnline ? 'Online' : 'Offline';
}
```

```jsx
function FriendListItem(props) {
  const isOnlice = useFriendStatus(props.friend.id);
  
  return (
    <li style={{ color: isOnline ? 'green' : 'black' }}>
      {props.friend.name}
    </li>
  );
}
```

<mark style="color:purple;">**본래의 예시와 동일한 코드인가?**</mark> Yes. 정확히 같은 방식으로 동작한다. 바뀐 것은 오로지 공통의 코드를 추출하여 새로운 함수로 만든 것 뿐이다. Custom Hook은 React의 특별한 기능이 아니라 Hook의 디자인 컨벤션이다.

<mark style="color:purple;">**Custom Hook의 이름은 "use"로 시작되어야 하는가?**</mark> Yes. 그렇지 않을 시 특정 함수가 Hook을 호출하는지를 알 수 없기 때문에 Hook 규칙의 위반 여부를 자동으로 체크할 수 없다.

<mark style="color:purple;">**같은 Hook을 사용하는 두 개의 컴포넌트는 state을 공유하는가?**</mark> No. Custom Hook은 상태 관련 로직(구독을 설정하고 현재 변수값을 기억하는 것)을 재사용하는 메커니즘이지만, Custom Hook을 사용할 때마다 그 안의 state와 effect는 완전히 독립적이다.

<mark style="color:purple;">**Custom Hook은 어떻게 독립된 state을 얻는가?**</mark> Custom Hook인 useFriendStatus를 직접적으로 호출하기 때문에 React의 관점에서 useState와 useEffect를 호출한 것과 다름이 없기 때문이다. 이전에 배웠듯 하나의 컴포넌트 안에서 useState와 useEffect를 여러 번 부를 수 있고 이들은 모두 완전히 독립적이다.

## Tip: Hook에서 Hook으로 정보 전달하기

Hook은 함수이기 때문에 Hook 사이에서도 정보를 전달할 수 있다.

현재 선택된 친구가 온라인 상태인지를 표시하는 컴포넌트를 보자.

```jsx
const friendList = [
  { id: 1, name: 'Phoebe' },
  { id: 2, name: 'Rachel' },
  { id: 3, name: 'Ross' }
];

function ChatRecipientPicker() {
  const [recipientID, setRecipientID] = useState(1);
  const isRecipientOnline = useFriendStatus(recipientID);
  
  return (
    <>
      <Circle color={isRecipientOnline ? 'green' : 'red'} />
      <select
        value={recipientID}
        onChange{(e) => setRecipientID((Number(e.target.value))}
      >
        {friendList.map((friend) => (
          <option key={friend.id} value={friend.id}>
            {friend.name}
          </option>
        ))}
      </select>
    </>
  );
}
```

현재 선택된 친구의 ID를 recipientID state 변수에 저장하고, 사용자가 \<select>에 있는 다른 친구를 선택하면 이를 업데이트한다.

useState Hook 호출은 recipientID state 변수의 최신값을 돌려주기 때문에 이를 useFriendStatus Hook에 인수로 보낼 수 있다. 즉, useState Hook에서 Custom Hook으로 정보를 전달할 수 있다는 의미.

```jsx
const [recipientID, setRecipientID] = useState(1);
const isRecipientOnline = useFriendStatus(recipientID);
```

이를 통해 지금 선택되어 있는 친구의 온라인 상태 여부를 알 수 있다. 다른 친구를 선택하고 recipientID state 변수를 업데이트하면 useFriendStatus Hook은 이미 선택되어있는 친구의 구독을 해지하고 새롭게 선택된 친구의 상태를 구독할 것이다.

## useYourImagination()

Custom Hook은 이전 React 컴포넌트에서는 불가능했던 로직 공유의 유연성을 제공한다. 폼 다루기, 애니메이션, 선언형 구독, 타이머 등 다양한 쓰임새에 적용할 수 있다. 또한 React 내장 기능만큼이나 사용하기 쉬운 Hook을 만들 수도 있다.

너무 이른 단계에서 로직을 뽑아내려고 하지는 않는 게 좋다. 함수 컴포넌트가 할 수 있는 일이 더 다양해졌기 때문에 코드도 길어졌을 것이다. 이는 지극히 평범한 일이며, 지금 바로 Hook으로 분리해야만 한다고 느낄 필요는 없다. 먼저 Custom Hook이 복잡한 로직을 간단한 인터페이스 뒤에 숨길 수 있도록 하거나, 복잡하게 뒤엉킨 컴포넌트를 풀어내도록 돕는 경우를 찾는 것부터 시작하자.

예를 들면, 내부에 많은 state를 지니고 있지만 이것들이 적절하게 관리되고 있지 않은 컴포넌트를 생각해보자. 이 state들을 reducer로 관리하는 useReducer Hook을 작성한다면 어떨까? (이미 내장된 Hook API 이긴 하지만)

```jsx
function useReducer(reducer, initialState) {
  const [state, setState] = useState(initialState);
  
  function dispatch(action) {
    const nextState = reducer(state, action);
    setState(nextState);
  }
  
  return [state, dispatch];
}
```

컴포넌트 내부에서 이 Hook을 사용하여 state 관리를 시행할 수 있다.

```jsx
function Todos() {
  const [todos, dispatch] = useReducer(todoReducer, []);
  
  function handleAddClick(text) {
    dispatch({ type: 'add', text });
  }
  
  // ...
}
```

> Reference
>
> [https://reactjs.org/](https://reactjs.org/)
>
> **2023.02.06**
