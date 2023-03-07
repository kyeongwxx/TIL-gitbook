# useEffect hook의 첫 렌더링 시 함수 실행을 막는 방법

dependency에 넣어준 state의 값이 변함에 따라 내부의 함수를 실행시키는 useEffect hook을 사용하고 싶었지만, 첫 렌더링 시에 일어나는 함수의 실행은 막고 싶었다.

이유는 레이아웃 단에서 실행되는 useEffect의 내부 함수와 특정 페이지에서 실행되는 useEffect의 내부 함수가 정확히 같은 동작을 하는 함수인데, 이 특정 페이지로 이동할 경우 같은 두 함수가 모두 실행되어 발생하는 문제가 있었기 때문이다.

따라서 useRef hook을 활용한 custom hook을 작성하여 이 특정 페이지에서 실행되는 useEffect의 첫 렌더링 시 함수 실행을 막아 보았다. 즉, 이 <mark style="color:purple;">**custom hook은 클래스 컴포넌트의 componentDidUpdate 메서드와 동일하게 동작**</mark>한다.

{% code title="useDidMountEffect.js" %}
```jsx
import { useEffect, useRef } from "react";

const useDidMountEffect = (func, deps) => {
  const didMount = useRef(false);

  useEffect(() => {
    if (didMount.current) func();
    else didMount.current = true;
  }, deps);
};

export default useDidMountEffect;
```
{% endcode %}

{% code title="page.js" %}
```jsx
import { useState, useEffect } from 'react';
import useDidMountEffect from '../path/to/useDidMountEffect';

const MyComponent = (props) => {    
    const [state, setState] = useState(null);    

    useDidMountEffect(() => {
        // react please run me if 'key' changes, but not on initial render
    }, [state]);

    return (
        <div>
             ...
        </div>
    );
}
// ...
```
{% endcode %}

### useRef hook에 대해

리액트 컴포넌트는 기본적으로 state가 변할 때 마다 리렌더링된다.

함수 컴포넌트에서 리렌더링이 발생하면 컴포넌트 내부 변수들이 기존에 저장하고 있는 값들이 모두 초기화되고, 내부 함수들은 다시 실행된다.

하지만 리렌더링 시에도 기존에 참조하고 있던 컴포넌트 함수 내부의 값이 그대로 보존되어야 하는 경우도 있다.

이때 사용하는 것이 useRef hook이다.

```jsx
const refContainer = useRef(initialValue)
```

<mark style="color:purple;">**useRef 함수는 current 속성을 가지고 있는 객체를 반환하는데, 이때 전달된 인자(initialValue)를 current 속성에 할당한다. 이 current 속성은 값을 변경해도 state를 변경할 때처럼 컴포넌트가 리렌더링되지 않는다.**</mark>

반대로 컴포넌트가 리렌더링될 때도 current 속성의 값이 사라지지 않는다.

이러한 특성으로 인해 위 custom hook에서 초기값 false로 선언한 didMount 변수는 렌더링 후 useEffect 내부 함수의 첫 실행을 막아주고, 이후는 true로 값을 재할당한 후 dependency에 따라 실행된다.

> Reference
>
> [https://stackoverflow.com/questions/53253940/make-react-useeffect-hook-not-run-on-initial-render/63776262#63776262](https://stackoverflow.com/questions/53253940/make-react-useeffect-hook-not-run-on-initial-render/63776262#63776262)
>
> [https://www.daleseo.com/react-hooks-use-ref/](https://www.daleseo.com/react-hooks-use-ref/)
>
> **2023.03.07**
