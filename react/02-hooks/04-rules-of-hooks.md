---
description: Hook은 자바스크립트 함수이지만 사용 시 두 가지 규칙을 준수해야 한다.
---

# 04-Rules of Hooks

## 컴포넌트의 최상위에서만 Hook을 호출해야 한다.

<mark style="color:purple;">**반복문, 조건문 혹은 중첩 함수 내에서 Hook을 호출하지 말자.**</mark> early return을 만나기 전 React 함수의 최상위에서 Hook을 호출해야 한다. 이 규칙을 준수하면 컴포넌트가 렌더링 될 때마다 동일한 순서로 Hook이 호출되는 것을 보장한다. 이러한 점은 useState와 useEffect가 여러 번 호출되는 중에도 Hook의 상태를 올바르게 유지할 수 있도록 해준다.

## 오직 React 함수 내에서만 Hook을 호출해야 한다.

<mark style="color:purple;">**Hook을 일반적인 자바스크립트 함수에서 호출하지 말자.**</mark> 대신 아래와 같이 호출할 수 있다.

* React 함수 컴포넌트에서 Hook을 호출한다.
* Custom Hook에서 Hook을 호출한다.

이 규칙을 준수하면 컴포넌트의 모든 상태 관련 로직을 소스 코드에서 명확하게 보이도록 할 수 있다.

## Explanation

이전에 배웠듯 한 컴포넌트에서 여러개의 State나 Effect Hook을 사용할 수 있다.

```jsx
function Form() {
  // 1. name이라는 state 변수를 사용하세요.
  const [name, setName] = useState('Enzo');
  
  // 2. Effect를 사용해 폼 데이터를 저장하세요.
  useEffect(function persistForm() {
    localStorage.setItem('formData', name);
  });
  
  // 3. surname이라는 state 변수를 사용하세요.
  const [surname, setSurname] = useState('Fernández');
  
  // 4. Effect를 사용해서 제목을 업데이트합니다.
  useEffect(function updateTitle() {
    document.title = name + ' ' + surname;
  });
  
  // ...
}
```

그렇다면 React는 어떻게 특정 state가 어떤 useState 호출에 해당하는지 알 수 있을까? 정답은 <mark style="color:purple;">**React가 Hook이 호출되는 순서에 의존한다는 것**</mark>입니다. 모든 렌더링에서 Hook의 호출 순서는 같이 때문에 예시가 올바르게 작동하는 것이다.

```jsx
// -----------
// 첫 번째 렌더링
// -----------
useState('Enzo')         // 1. 'Enzo'라는 name state 변수를 선언합니다.
useEffect(persistForm)   // 2. 폼 데이터를 저장하기 위한 effect를 추가합니다.
useState('Fernández')    // 3. 'Fernández'라는 surname state 변수를 선언합니다.
useEffect(updateTitle)   // 4. 제목을 업데이트하기 위한 effect를 추가합니다.

// -----------
// 두 번째 렌더링
// -----------
useState('Enzo')         // 1. name state 변수를 읽습니다.(인자는 무시됩니다.)
useEffect(persistForm)   // 2. 폼 데이터를 저장하기 위한 effect가 대체됩니다.
useState('Fernández')    // 3. surname state 변수를 읽습니다.(인자는 무시됩니다.)
useEffect(updateTitle)   // 4. 제목을 업데이트하기 위한 effect가 대체됩니다.

// ...
```

렌더링 간 Hook의 호출 순서가 동일하다면 React는 지역 state를 각 Hook에 연동시킬 수 있다. 하지만 Hook을 조건문 안에서 호출한다면 어떤 일이 일어날까?

```jsx
// 🔴 조건문에 Hook을 사용함으로써 첫 번째 규칙을 깬 case
if (name !== '') {
  useEffect(function persistForm() {
    localStorage.setItem('formData', name);
  });
}
```

<mark style="color:green;">**name !== ''**</mark> 조건은 첫 번째 렌더링에서 <mark style="color:green;">**true**</mark>이기 때문에 Hook은 동작한다. 하지만 사용자가 그 다음 렌더링에서 폼을 초기화하면서 조건을 <mark style="color:green;">**false**</mark>로 만든다. 이제 렌더링 간 Hook을 건더뛰기 때문에 Hook의 호출 순서는 달라지게 된다.

```jsx
useState('Enzo')          // 1. name state 변수를 읽습니다.(인자는 무시됩니다.)
// useEffect(persistForm) // 🔴 Hook을 건너뛴다.
useState('Fernández')     // 🔴 2. surname state 변수를 읽는 데 실패하였습니다.
useEffect(updateTitle)    // 🔴 3. 제목을 업데이트하기 위한 effect 대체에 실패하였습니다.
```

React는 두 번째 useState Hook 호출에 무엇을 반환할지 몰랐을 것이다. React는 이전 렌더링처럼 컴포넌트 내 두 번째 Hook 호출이 persistForm effect와 일치할 것이라 예상했지만, 그렇지 않았다. 해당 시점부터 건너뛴 Hook 다음 호출되는 Hook들이 순서가 하나씩 밀리면서 버그를 발생시킨다.

이것이 <mark style="color:purple;">**컴포넌트 최상위에서 Hook이 호출되어야만 하는 이유**</mark>이다. 조건부로 effect를 실행하길 원한다면 조건문을 Hook 내부에 넣을 수 있다.

```jsx
useEffect(function persistForm() {
  // 👍 더 이상 첫 번째 규칙을 어기지 않는다.
  if (name !== '') {
    localStorage.setItem('formData', name);
  }
});
```

> Reference
>
> [https://reactjs.org/](https://reactjs.org/)
>
> **2023.02.06**
