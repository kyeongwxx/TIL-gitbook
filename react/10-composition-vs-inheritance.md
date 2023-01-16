---
description: React는 강력한 합성 모델을 가지고 있으며, 컴포넌트 간 코드 재사용을 위해 상속 대신 합성을 사용하는 것이 좋다.
---

# 10-Composition vs Inheritance

## Containment

컴포넌트들은 어떤 자식 엘리먼트가 들어올지 미리 예상할 수 없는 상황이 있다. 범용적인 'box' 역할을 하는 <mark style="color:purple;">\<Sidebar /></mark> 또는 <mark style="color:purple;">\<Dialog /></mark>와 같은 컴포넌트가 그러하다.

이러한 컴포넌트에서는 특수한 <mark style="color:purple;">children prop</mark>을 사용하여 자식 엘리먼트를 그대로 전달하는 것이 좋다.

```jsx
function FancyBorder(props) {
  return (
    <div className={'FancyBorder FancyBorder-' + props.color} >
      {props.children}
    </div>
  );
}
```

이러한 방식으로 다른 컴포넌트에서 JSX를 중첩하여 임의의 자식을 전달할 수 있다.

```jsx
function WelcomeDialog() {
  return (
    <FancyBorder color='blue'>
      <h1 className='Dialog-title'>
        Welcome
      </h1>
      <p className='Dialog-message'>
        Thank you for visiting our spacecraft!
      </p>
    </FancyBorder>
  );
}
```

이때 JSX 태그 내 요소들이 <mark style="color:purple;">\<FancyBorder /></mark> 컴포넌트의 <mark style="color:purple;">children prop</mark>으로 전달된다. \<div> 태그 내 {props.children}으로 전달된 엘리먼트들이 출력된다는 의미.

흔하진 않지만 종종 컴포넌트에 children 하나가 아닌 여러 개의 prop이 필요할 수도 있다. 이런 상황에서는 children 대신 자신만의 고유한 convention을 적용할 수 있다.

```jsx
function SplitPane(props) {
  return (
    <div className='SplitPane'>
      <div className='SplitPane-left'>
        {props.left}
      </div>
      <div className='SplitPane-right'>
        {props.right}
      </div>
    </div>
  );
}

function App() {
  return (
    <SplitPane
      left={<Contacts />}
      right={<Chat />}
    />
  );
}
```

<mark style="color:purple;">\<Contacts /></mark>와 <mark style="color:purple;">\<Chat /></mark> 같은 React 엘리먼트는 단지 객체이기 때문에 다른 데이터처럼 prop으로 전달할 수 있다. prop으로 전달할 수 있는 것에는 제한이 없다.

## Specialization

예를  들어 <mark style="color:purple;">\<WelcomeDialog /></mark> 컴포넌트는 <mark style="color:purple;">\<Dialog /></mark> 컴포넌트의 '특수한 케이스'이다. 이처럼 어떤 컴포넌트 내에 특수한 컴포넌트가 자리할 수 있는데, React에서는 이 또한 합성을 통해 해결할 수 있다.

더 specific한 <mark style="color:purple;">\<WelcomeDialog /></mark> 컴포넌트가 더 generic한 <mark style="color:purple;">\<Dialog /></mark> 컴포넌트를 렌더링하고 props를 통해 내용을 구성한다.

```jsx
function Dialog(props) {
  return (
    <FancyBorder color='blue'>
      <h1 className='Dialog-title'>
        {props.title}
      </h1>
      <p className='Dialog-message'>
        {props.message}
      </p>
    </FancyBorder>
  );
}

function WelcomeDialog() {
  return (
    <Dialog
      title='Welcome'
      message='Thank you for visiting our spacecraft'
    />
  );
}
```

> Reference
>
> [https://reactjs.org/](https://reactjs.org/)
>
> **2023.01.16**
