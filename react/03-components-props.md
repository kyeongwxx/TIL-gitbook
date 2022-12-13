# 03-Components와 Props

> 컴포넌트를 통해 UI를 재사용 가능한 개별적 조각으로 나누고, 각 조각을 개별적으로 다룰 수 있다. 즉, 엘리먼트 트리를 캡슐화한다는 것. <mark style="background-color:blue;">모듈화를 통해 복잡한 UI를 개별적으로 쉽게 관리</mark>할 수 있게 한다는 말

## 함수 컴포넌트와 클래스 컴포넌트

컴포넌트를 정의하는 가장 간단한 방법은 자바스크립트 함수를 작성하는 것

```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

위 함수는 데이터를 가진 props 객체 인자를 받은 후 React 엘리먼트를 반환하므로 유효한 React 컴포넌트이다.

이러한 컴포넌트는 자바스크립트 함수이기 때문에 말 그대로 <mark style="background-color:green;">함수 컴포넌트</mark>라고 호칭한다.
