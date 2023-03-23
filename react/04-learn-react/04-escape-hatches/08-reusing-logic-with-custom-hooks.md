# 08-Reusing Logic with Custom Hooks

리액트에는 useState, useContext 및 useEffect와 같은 몇 가지 내장 Hook이 있다. 하지만 때로는 좀 더 특정한 목적을 위한 Hook이 있었으면 할텐데, 이때 필요에 따라 자신만의 Hook을 만들 수 있다.

## Cutom Hooks : Sharing logic between components

대부분의 앱이 그렇듯 네트워크에 크게 의존하는 앱을 개발하고 있다고 상상해보자. 앱 사용중 네트워크 연결이 실수로 끊어진 경우 유저에게 경고를 하려고 한다. 어떻게 할까?

컴포넌트에 두 가지가 필요할텐데,

1. 네트워크가 온라인인지 추적하는 state
2. 전역 온라인, 오프라인 이벤트를 subscribes 하고 해당 상태를 업데이트하는 Effect

이렇게 하면 컴포넌트가 네트워크 상태와 동기화된다. 시작해보자!

```jsx
import { useEffect, useState } from "react";

export default function StatusBar() {
  const [isOnline, setIsOnline] = useState(true);

  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }

    window.addEventListener("online", handleOnline);
    window.addEventListener("offline", handleOffline);

    return () => {
      window.removeEventListener("online", handleOnline);
      window.removeEventListener("offline", handleOffline);
    };
  }, []);

  return <h1>{isOnline ? "✅ online" : "❌ Disconnected"}</h1>;
}
```

네트워크를 on/off 해보고 이에 따라 StatusBar 컴포넌트가 어떻게 응답하는지 확인해보자.

이제 다른 컴포넌트에서 동일한 로직을 사용하고 싶다면? 네트워크가 off 일 땐 "Save" 대신 비활성화된 "Reconnecting"을 보여주는 버튼을 구현해보자.

먼저 isOnline state와 Effect를 복사하여 SavaButton 컴포넌트에 붙여넣자.

```jsx
import { useEffect, useState } from "react";

export default function SaveButton() {
  const [isOnline, setIsOnline] = useState(true);

  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }

    window.addEventListener("online", handleOnline);
    window.addEventListener("offline", handleOffline);

    return () => {
      window.removeEventListener("online", handleOnline);
      window.removeEventListener("offline", handleOffline);
    };
  }, []);

  function handleSaveClick() {
    console.log("✅ Progress saved");
  }

  return (
    <button disabled={!isOnline} onClick={handleSaveClick}>
      {isOnline ? "Save progress" : "Reconnecting..."}
    </button>
  );
}
```

네트워크를 off 하면 버튼이 바뀌는지 확인해보자.

이 두 컴포넌트는 잘 작동하지만 중복되는 로직이 아쉽다. 재사용하고 싶지 않은가?

### Extracting your own custom Hook from a component

useState 또는 useEffect와 유사하게 useOnlineStatus Hook이 내장되어 있다고 상상해보자. 그렇게 되면 이 두 컴포넌트의 중복 요소를 제거하고 단순화할 수 있다.

리액트에선 위와 같은 Hook이 내장되어 있진 않아도 직접 만들 수 있다. useOnlineStatus 함수를 선언하고 이전에 만든 컴포넌트의 코드를 이 함수로 이동시키자.

{% code title="useOnlineStatus.js" %}
```jsx
import { useEffect, useState } from "react";

export default function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);

  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener("online", handleOnline);
    window.addEventListener("offline", handleOffline);
    return () => {
      window.removeEventListener("online", handleOnline);
      window.removeEventListener("offline", handleOffline);
    };
  }, []);

  return { isOnline };
}
```
{% endcode %}

함수 끝에서 isOnline을 return한다. 이렇게 하면 컴포넌트가 custom hook의 해당 값을 읽을 수 있다.

{% code title="App.js" %}
```jsx
import useOnlineStatus from "./useOnlineStatus";

function StatusBar() {
  const { isOnline } = useOnlineStatus();

  return <h1>{isOnline ? "✅ Online" : "❌ Disconnected"}</h1>;
}

function SaveButton() {
  const { isOnline } = useOnlineStatus();

  function handleSaveClick() {
    console.log("✅ Progress saved");
  }

  return (
    <button disabled={!isOnline} onClick={handleSaveClick}>
      {isOnline ? "Save progress" : "Reconnecting..."}
    </button>
  );
}

export default function App() {
  return (
    <>
      <SaveButton />
      <StatusBar />
    </>
  );
}
```
{% endcode %}

이제 컴포넌트에 중복되는 로직은 많지 않다. 더 중요한 것은 내부 코드가 하려는 일의 방법(브라우저 이벤트 subscribe)이 아니라 하려는 일 자체(online status 사용)를 설명한다는 것이다.

