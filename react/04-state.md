# 04-state와 생명주기

이전 엘리먼트 렌더링 섹션의 시계 예시에서는 UI를 업데이트하는 한 가지 방법을 배웠다. <mark style="background-color:yellow;">root.render()</mark>를 호출하여 렌더링된 값을 변경하는 것.

```jsx
const root = ReactDOM.createRoot(document.getElementById('root'));

const tick = () => {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  root.render(element);
}

setInterval(tick, 1000);
```

state와 생명주기 섹션에서는 스스로 타이머를 설정하고, 매초 스스로 업데이트를 할 수 있는 재사용 가능한 캡슐화된 <mark style="background-color:yellow;">Clock</mark> 컴포넌트를 만들어 본다.

```jsx
const root = ReactDOM.createRoot(document.getElementById('root'));

const Clock = (props) => {
  return (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {props.date.toLocaleString()}.</h2>
    </div>
  );
}

const tick = () => {
  root.render(<Clock date={new Date()} />);
}

setInterval(tick, 1000);
```

<mark style="background-color:green;">state는 props와 유사하지만, private이라는 점과 컴포넌트가 완전히 컨트롤한다는 점이 다르다.</mark> props 대신 state를 쓰면 Clock 컴포넌트가 스스로 상태를 갖고, 업데이트를 알아서 하도록 만들 수 있다.

## 함수에서 클래스로 변환하기

1. <mark style="background-color:yellow;">React.Component</mark>를 확장하는 동일한 이름의 class를 생성한다.
2. <mark style="background-color:yellow;">render()</mark>라고 불리는 빈 메서드를 추가한다.
3. 함수의 내용을 <mark style="background-color:yellow;">render()</mark> 메서드 안으로 옮긴다.
4. <mark style="background-color:yellow;">render()</mark> 내용 안에 있는 <mark style="background-color:yellow;">props</mark>를 <mark style="background-color:yellow;">this.props</mark>로 변경한다.
5. 남아있는 빈 함수 선언을 삭제한다.

```jsx
const root = ReactDOM.createRoot(document.getElementById('root'));

class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.props.date.toLocaleString()}.</h2>
      </div>
    );
  }
}

const tick = () => {
  root.render(<Clock date={new Date()} />);
}

setInterval(tick, 1000);
```

Clock 컴포넌트는 이제 함수가 아닌 클래스로 정의된다.

클래스 컴포넌트에서 render() 메서드는 업데이트가 발생할 때마다 호출된다. 이때 같은 DOM 노드로 렌더링하는 한, Clock 클래스의 단일 인스턴스만 사용된다.

이렇게 하나의 인스턴스를 계속해서 사용하는 것은 local state와 생명주기 메서드 같은 부가 기능을 사용할 수 있게 해준다.

## 클래스에 local state 추가하기

<mark style="background-color:yellow;">date</mark>를 props에서 state로 이동하는 세 단계

1. render() 메서드 안에 있는 this.props.date를 <mark style="background-color:yellow;">this.state.date</mark>로 변경한다.

```jsx
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleString()}.</h2>
      </div>
    );
  }
}
```

2\. 초기 this.state를 지정하는 <mark style="background-color:blue;">class constructor</mark>를 추가한다. 클래스 컴포넌트는 항상 props로 기본 constructor를 호출해야 한다.

```jsx
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }
  
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleString()}.</h2>
      </div>
    );
  }
}
```

3\. 컴포넌트에서 date prop을 삭제한다.

```jsx
root.render(<Clock />);
```

결과 코드는 다음과 같다.

```jsx
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<Clock />);
```

## Lifecycle 메서드를 클래스에 추가하기

이제 Clock 컴포넌트가 스스로 타이머를 설정하고 매초 스스로 업데이트하도록 만들어보자.

리액트에서는 컴포넌트가 처음 DOM에 렌더링 되는 시점을 <mark style="background-color:blue;">'마운트(Mount)'</mark>, 컴포넌트가 생성한 DOM이 삭제되는 시점을 <mark style="background-color:blue;">'언마운트(Unmount)'</mark>라고 한다.

클래스 컴포넌트에서는 컴포넌트가 마운트되거나 언마운트되는 시점에 특정 코드가 동작할 수 있도록하는 Lifecycle 메서드를 사용할 수 있다.

Lifecycle 메서드는 컴포넌트가 삭제될 때 해당 컴포넌트가 사용 중이던 리소스를 확보하는 작업에도 유용하게 사용할 수 있다.

