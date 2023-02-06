# 06-Hooks API Reference

## Basic Hooks

### useState

```jsx
const [state, setState] = useState(initialState);
```

상태값과 상태값을 업데이트하는 함수를 반환한다.

최초 렌더링 시 반환되는 state는 전달된 인자 initialState의 값과 같다.

setState 함수는 state를 업데이트할 때 사용한다. 새 state 값을 받아 컴포넌트 리렌더링을 큐에 등록한다.

```jsx
setState(newState);
```

다음 리렌더링 시에 useState를 통해 반환받은 첫 번째 값은 항상 업데이트가 적용된 이후 갱신된 최신 state가 된다.

> React는 setState 함수의 identity가 안정적이고 리렌더링 시 바뀌지 않음을 보장한다. 이것이 useEffect나 useCallback의 dependency에서 setState 함수가 생략되는 이유이다.

<mark style="color:green;">**Functional updates**</mark>

만약 새로운 state가 이전 state를 사용하여 계산된다면, 이전 state를 받아 새로운 state를 반환하는 함수를 setState로 전달할 수 있다.

```jsx
function Counter({ initialCount }) {
  const [count, setCount] = useState(initialCount);
  
  return (
    <>
      Count: {count}
      <button onClick={() => setCount(initialCount)}>Reset</button>
      <button onClick={() => setCount(prevCount => prevCount - 1)}>-</button>
      <button onClick={() => setCount(prevCount => prevCount + 1)}>+</button>
    </>
  );
}
```

"+"와 "-" 버튼은 함수 형식을 사용하고 있다. 업데이트된 값이 업데이트 이전 값을 바탕으로 계산되기 때문이다. 반면, "Reset" 버튼은 카운트를 항상 0으로 설정하기 때문에 일반적인 형식을 사용한다.

업데이트 함수가 현재 state와 동일한 값을 반환한다면 리렌더링은 일어나지 않는다.

> 클래스 컴포넌트의 setState 메서드와는 다르게, useState는 update 객체를 자동으로 합치지는 않는다. 함수 업데이터 폼을 객체 전개 연산자와 결합함으로써 이 동작을 복제할 수 있다.

```jsx
const [state, setState] = useState({});

setState(prevState => {
  return { ...prevState, ...updatedValues };
});
```

<mark style="color:green;">**Lazy initial state**</mark>

initialState 인자는 최초 렌더링 시에 사용하는 state이다. 그 이후의 렌더링 시에는 이 값은 무시된다. 초기 state가 고비용 계산의 결과라면 최초 렌더링 시에만 실행될 함수를 대신 제공할 수 있다.

```jsx
const [state, setState] = useState(() => {
  const initialState = someExpensiveComputation(props);
  return initialState;
});
```

<mark style="color:green;">**상태 업데이트 중단**</mark>

State Hook을 현재 state와 동일한 값으로 업데이트하는 경우 React는 자식을 렌더링 한다거나 하는 처리를 회피하고 그 처리를 종료한다.

<mark style="color:green;">**상태 업데이트 일괄 처리**</mark>

React는 성능 향상을 위해 여러 state 업데이트를 한 번의 렌더링으로 그룹화한다. 일반적으로 이 방법은 성능을 향상시키고 앱의 동작에 영향을 끼치지 않는다.

드물게 DOM 업데이트를 강제로 동기적으로 적용해야 하는 경우 이를 flushSync로 래핑할 수 있다. 다만 성능 저하를 야기하므로 필요한 경우에만 수행해야 한다.

### useEffect

```jsx
useEffect(didUpdate);
```

명령형 함수 또는 effect를 발생시키는 함수를 인자로 받는다.

Mutations, subscriptions, timers, logging 또는 다른 side effects는 함수 컴포넌트의 본문 내에서 허용되지 않는다. 이는 혼란스러운 버그와 UI의 불일치를 야기하기 때문이다.

대신 useEffect를 사용한다. useEffect에 전달된 함수는 화면이 렌더링된 이후에 수행된다.

useEffect 사용 시 기본적으로 동작은 모든 렌더링이 완료된 후에 수행되지만, 특정 값이 변경되었을 때만 실행되ㅔ 할 수도 있다.

<mark style="color:green;">**Cleaning up an effect**</mark>

effects는 종종 subscription 또는 timer ID처럼 컴포넌트가 언마운트 될 시 정리되어야 할 리소스를 만든다. 이를 수행하기 위해 useEffect로 전달된 함수는 clean-up 함수를 return 할 수 있다. 예를 들어 subscription을 생성하는 경우 아래와 같다.

```jsx
useEffect(() => {
  const subscription = props.source.subscribe();
  
  return () => {
    // Clean up the subscruption
    subscription.unsubscribe();
  };
});
```

clean-up 함수는 메모리 누수 방지를 위해 UI에서 컴포넌트를 제거하기 전에 수행된다. 더불어, 컴포넌트가 여러 번 렌더링 된다면 <mark style="color:purple;">**next effect가 수행되기 전에 previous effect는 정리**</mark>된다. 위의 예제에서 매 렌더링마다 새로운 구독이 생성된다고 볼 수 있다.