custom hook으로 로직을 빼내면 일부 외부 시스템 또는 브라우저 API를 처리하는 방법에 대한 세부 정보를 숨길 수 있다. 이에 따라 컴포넌트의 코드는 구현이 아니라 의도를 더 잘 나타내게 된다.

### Hook names always start with `use`

리액트 앱은 컴포넌트로 이루어져 있다. 또한 컴포넌트는 내장된 또는 custom Hooks로 이루어져 있다. 다른 사람이 만든 custom Hooks를 자주 사용하겠지만 가끔은 직접 작성할 수도 있다.

이 때 다음과 같은 네이밍 컨벤션을 따라야한다.

1. 리액트 컴포넌트의 이름은 대문자로 시작해야 한다. 또한 컴포넌트는 JSX와 같이 리액트가 알고 있는 display 방법으로 return해야 한다.
2. Hook 이름은 use로 시작해야 한다. Hook는 임의의 값을 반환할 수 있다.

이 컨벤션은 컴포넌트에 어떤 state, Effects 그리고 다른 리액트 기능이 있는지 알 수 있게 한다.

### Custom Hooks let you share stateful logic, not state itself

앞선 예제에서 네트워크를 on/off 할 때 두 컴포넌트가 같이 업데이트 되었다. 그러나 단일 isOnline state가 두 컴포넌트 사이에 공유되는 것은 아니다. 그들은 독립된 isOnline state를 갖는다.

```jsx
function StatusBar() {
  const isOnline = useOnlineStatus();
  // ...
}

function SaveButton() {
  const isOnline = useOnlineStatus();
  // ...
}

```

위 코드는 복사본을 빼내기 전과 동일한 방식으로 작동한다.

```jsx
function StatusBar() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    // ...
  }, []);
  // ...
}

function SaveButton() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    // ...
  }, []);
  // ...
}
```

이들은 완전히 독립적인 두 개의 state와 Effects이다. 같은 외부 값(네트워크의 on/off)과 동기화했기 때문에 우연히 같은 값을 가지게 된 것이다.

Form 컴포넌트의 예제를 통해 다시 이해해보자.

```jsx
import { useState } from "react";

export default function Form() {
  const [firstName, setFirstName] = useState("Mary");
  const [lastName, setLastName] = useState("Poppins");

  function handleFirstNameChange(e) {
    setFirstName(e.target.value);
  }

  function handleLastNameChange(e) {
    setLastName(e.target.value);
  }

  return (
    <>
      <label>
        First name:
        <input value={firstName} onChange={handleFirstNameChange} />
      </label>
      <label>
        Last name:
        <input value={lastName} onChange={handleLastNameChange} />
      </label>
      <p>
        <b>
          Good morning, {firstName} {lastName}.
        </b>
      </p>
    </>
  );
}
```

각 form 필드에는 중복되는 로직이 몇 가지 보인다.

1. state(firstName, lastName)
2. change handler(handleFirstNameChange, handlelastNameChange)
3. value와 onChange 속성을 지정하는 input 요소

이때 useFormInput cumtom Hook을 만들어 반복 로직을 빼낼 수 있다.

{% code title="useFormInput.js" %}
```jsx
import { useState } from "react";

export default function useFormInput(initialValue) {
  const [value, setValue] = useState(initialValue);

  function handleChange(e) {
    setValue(e.target.value);
  }

  return { value, handleChange };
}
```
{% endcode %}

{% code title="App.js" %}
```jsx
import useFormInput from "./useFormInput";

export default function Form() {
  const { value: firstName, handleChange: firstNameChange } = useFormInput("Mary");
  const { value: lastName, handleChange: lastNameChange } = useFormInput("Poppins");

  return (
    <>
      <label>
        First name:
        <input value={firstName} onChange={firstNameChange} />
      </label>
      <label>
        Last name:
        <input value={lastName} onChange={lastNameChange} />
      </label>
      <p>
        <b>
          Good morning, {firstName} {lastName}.
        </b>
      </p>
    </>
  );
}
```
{% endcode %}

value라는 하나의 state만 선언하지만 From 컴포넌트는 useFormInput을 두 번 호출한다.

이것이 두 개의 개별 state 변수를 선언하는 것처럼 작동하는 이유이다.

<mark style="color:purple;">**custom Hook을 사용하면 state 로직을 공유할 수 있지만 state 자체는 공유할 수 없다. Hook에 대한 각 호출은 동일한 Hook에 대한 호출일지라도 완전히 독립적인 호출이다.**</mark>

여러 컴포넌트 간 state 자체를 공유해야 하는 경우 state 끌어올리기를 사용해야 한다.