```jsx
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
  }

  componentWillUnmount() {
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

<mark style="background-color:yellow;">componentDidMount()</mark> 메서드는 컴포넌트의 return 값이 DOM에 렌더링된 후 실행된다.

```jsx
componentDidMount() {
  this.timerID = setInterVal(
    () => this.tick(),
    1000
  );
}
```

componentWillUnmount() 메서드로 컴포넌트가 DOM에서 삭제될 때 타이머를 해제한다.

```jsx
componentWillUnmount() {
  clearInterval(this.timerID);
}
```

local state를 업데이트 하기위해 <mark style="background-color:yellow;">this.setState()</mark>를 사용한다.

```jsx
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({date: new Date()});
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<Clock />);
```

위 예시의 전체 사이클을 파악해보면

1. ReactDOM.render()의 인자로 Clock 컴포넌트를 전달하면 React는 Clock 컴포넌트의 constructor를 호출한다.
2. 이어서 React는 바로 Clock 컴포넌트의 render()를 호출한다. 이를 통해 React는 화면에 표시되어야 할 내용을 알게 되고, React는 Clock의 렌더링 결과를 일치시키기 위해 DOM을 업데이트한다.
3. React는 업데이트를 마치면(Clock 컴포넌트가 렌더링되면) componentDidMount()를 호출한다.(타이머 설정)
4. 매초 브라우저가 tick()을 호출하면 그 안에서 Clock 컴포넌트는 setState()에 현재 시각을 나타내는 객체를 담아 호출함으로써 UI를 업데이트한다.
5. setState() 호출에 따라 React는 state가 변경된 것을 인지하고 render()를 호출하 DOM을 업데이트한다.
6. 컴포넌트가 DOM으로부터 한 번이라도 삭제된 적이 있다면 React는 componentWillUnmount()를 호출한다.&#x20;

## State 올바르게 사용하기

### 직접 State를 수정하지 않는다.

예를 들면 아래 코드는 컴포넌트를 리렌더링시키지 않는다.

```jsx
// Wrong
this.state.comment = 'Hello';
```

state 변경은 <mark style="background-color:yellow;">setState()</mark>를 사용한다. this.state를 지정할 수 있는 유일한 공간은 constructor이다.

```jsx
// Correct
this.setState({comment: 'Hello'});
```

### State 업데이트는 비동기적일 수 있다.

React는 성능을 위해 여러 setState() 호출을 단일 업데이트로 한꺼번에 처리할 수 있다.

this.props와 this.state가 비동기적으로 업데이트될 수 있기 때문에 this.state나 this.props를 값으로 바로 사용하지 않는다.

예를 들어, 다음 코드는 업데이트에 실패할 수 있다.

```jsx
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});
```

대신 state, props를 인자로 전달하는 함수를 작성하여 사용한다.

```jsx
// Correct
this.setState((state, props) => ({
  counter: state.counter + props.increment
}));
```

### State 업데이트는 merge된다.

setState()를 호출하면 React는 setState() 내 객체를 현재 state에 merge한다.

예를 들어, state는 다양한 변수를 가질 수 있다.

```jsx
constructor(props) {
  super(props);
  this.state = {
    posts: [],
    comments: []
  };
}
```

별도의 setState() 호출로 각 변수를 독립적으로 업데이트할 수 있다.

```jsx
componentDidMount() {
  fetchPosts().then(response => {
    this.setState({
      posts: response.posts
    });
  });

  fetchComments().then(response => {
    this.setState({
      comments: response.comments
    });
  });
}
```

병합은 shallow하게 이루어지기 때문에 this.setState({comments})는 this.state.comments에 merge되는 동안 this.state.posts에 영향을 주지 않는다.

## 데이터는 아래로 흐른다.

state는 local 또는 캡슐화라고도 불린다. 이유는 state를 갖고 있는 컴포넌트 본인 외에는 다른 어떤 컴포넌트에서도 state에 접근할 수 없기 때문이다.

컴포넌트는 자신의 state를 자식 컴포넌트에 props로 전달할 수 있다.

```jsx
<FormattedDate date={this.state.date} />
```

FormattedDate 컴포넌트는 date를 자신의 props로 받는다.

```jsx
const FormattedDate = (props) => {
  return <h2>It is {props.date.toLocaleTimeString()}.</h2>;
}
```

이를 “하향식(top-down)” 또는 “단방향식” 데이터 흐름이라고 한다. 모든 state는 항상 특정한 컴포넌트가 소유하고 있고, 그 state로부터 파생된 데이터나 UI는 오직 트리구조에서 자신보다 하위에 있는 컴포넌트에만 영향을 미친다.

```jsx
function App() {
  return (
    <div>
      <Clock />
      <Clock />
      <Clock />
    </div>
  );
}
```

위 예시에서 각 Clock 컴포넌트는 자신만의 타이머를 설정하고 독립적으로 업데이트한다.

> Reference
>
> [https://reactjs.org/](https://reactjs.org/)
