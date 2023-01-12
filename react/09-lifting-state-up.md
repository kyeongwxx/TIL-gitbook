---
description: >-
  종종 동일한 데이터에 대한 변경이 여러 컴포넌트에서 필요한 상황이 있다. 이럴 때는 가까운 조상 컴포넌트로 state를 끌어올리는 것이
  좋다.
---

# 09-Lifting State Up

주어진 온도에서 물의 끓는 여부를 체크하는 온도 계산기를 만들어 보자.

먼저 <mark style="color:purple;">\<BoilingVeridict /></mark> 컴포넌트 생성. 이 컴포넌트는 섭씨온도를 의미하는 <mark style="color:purple;">celsius</mark> prop을 받아서 온도가 물이 끓기에 충분한지 여부를 출력한다.

```jsx
function BoilingVerdict(props) {
  if (props.celsius >= 100) return <p>The water would boil.</p>;
  
  return <p>The water would not boil.</p>;
```

다음으로 <mark style="color:purple;">\<Calculator /></mark> 컴포넌트 생성. 이 컴포넌트는 온도를 입력할 수 있는 <mark style="color:purple;">input</mark>을 렌더링하고 값을 <mark style="color:purple;">this.state.temperature</mark>에 저장한다.

또한 <mark style="color:purple;">\<BoilingVeridict /></mark> 컴포넌트를 렌더링 한다.

```jsx
class Calculator extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.state = { temperature: ''};
  }
  
  handleChange(e) {
    this.setState({ temperature: e.target.value });
  }
  
  render() {
    const temperature = this.state.temperature;
    
    return (
      <fieldset>
        <legend>Enter temperature in Celsius:</legend>
        <input value={temperature} onChange={this.handleChange} />
        <BoilingVerdict celsius={parseFloat(temperature)} />
      </fieldset>
    );
  }
}       
```