## Passing reactive values between Hooks

custom Hook의 내부 코드는 컴포넌트를 리렌더링할 때마다 다시 실행된다. 이것이 컴포넌트와 마찬가지로 custom Hook이 pure해야하는 이유다. custom Hook의 코드를 컴포넌트 본문의 일부로 생각하자!

custom Hook은 컴포넌트와 함께 리렌더링되기 때문에 항상 최신의 props 및 state를 유지한다. 이것이 무슨 의미인지를 파악하려면 아래 chat room 예제를 공부해보자.

{% tabs %}
{% tab title="App.js" %}
```jsx
import { useState } from "react";
import ChatRoom from "./chatRoom";

export default function App() {
  const [roomId, setRoomId] = useState("general");

  return (
    <>
      <label>
        Choose the chat room:{" "}
        <select value={roomId} onChange={(e) => setRoomId(e.target.value)}>
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <hr />
      <ChatRoom roomId={roomId} />
    </>
  );
}
```
{% endtab %}

{% tab title="ChatRoom.js" %}
```jsx
import { useEffect, useState } from "react";
import createConnection from "./chat";
import showNotification from "./notification";

export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState("https://localhost:1234");

  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId,
    };
    const connection = createConnection(options);
    connection.on("message", (msg) => {
      showNotification('New message: ' + msg);
    });
    connection.connect();
    return () => connection.disconnect;
  }, [roomId, serverUrl]);

  return (
    <>
      <label>
        Server URL:
        <input
          value={serverUrl}
          onChange={(e) => setServerUrl(e.target.value)}
        />
      </label>
      <h1>Welcome to the {roomId} room!!!</h1>
    </>
  );
}
```
{% endtab %}

{% tab title="chat.js" %}
```jsx
export default function createConnection({ serverUrl, roomId }) {
  if (typeof serverUrl !== "string")
    throw Error(`Expected serverUrl to be a string. Received: ${serverUrl}`);

  if (typeof roomId !== "string")
    throw Error(`Expected serverUrl to be a string. Received: ${roomId}`);

  let intervalId;
  let messageCallback;

  return {
    connect() {
      console.log(`✅ Connecting to ${roomId} room at ${serverUrl} ...`);
      clearInterval(intervalId);
      intervalId = setInterval(() => {
        if (messageCallback) {
          if (Math.random() > 0.5) {
            messageCallback("hey");
          } else {
            messageCallback("lol");
          }
        }
      }, 3000);
    },

    disconnect() {
      clearInterval(intervalId);
      messageCallback = null;
      console.log(`❌ Disconnected from ${roomId} room at ${serverUrl}`);
    },

    on(event, callback) {
      if (messageCallback) throw Error("Cannot add the handler twice.");
      if (event !== "message") throw Error('Only "message" event is supported');
      messageCallback = callback;
    },
  };
}
```
{% endtab %}

{% tab title="notification.js" %}
```jsx
import Toastify from 'toastify-js';
import 'toastify-js/src/toastify.css';

export default function showNotification(message, theme = 'dark') {
  Toastify({
    text: message,
    duration: 2000,
    gravity: 'top',
    position: 'right',
    style: {
      background: theme === 'dark' ? 'black' : 'white',
      color: theme === 'dark' ? 'white' : 'black',
    },
  }).showToast();
}
```
{% endtab %}
{% endtabs %}

serverUrl이나 roomId를 변경하면 Effect가 변경 사항에 반응하여 다시 동기화된다. Effect의 종속성을 변경할 때마다 채팅이 다시 연결되는 것을 콘솔로 알 수 있다.

이제 Effect 코드를 custom Hook으로 만들어보자.

```jsx
import { useEffect } from "react";
import createConnection from "./chat";
import showNotification from "./notification";

export default function useChatRoom({ serverUrl, roomId }) {
  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId,
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on("message", (msg) => {
      showNotification("New message: " + msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl]);
}
```

이렇게 하면 ChatRoom 컴포넌트가 내부 작동 방식에 대한 걱정없이 custom Hook를 호출할 수 있다.

```jsx
import { useState } from "react";
import useChatRoom from "./useChatRoom";

export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState("https://localhost:1234");

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl,
  });

  return (
    <>
      <label>
        Server URL:
        <input
          value={serverUrl}
          onChange={(e) => setServerUrl(e.target.value)}
        />
      </label>
      <h1>Welcome to the {roomId} room!!!</h1>
    </>
  );
}
```

같은 일을 하지만 로직이 훨씬 더 간단해 보인다. 로직은 전과 같이 prop 및 state 변경에 응답한다.

ChatRoom 컴포넌트가 리렌더링될 때마다 최신 roomId 및 serverUrl을 custom Hook에 전달한다. 이것이 리렌더링 후 값이 다를 때마다 Effect가 채팅에 다시 연결되는 이유이다.