<mark style="color:green;">**Timing of effects**</mark>

componentDidMount와 componentDidUpdate와는 달리, useEffect로 전달된 함수는 지연 이벤트 동안 layout과 paint <mark style="color:purple;">**후**</mark>에 실행된다. 이는 subscription 설정이나 이벤트 핸들러를 설정하는 것과 같은 다수의 일반적인 side effects에 적합하다. 왜냐하면 대부분의 작업이 브라우저에서 화면을 업데이트하는 것을 차단해선 안 되기 때문이다.

그렇지만, 모든 effect가 지연될 수는 없다. 예를 들어 사용자에게 노출되는 DOM 변경은 내용의 불일치를 경험하지 않도록 다음 화면을 다 그리기 이전에 동기화 되어야 한다. 이러한 종류의 effect를 위해 React는 useLayoutEffect라는 추가적인 Hook을 제공한다. 이는 useEffect와 동일한 기능을 하지만 실행 시점만 차이가 있다.

또한, React 18 부터는 클릭과 같은 개별 사용자 입력의 결과이거나 flushSync로 래핑된 업데이트의 결과일 땐 useEffect로 전달된 함수는 layout과 paint 전에 동기적으로 실행된다.

> 이는 useEffect에 전달된 함수가 호출되는 시점에만 영향을 미친다. 함수내에서 예약된 업데이트는 여전히 지연된다. 이는 함수를 실행하고 즉시 처리하는 useLayoutEffect와는 다르다.

브라우저가 그려진 후까지 useEffect가 지연되는 경우에도 새로운 렌더링 전에 실행이 보장된다. React는 새 업데이트를 시작하기 전에 항상 이전 렌더링의 효과를 플러시한다.

<mark style="color:green;">**Conditionally firing an effect**</mark>

effect의 기본 동작은 모든 렌더링을 완료한 후 effect를 발생시키는 것이다. 이와 같은 방법으로 dependency 중 하나가 변경된다면 effect는 항상 재생성된다.

하지만 이전 섹션의 subscription 예시와 같은 일부 경우에는 과도한 작업일 수 있다. props.source가 변경될 때만 필요한 것이라면 매 업데이트마다 새로운 subscription을 생성할 필요는 없다.

이를 수행하기 위해 useEffect에 두 번째 인자를 사용한다. 이 인자는 effect가 의존하는, effect가 종속된 값의 배열이다.

```jsx
useEffect(() => {
  const subscription = props.source.subscribe();
  
  return () => {
    subscription.unsubscribe();
  };
}, [props.source])
```

> effect를 마운트 또는 언마운트 시 한 번만 실행하고 싶다면 dependency로 빈 배열을 전달한다. 이를 통해 React에게 props나 state에서 가져온 어떤 값도 의존하지 않으므로 다시 실행할 필요가 없다는 것을 알려준다.&#x20;

effect 함수 안에서 참조되는 모든 값은 dependency에 드러나야 한다.

### useContext

```jsx
const value = useContext(MyContext);
```

context 객체(React.createContext에서 반환된 값)를 받아 해당 context의 현재 값을 반환한다. context의 현재 값은 트리 내에서 이 Hook을 호출하는 컴포넌트 가장 가까이에 있는 \<MyContext.Provider>의 value prop에 의해 결정된다.

컴포넌트에서 가장 가까운 \<MyContext.Provider>가 업데이트되면, 이 Hook은 MyContext provider에 전달된 최신의 context value와 함께 리렌더를 일으킨다. 상위 컴포넌트에서 React.memo 또는 shouldComponentUpdate를 사용하더라도,  useContext를 사용하여 컴포넌트 자체에서부터 다시 렌더링된다.

useContext로 전달한 인자는 context 객체 그 자체이어야함을 기억해야 한다.

* **Correct:** useContext(MyContext)
* **Incorrect:** useContext(MyContext.Consumer)
* **Incorrect:** useContext(MyContext.Provider)

useContext를 호출한 컴포넌트는 context 값이 변경되면 항상 리렌더링 된다. 컴포넌트를 리렌더링하는 것에 비용이 많이 든다면, 메모이제이션을 사용하여 최적화할 수 있다.

<mark style="color:purple;">**useContext는 Context.Provider와 같이 사용한다.**</mark>

```jsx
const themes = {
  light: { foreground: '#000', background: '#eee' },
  dark: { foreground: '#fff', background: '#222' }
};

const ThemeContext = React.createContext(thems.light);

function App() {
  return (
    <ThemeContext.Provider value={themes.dark}>
      <Toolbar />
    <ThemeContext.Provider>
  );
}

function Toolbar(props) {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

function ThemedButton() {
  const theme = useContext(ThemeContext);
  
  return (
    <button style={{ background: theme.background, color: theme.foreground }}
      I am styled by theme context!
    </button>
  );
}
```
