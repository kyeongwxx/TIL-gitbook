# 03-Components와 Props

> 컴포넌트를 통해 UI를 재사용 가능한 개별적 조각으로 나누고, 각 조각을 개별적으로 다룰 수 있다. 즉, 엘리먼트 트리를 캡슐화한다는 것. <mark style="background-color:orange;">모듈화를 통해 복잡한 UI를 개별적으로 쉽게 관리</mark>할 수 있게 한다는 말

## 함수 컴포넌트와 클래스 컴포넌트

컴포넌트를 정의하는 가장 간단한 방법은 자바스크립트 함수를 작성하는 것

```jsx
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

위 함수는 데이터를 가진 props 객체 인자를 받은 후 React 엘리먼트를 반환하므로 유효한 React 컴포넌트이다.

이러한 컴포넌트는 자바스크립트 함수이기 때문에 말 그대로 <mark style="background-color:green;">함수 컴포넌트</mark>라고 한다.



class를 사용하여 컴포넌트를 정의할 수도 있다.

```jsx
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

위와 같은 컴포넌트를 <mark style="background-color:green;">클래스 컴포넌트</mark>라 한다.

## 컴포넌트 렌더링

지금까지는 DOM 태그만을 사용해 React 엘리먼트를 나타냈지만,

```javascript
const element = <div />;
```

React 엘리먼트는 사용자 정의 컴포넌트로도 나타낼 수 있다.

```javascript
const element = <Welcome name="Sara" />;
```

React가 사용자 정의 컴포넌트로 작성한 엘리먼트를 발견하면 JSX attribute와 children을 해당 컴포넌트에 단일 객체로 전달한다. 이 객체를 <mark style="background-color:green;">props</mark>라 한다.

```jsx
const Welcome = (props) => {
  return <h1>Hello, {props.name}</h1>;
}

const root = ReactDOM.createRoot(document.getElementById('root'));
const element = <Welcome name="Eden Hazard" />;
root.render(element);
```

위 예시에서 일어나는 일을 차례로 살펴보면

1. <mark style="background-color:yellow;">\<Welcome name="Eden Hazard" /></mark> 엘리먼트로 <mark style="background-color:yellow;">root.render()</mark>를 호출
2. React는 <mark style="background-color:yellow;">{name: "Eden Hazard"}</mark>를 props로 하여 <mark style="background-color:yellow;">Welcome</mark> 컴포넌트를 호출
3. <mark style="background-color:yellow;">Welcome</mark> 컴포넌트는 결과적으로 <mark style="background-color:yellow;">\<h1>Hello, Sara\</h1></mark> 엘리먼트를 반환
4. React DOM은 <mark style="background-color:yellow;">\<h1>Hello, Sara\</h1></mark> 엘리먼트와 일치하도록 DOM을 업데이트

> 컴포넌트의 이름은 항상 대문자로 시작한다. React는 소문자로 시작하는 컴포넌트를 DOM 태그로 처리하기 때문

## 컴포넌트 합성

컴포넌트는 return에 <mark style="background-color:green;">다른 컴포넌트를 참조</mark>할 수 있다. 즉, 다양한 상황에 따라 공통 컴포넌트를 만들어 사용할 수 있다는 말. button, form, dialog, screen 등

예를 들어 <mark style="background-color:yellow;">Welcome</mark>을 여러 번 렌더링하는 <mark style="background-color:yellow;">App</mark> 컴포넌트를 만들 수 있다.

```jsx
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

function App() {
  return (
    <div>
      <Welcome name="Cesc Fabregas" />
      <Welcome name="Eden Hazard" />
      <Welcome name="Diego Costa" />
    </div>
  );
}
```

## 컴포넌트 추출

컴포넌트는 여러 개의 작은 컴포넌트로 나눌 수 있다.

다음 <mark style="background-color:yellow;">Comment</mark> 컴포넌트를 보자

```jsx
const Comment = (props) => {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <img className="Avatar"
          src={props.author.avatarUrl}
          alt={props.author.name}
        />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-data">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

이 컴포넌트는 author, text, date를 props로 받은 후 소셜 미디어 웹 사이트의 코멘트를 그린다.

이 컴포넌트는 태그들이 모두 중첩 구조로 이루어져 있어 변경하기 어렵고, 재사용하기도 힘들다. 이 컴포넌트에서 몇 가지 컴포넌트를 추출해보자.

먼저 <mark style="background-color:yellow;">Avatar</mark> 컴포넌트 추출

```jsx
const Avatar = (props) => {
  return (
    <img className="Avatar"
      src={props.user.avatarUrl}
      alt={props.user.name}
    />
  );
}
```

이렇게 되면 <mark style="background-color:yellow;">Comment</mark> 컴포넌트가 보다 단순해진다.

```jsx
const Comment = (props) => {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <Avatar user={props.author} />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-data">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

다음으로 <mark style="background-color:yellow;">UserInfo</mark> 컴포넌트 추출

```jsx
const UserInfo = (props) => {
  return (
    <div className="UserInfo">
      <Avatar user={props.user} />
      <div className="UserInfo-name">
        {props.user.name}
      </div>
    </div>
  );
}
```

더욱 단순해진 <mark style="background-color:yellow;">Comment</mark> 컴포넌트

```jsx
const Comment = (props) => {
  return (
    <div className="Comment">
      <UserInfo user={props.author} />
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-data">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

UI 일부가 여러 번 사용되거나(Button, Panel, Avatar), UI 일부가 자체적으로 복잡한(App, FeedStory, Comment) 경우에는 별도의 컴포넌트로 만드는게 좋다.

## Props는 읽기 전용

컴포넌트 자체의 props를 수정해서는 안된다.

```javascript
function sum(a, b) {
  return a + b;
}
```

위와 같은 함수를 <mark style="background-color:green;">순수 함수</mark>라고 한다. 입력값을 바꾸려 하지 않고 항상 동일한 입력값에 대해 동일한 결과를 반환한다.

반면 다음은 자신의 입력값을 바꾸는 <mark style="background-color:green;">비순수 함수</mark>다.

```javascript
function withdraw(account, amount) {
  account.total -= amount;
}
```

<mark style="background-color:orange;">모든 React 컴포넌트는 자신의 props를 다룰 때 반드시 순수 함수처럼 동작해야 한다.</mark>

> Reference
>
> [https://reactjs.org/](https://reactjs.org/)