### Passing event handlers to custom Hooks

보다 많은 컴포넌트에서 useChatRoom을 사용하기 시작하면 컴포넌트가 스스로 동작을 커스터마이징 하도록 할 수 있다. 예를 들면, 현재는 메시지가 도착했을 때 수행할 작업에 대한 논리는 Hook 내부에 하드코딩되어 있다.

```jsx
export default function useChatRoom({ serverUrl, roomId }) {
  useEffect(() => {
    // ...
    connection.on("message", (msg) => {
      showNotification("New message: " + msg);
    });
    // ...
  }, [roomId, serverUrl]);
}
```

이 로직을 컴포넌트로 다시 옮긴다고 가정해보자.

```jsx
export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState("https://localhost:1234");

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl,
    onReceiveMessage(msg) {
      showNotification('New message: ' + msg);
    }
  });
  // ...
```

이후 다시 custom Hook을 손봐준다.

```jsx
export default function useChatRoom({ serverUrl, roomId }) {
  useEffect(() => {
    // ...
    connection.on("message", (msg) => {
      onReceiveMessage(msg);
    });
    // ...
  }, [roomId, serverUrl, onReceiveMessage]); // ✅ All dependencies declared
}
```

useChatRoom을 사용하기 위해 더 이상 어떻게 작동하는지 알 필요가 없다. 다른 컴포넌트에 추가하고, 다른 옵션을 전달할 수 있으며, 동일한 방식으로 작동한다. 이것이 custom Hooks의 힘이다.

## When to use custom Hooks

모든 중복 코드를 custom Hook으로 뺄 필요는 없다. 약간의 중복은 괜찮다. 예를 들어, 앞서 단일 useState 호출을 래핑하기 위해 useFormInput Hook을 만드는 것은 불필요하다.

하지만 Effect를 작성할 때마다 custom Hook으로 빼는 게 더 명확할 지는 항상 고민해봐야한다. Effects는 자주 필요하지 않다. 따라서 이를 작성하는 경우는 외부 시스템과 연동하기 위해 리액트 밖으로 나간다거나 리액트에 내장된 API 외의 것을 하려고 할 상황이다. 이때 custom Hook을 사용한다면 의도와 데이터 흐름을 명확하게 전달할 수 있다.

예를 들어 두 개의 드롭다운을 보여주는 ShippingForm 컴포넌트를 생각해보자. 하나는 도시 목록을 보여주고 다른 하나는 선택한 도시의 지역 목록을 보여준다.

```jsx
function ShippingForm({ country }) {
  const [cities, setCities] = useState(null);
  // This Effect fetches cities for a country
  useEffect(() => {
    let ignore = false;
    fetch(`/api/cities?country=${country}`)
      .then(response => response.json())
      .then(json => {
        if (!ignore) {
          setCities(json);
        }
      });
    return () => {
      ignore = true;
    };
  }, [country]);

  const [city, setCity] = useState(null);
  const [areas, setAreas] = useState(null);
  // This Effect fetches areas for the selected city
  useEffect(() => {
    if (city) {
      let ignore = false;
      fetch(`/api/areas?city=${city}`)
        .then(response => response.json())
        .then(json => {
          if (!ignore) {
            setAreas(json);
          }
        });
      return () => {
        ignore = true;
      };
    }
  }, [city]);

  // ...
```

코드에 중복이 있긴 하지만 이러한 Effect는 서로 분리하여 유지하는 것이 맞다. 서로 다른 두 가지를 동기화하므로 하나의 Effect로 합치면 안된다는 뜻이다. 대신 중복 로직을 useData custom Hook으로 빼내어 단순화할 수 있다.

```jsx
function useData(url) {
  const [data, setData] = useState(null);
  
  useEffect(() => {
    if (url) {
      let ignore = false;
      fetch(url)
        .then(response => response.json())
        .then(json => {
          if (!ignore) {
            setData(json);
          }
        });
      return () => {
        ignore = true;
      };
    }
  }, [url]);
  
  return data;
}
```

이제 ShippingForm 컴포넌트의 두 Effects를 useData custom Hook 호출로 바꿀 수 있다.

```jsx
function ShippingForm({ country }) {
  const cities = useData(`/api/cities?country=${country}`);
  const [city, setCity] = useState(null);
  const areas = useData(city ? `/api/areas?city=${city}` : null);
  // ...
```

custom Hook을 사용하면 데이터 흐름이 명시적으로 된다. url을 입력하고, data를 가져온다. custom Hook 내부에 Effect를 숨김으로써 컴포넌트에서 작업하는 사람이 불필요한 dependency를 추가하지 못하도록 방지할 수도 있다.
