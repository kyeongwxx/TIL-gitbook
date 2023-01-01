---
description: HTML form 엘리먼트는 자체적으로 내부 상태를 가지기 때문에 React의 다른 DOM 엘리먼트와는 다르게 동작한다.
---

# 08-forms

예를 들어, 순수한 HTML에서 이 form은 name을 입력받는다.

```html
<form>
  <label>
  Name:
    <input type='text' name='name' />
  </label>
  <input type='submit' value='Submit' />
</form>
```

이 form은 사용자가 submit 할 시 새로운 페이지로 이동하는 기본 HTML form 동작을 수행한다. React에서 동일한 동작을 원한다면 그대로 사용하면 된다.

하지만 대부분의 경우 자바스크립트 함수로 form의 submit을 처리하고 사용자가 form에 입력한 데이터에 접근하도록 하는 것이 편리하다.

이를 위한 표준 방식은 "제어 컴포넌트(controlled components)"라고 불리는 테크닉을 이용하는 것이다.

## 제어 컴포넌트 (Controlled Component)

HTML에서 <mark style="background-color:yellow;">\<input></mark>, <mark style="background-color:yellow;">\<textarea></mark>, <mark style="background-color:yellow;">\<select></mark>와 같은 form 엘리먼트는 일반적으로 사용자의 입력을 기반으로 자신의 state를 관리하고 업데이트한다.

React에서는 가변 state가 일반적으로 컴포넌트의 state 속성에 유지되며 setState()에 의해 업데이트된다.

이에 state를 single source of truth로 만들어 두 요소를 결합할 수 있다. 그러면 form을 렌더링하는 React 컴포넌트는 form에 발생하는 사용자 입력값을 제어한다.

이러한 방식으로 React에 의해 값이 제어되는 input form 엘리먼트를 '제어 컴포넌트'라고 한다.

예를 들어, 위 예시가 submit될 때 이름을 기록하길 원한다면 form을 controlled component로 작성할 수 있다.

```jsx
class NameForm extends React.Component }
  constructor(props) {
    super(props);
    this.state = {value: ''};
    
    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }
  
  handleChange(event) {
    this.setState({value: event.target.value});
  }
  
  handleSubmit(event) {
    alert('A name was submitted: ' + this.state.value);
    event.preventDefault();
  }
  
  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type='text' value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type='submit' value='Submit' />
      </form>
    );
  }
}         
```

value 어트리뷰트는 form 엘리먼트에 설정되므로 표시되는 값은 항상 this.state.value가 되고 state는 single source of truth가 된다.

모든 키 입력에서 handleChange가 동작하기 때문에 사용자가 입력할 때 보여지는 값(state)이 업데이트된다.

controlled component를 사용하면 input의 값은 항상 state에 의해 결정된다. 이 말은 input의 value를 다른 엘리먼트나 이벤트 핸들러에서 사용할 수 있다는 뜻.

## \<textarea>

HTML에서 \<textarea> 엘리먼트는 텍스트를 자식으로 정의한다.

```html
<textarea>
  Hello there, this is some text in a text area
</textarea>
```

React에서 \<textarea>는 value 어트리뷰트를 대신 사용한다.

```jsx
class EssayForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: 'Please write an essay about your favorite DOM element.'
    };
    
    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }
  
  handleChange(event) {
    this.setState({value: event.target.value});
  }
  
  handleSubmit(event) {
    alert('An essay was submitted: ' + this.state.value);
    event.preventDefault();
  }
  
  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Essay:
          <textarea value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type='submit' value='Submit' />
      </form>
    );
  }
}
```

## \<select>

HTML에서 \<select>는 드롭 다운 목록을 만든다.

```html
<select>
  <option value="grapefruit">Grapefruit</option>
  <option value="lime">Lime</option>
  <option selected value="coconut">Coconut</option>
  <option value="mango">Mango</option>
</select>
```

selected 어트리뷰트가 있는 Coconut \<option> 태그가 초기값이 된다.

React에서는 selected 어트리뷰트 대신 최상단 \<select>에 value 어트리뷰트를 사용한다.

```jsx
class FlavorForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: 'coconut'};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('Your favorite flavor is: ' + this.state.value);
    event.preventDefault();
  }
  
  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
        Pick your favorite flavor:
          <select value={this.state.value} onChange={this.handleChange}>
            <option value="grapefruit">Grapefruit</option>
            <option value="lime">Lime</option>
            <option value="coconut">Coconut</option>
            <option value="mango">Mango</option>
          </select>
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

전반적으로 <mark style="background-color:yellow;">\<input type='text'></mark>, <mark style="background-color:yellow;">\<textarea></mark>, <mark style="background-color:yellow;">\<select></mark> 모두 매우 비슷하게 동작한다. 제어 컴포넌트를 구현하는데 <mark style="color:purple;">value</mark>를 공통적으로 사용한다.

> \<select> 태그에 multiple 어트리뷰트를 허용한다면 value 어트리뷰트에 배열을 전달할 수 있다.

```jsx
<select multiple={true} value={['B', 'C']}>
```

## \<input type='file' />

HTML에서 <mark style="background-color:yellow;">\<input type='file' /></mark>는 사용자가 하나 이상의 파일을 로컬에서 서버로 업로드하거나, File API를 통해 자바스크립트로 조작할 수 있다.

값이 읽기 전용이기 때문에 React에서는 **비제어** 컴포넌트이다.

## Multiple inputs 제어하기

여러 input 엘리먼트를 제어해야할 때 각 엘리먼트에 <mark style="background-color:yellow;">name</mark> 어트리뷰트를 추가하고, <mark style="background-color:yellow;">event.target.name</mark> 값을 통해 핸들러가 어떤 작업을 할 지 선택할 수 있다.

```jsx
class Reservation extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      isGoing: true,
      numberOfGuests: 2
    };
    
    this.handleInputChange = this.handleInputChange.bind(this);
  }
  
  handleInputChange(event) {
    const target = event.target;
    const value = target.type === 'checkbox' ? target.checked : target.value;
    const name = target.name
    
    this.setState({
      [name]: value
    });
  }
  
  render() {
    return (
      <form>
        <label>
          Is going:
          <input
            name='isGoing'
            type='checkbox'
            checked={this.state.isGoing}
            onChange={this.handleInputChange} />
        </label>
        <br />
        <label>
          Number of guests:
          <input
            name='numberOfGuests'
            type='number'
            value={this.state.numberOfGuests}
            onChange={this.handleInputChange} />
        </label>
      </form>
    );
  }
}
```

## Controlled Input Null Value

제어 컴포넌트에 value를 지정하면 의도하지 않는 한 사용자가 변경할 수 없다.

value를 설정했는데 여전히 수정할 수 있다면 실수로 value를 undefined나 null로 설정했을 수 있다.

```jsx
ReactDOM.createRoot(mountNode).render(<input value="hi" />);

setTimeout(function() {
  ReactDOM.createRoot(mountNode).render(<input value={null} />);
}, 1000);
```

> Reference
>
> [https://reactjs.org/](https://reactjs.org/)
>
> **2023.01.01**
