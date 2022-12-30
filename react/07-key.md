# 07-리스트와 Key

먼저 자바스크립트에서 리스트를 어떻게 변환하는지 살펴보자.

아래는 map 함수를 이용하여 numbers 배열의 값을 두 배로 만든 후, 반환되는 새 배열을 doubled 변수에 할당하고 log를 확인하는 코드이다.

```javascript
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map((number) => number * 2);
console.log(doubled);
```

이 코드는 콘솔에 \[2, 4, 6, 8, 10]을 출력한다.

React에서 배열을 엘리먼트의 리스트로 만드는 방식은 이와 거의 동일하다.

## 여러 개의 컴포넌트 렌더링하기

엘리먼트 모음을 만들고 중괄호 {}를 이용하여 JSX에 포함시킬 수 있다.

map 함수를 이용해서 numbers 배열을 반복 실행하고, 각 항목에 대해 \<li> 엘리먼트를 반환, 결과를 listItems 변수에 저장해보자.

```jsx
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li>{number}</li>
);
```

그러면 \<ul> 엘리먼트 안에 listItems 배열을 포함할 수 있다. 아래 코드는 1부터 5까지의 숫자로 이루어진 리스트를 보여준다.

```jsx
<ul>{listItems}</ul>
```

## 기본 리스트 컴포넌트

이전 예제를 컴포넌트화 해보자

```jsx
const NumberList = (props) => {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li>{number}</li>
  );
  
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<NumberList numbers={numbers} />);
```

이 코드를 실행하면 리스트의 각 항목에 key를 넣어야 한다는 경고가 표시되는데, key는 엘리먼트 리스트를 만들 때 포함해야 하는 특수한 문자열 어트리뷰트이다.

numbers.map() 안에서 리스트의 각 항목에 key를 할당하여 문제를 해결할 수 있다.

```jsx
const NumberList = (props) => {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li key={number.toString()}>
      {number}
    </li>
  );
  
  return (
    <ul>{listItems}</ul>
  );
}
```

## Key

key는 React가 어떤 item을 변경, 추가 또는 삭제할지 식별하는 것을 도와준다. key는 고유성을 갖기 위해 배열 내 엘리먼트에 지정되어야 한다.

```jsx
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li key={number.toString()}>
    {number}
  </li>
);
```

key를 사용하는 가장 좋은 방법은 다른 item들 사이에서 해당 item을 고유하게 식별할 수 있는 문자열을 사용하는 것이다. 대부분 데이터의 id를 key로 사용한다.

```jsx
const todoItems = todos.map((todo) =>
  <li key={todo.id}>
    {todo.text}
  </li>
);
```

id가 없다면 최후의 수단으로 index를 key로 사용할 수 있다.

```jsx
const todoItems = todos.map((todo, index) =>
  // Only do this if items have no stable IDs
  <li key={index}>
    {todo.text}
  </li>
);
```

item의 순서가 바뀔 수 있는 경우 key로 index를 사용하는 것은 권장되지 않지만, key를 명시적으로 지정하지 않으면 React는 기본적으로 index를 key로 사용한다.

## Key로 컴포넌트 추출하기

key는 둘러싼 배열의 context에서만 유의미하다.

예를 들어, \<ListItem /> 컴포넌트를 추출할 경우, \<ListItem /> 컴포넌트 내 \<li> 엘리먼트가 아니라 배열, context 내의 \<ListItem /> 컴포넌트가 key를 가져야 한다.

<mark style="color:red;">잘못된 key 사용법</mark>

```jsx
const ListItem = (props) => {
  const value = props.value;
  
  return (
    // 틀렸습니다! 여기에는 key를 지정할 필요가 없습니다.
    <li key={value.toString()}>
      {value}
    </li>
  );
}

const NumberList = (props) => {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    // 틀렸습니다! 여기에 key를 지정해야 합니다.
    <ListItem value={number} />
  );
  
  return (
    <ul>{listItems}</ul>
  );
}
```

<mark style="color:green;">올바른 key 사용법</mark>

```jsx
const ListItem = (props) => {
  // 맞습니다! 여기에는 key를 지정할 필요가 없습니다.
  return <li>{props.value}</li>;
}

const NumberList = (props) => {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    // 맞습니다! 배열 안에 key를 지정해야 합니다.
    <ListItem key={number.toString()} value={number} />
  );
  
  return <ul>{listItems}</ul>;
}
```

## Key는 형제 사이에서만 고유한 값이어야 한다.

key는 배열 내의 형제 사이에서만 고유해야 하고, 전체 범위에서 고유할 필요는 없다. 두 개의 다른 배열을 만들 때 동일한 key를 사용할 수 있다.

```jsx
const Blog = (props) => {
  const sidebar = (
    <ul>
      {props.posts.map((post) =>
        <li key={post.id}>
          {post.title}
        </li>
      )}
    </ul>
  );
  
  const content = props.posts.map((post) =>
    <div key={post.id}>
      <h3>{post.title}</h3>
      <p>{post.content}</p>
    </div>
  );
  
  return (
    <div>
      {sidebar}
      <hr />
      {content}
    </div>
  );
}

const posts = [
  {id: 1, title: 'Hello World', content: 'Welcome to learning React!'},
  {id: 2, title: 'Installation', content: 'You can install React from npm.'}
];

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<Blog posts={posts} />);
```

React에서 key는 힌트를 제공하지만 컴포넌트로 전달하지는 않는다. 컴포넌트에서 key와 동일한 값이 필요하면 다른 이름의 prop으로 전달해야 한다.

```jsx
const content = posts.map((lost) =>
  <Post
    key={post.id}
    id={post.id}
    title={post.title} />
);
```

위 예제에서 \<Post /> 컴포넌트는 props.id를 읽을 수 있지만, props.key는 읽을 수 없다.

## JSX에 map 포함시키기

위에서 별도의 listItems 변수를 선언하고 이를 JSX에 포함했었다.

```jsx
const NumberList = (props) => {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <ListItem
      key={number.toString()}
      value={number}
    />
  );
  
  return <ul>{listItems}</ul>;
}
```

JSX를 사용하면 중괄호 내에 모든 표현식을 넣을 수 있으므로 map 함수의 결과를 인라인으로도 처리할 수 있다.

```jsx
const NumberList = (props) => {
  const numbers = props.numbers;
  
  return (
    <ul>
      {numbers.map((number) =>
        <ListItem key={number.toString()} value={number} />
      )}
    </ul>
  );
}
```

map 함수가 너무 중첩된다면 컴포넌트로 추출하는 것이 좋다.

> Reference
>
> [https://reactjs.org/](https://reactjs.org/)
>
> **2022.12.30**
