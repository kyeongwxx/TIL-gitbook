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

## 두 번째 input 추가하기

새로운 요구사항으로 섭씨 입력 필드에 화씨 입력 필드를 추가하고, 두 필드 간 동기화 상태를 유지한다.

<mark style="color:purple;">\<Calculator /></mark>에서 <mark style="color:purple;">\<TemperatureInput /></mark> 컴포넌트를 분리하는 작업부터 시작. 또한 <mark style="color:purple;">'c'</mark> 또는 <mark style="color:purple;">'f'</mark>의 값을 가질 수 있는 <mark style="color:purple;">scale</mark> prop를 추가한다.

```jsx
const scaleNames = {
  c: 'Celsius',
  f: 'Fahrenheit'
};

class TemperatureInput extends React.Component {
  constuctor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.state = { temperature: '' };
  }
  
  handleChange(e) {
    this.setState({ temperature: e.target.value });
  }
  
  render() {
    const temperature = this.state.temperature;
    const sacle = this.props.scale;
    
    return (
      <fieldset>
        <legend>Enter temperature in {sacleNames[scale]}:</legend>
        <input value={temperature} onChange={this.handleChange} />
      </fieldset>
    );
  }
}
```

이제 <mark style="color:purple;">\<Calculator /></mark> 컴포넌트가 두 개의 온도 입력 필드를 렌더링하도록 할 수 있다.

```jsx
class Calculator extends React.Component {
  render() {
    return (
      <div>
        <TemperatureInput scale='c' />
        <TemperatureInput scale='f' />
      </div>
    );
  }
}   
```

하지만 둘 중 하나의 input에 온도를 입력하더라도 다른 하나의 input은 갱신되지 않는 문제가 남아있는데, 이것은 초기에 두 입력 필드 간 동기화 상태를 유지하고자 했던 요구사항과는 맞지 않다.

또한 <mark style="color:purple;">\<Calculator /></mark> 컴포넌트에서 <mark style="color:purple;">\<BoilingVerdict /></mark>도 역시 보여줄 수 없는 상황이다. 현재 입력된 온도 정보가 <mark style="color:purple;">\<TemperatureInput /></mark> 안에 숨겨져 있으므로 <mark style="color:purple;">\<Calculator /></mark>는 해당 값을 알 수 없기 때문이다.

## 변환 함수 작성하기

먼저 섭씨 <-> 화씨 변환 함수를 생성.

```jsx
function toCelsius(fahrenheit) {
  return (fahrenheit - 32) * 5 / 9;
}

function toFahrenheit(celsius) {
  return (celsius * 9 / 5) + 32;
}
```

다음으로 <mark style="color:purple;">temperature</mark> 문자열과 변환 함수를 인수로 받아 문자열을 반환하는 또 다른 함수를 생성. 그리고 이 함수를 한 입력값에 기반해 나머지 입력값을 계산하는 용도로 사용.

이 함수는 올바르지 않은 <mark style="color:purple;">temperature</mark> 값에 대해서는 빈 문자열을 반환하고 값을 소수점 세 번째 자리로 반올림하여 출력함.

```jsx
function tryConvert(temperature, convert) {
  const input = parseFloat(temperature);
  
  if (Number.isNaN(input)) return '';
  
  const output = convert(input);
  const rounded = Math.round(output * 1000) / 1000;
  
  return rounded.toString();
}
```

예를 들어 <mark style="color:purple;">tryConvert('abc', toCelsius)</mark>는 빈 문자열을 반환하고, <mark style="color:purple;">tryConvert('10.22', toFahrenheit)는 '50.396'</mark>을 반환한다.

## State 끌어올리기

현재는 두 \<TemperatureInput /> 컴포넌트가 각각의 입력값을 각자의 state에 독립적으로 저장하고 있다.

하지만 섭씨 온도 입력값을 변경할 경우 화씨 온도 입력값 역시 변환된 온도를 반영할 수 있어야 하며, 그 반대의 경우에도 마찬가지여야 한다.

state를 공유할 수 있어야 한다는 말인데, React에서 이런 일은 해당 state를 필요로 하는 컴포넌트 간 가장 까까운 공통 조상으로 state를 끌어올림으로써 가능하다. 이를 <mark style="background-color:green;">"Lifting state up"</mark>이라고 한다.

즉, 두 <mark style="color:purple;">\<Temperature /></mark> 컴포넌트가 개별적으로 가지고 있던 state를 지우는 대신, 가장 가까운 공통 조상 컴포넌트인  <mark style="color:purple;">\<Calculator /></mark> 컴포넌트로 state 값을 끌어올릴 것이다.

<mark style="color:purple;">\<Calculator /></mark> 컴포넌트가 공유될 state를 소유하고 있으면 이 컴포넌트는 두 입력 필드의 현재 온도에 대한 source of truth가 되고 이를 통해 일관된 값을 유지하도록 할 수 있다. 다시 말하면 두 <mark style="color:purple;">\<TemperatureInput /></mark> 컴포넌트가 공통 부모인 <mark style="color:purple;">\<Calculator /></mark> 컴포넌트로부터 동일한 props를 받기 때문에 두 입력 필드는 항상 동기화된 상태를 유지할 수 있다.

우선 <mark style="color:purple;">\<TemperatureInput /></mark> 컴포넌트에서 this.<mark style="color:green;">state</mark>.temperature -> this.<mark style="color:green;">props</mark>.temperature로 변경.

{% code title="<TemperatureInput />" %}
```jsx
...
render() {
  // Before: const temperature = this.state.temperature;
  const temperature = this.props.temperature;
...
```
{% endcode %}

이제 state를 사용하는 것이 아닌 props를 받아와서 사용하기 때문에 this.setState()를 사용할 수 없다. <mark style="color:purple;">props는 읽기 전용</mark>이므로 <mark style="color:purple;">\<Calculator /></mark> 부모 컴포넌트에서 onTemperatureChange() 함수를 생성하고, <mark style="color:purple;">\<TemperatureInput /></mark> 자식 컴포넌트에서 this.props.onTemperatureChange로 받아와 사용해야 한다.

{% code title="<TemperatureInput />" %}
```jsx
...
handleChange(e) {
  // Before: this.setState({ temperature: e.target.value });
  this.props.onTemperatureChange(e.tartget.value);
...
```
{% endcode %}

<mark style="color:orange;">onTemperature prop</mark>은 부모 컴포넌트 <mark style="color:purple;">\<Calulator /></mark>로 부터 <mark style="color:orange;">temperature prop</mark>과 함께 전달된다. 이를 이용해 <mark style="color:purple;">\<Temperature /></mark> 컴포넌트의 지역 state를 수정하므로 변경된 새 값을 전달받은 두 입력 필드는 모두 리렌더링 된다.

{% code title="<TemperatureInput />" %}
```jsx
class TemperatureInput extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
  }
  
  handleChange(e) {
    this.props.onTemperatureChange(e.target.value);
  }
  
  render() {
    const temperature = this.props.temperature;
    const sclae = this.props.scale;
    
    return (
      <fieldset>
        <legend>Enter temperature in {scaleNames}:</legend>
        <input value={temperature} onChange={this.handleChange} />
      </fieldset>
    );
  }
}
```
{% endcode %}

두 입력 필드로부터 끌어올린 state인 temperature와 scale의 현재 입력값은 <mark style="color:purple;">\<Calculator /></mark> 컴포넌트의 로컬 state에 저장된다.

예를 들어 섭씨 입력 필드에 37을 입력하면 \<Calculator /> 컴포넌트의 state는 다음과 같다.

{% code title="<Calculator />" %}
```jsx
...
{
  temperature: '37',
  scale: 'c'
}
...
```
{% endcode %}

이후 화씨 입력 필드의 값을 212로 수정하면 \<Calculator /> 컴포넌트의 state는 다음과 같다.

{% code title="<Calculator />" %}
```javascript
...
{
  temperature: '212',
  scale: 'f'
}
...
```
{% endcode %}

이제 현재 temperature와 scale에 기반해 다른 입력 필드의 값을 추론할 수 있기 때문에 두 입력 필드에 모두 값을 저장하는, 즉 두 state를 사용하는 일은 불필요한 작업이었던 것이 된다.

두 입력 필드의 값이 동일한 state로부터 계산되므로 이 둘은 항상 동기화된 상태를 유지한다.

{% code title="<Calculator />" %}
```jsx
class Calculator extends React.Component {
  constructor(props) {
    super(props);
    this.handleCelsiusChange = this.handleCelsiusChange.bind(this);
    this.handleFahrenheitChange = this.handleFahrenheitChange.bind(this);
    this.state = { temperature: '', scale: 'c' };
  }
  
  handleCelsiusChange(temperature) {
    this.setState({ scale: 'c', temperature });
  }
  
  handleFahrenheitChange(temperature) {
    this.setState({ scale: 'f', temperature });
  }
  
  render() {
    const scale = this.state.scale;
    const temperature = this.state.temperature;
    const celsius = scale === 'f' ? tryConvert(temperature, toCelsius) : temperature;
    const fahrenheit = scale === 'c' ? tryConvert(temperature, toFahrenheit) : temperature;
    
    return (
      <div>
        <TemperatureInput
          scale='c'
          temperature={celsius}
          onTemperatureChange={this.handleCelsiusChange}
        />
        <TemperatureInput
          scale='f'
          temperature={fahrenheit}
          onTemperatureChange={this.handleFahrenheitChange}
        />
        <BoilingVerdict celsius={parseFloat(celsius)} />
      </div>
    );
  }
}
```
{% endcode %}

이제 어떤 입력 필드를 수정하든 <mark style="color:purple;">\<Calculator /></mark> 컴포넌트의 this.state.temperature와 this.state.scale이 갱신된다.

입력 필드 중 하나는 있는 그대로의 값을 받으므로 사용자 입력값이 저장되고, 다른 입력 필드의 값은 저장된 값에 기반해 계산된다.

다시 정리하면

1. 값이 입력되면 React는 DOM \<input />의 onChange에 지정된 함수(<mark style="color:purple;">\<TemperatureInput /></mark> 컴포넌트의 handleChange 메서드)를 호출.
2. 위 지정된 함수는 새로 입력된 값과 함께 this.props.onTemperatureChange()를 호출.
3. 입력 필드에 따라 <mark style="color:purple;">\<Calculator /></mark> 컴포넌트의 handleCelsiusChange 또는 handleFahrenheitChange 메서드 호출
4. 위 메서드들은 내부적으로 <mark style="color:purple;">\<Calculator /</mark>> 컴포넌트가 새 입력값 또는 수정한 입력값에 따라 this.setState()를 호출함으로써 UI를 리렌더링함.
5. React는 <mark style="color:purple;">\<Calculator /></mark> 컴포넌트의 render 메서드를 호출하고, 이 단계에서 온도 변환이 입력된 필드 값 기반 계산되어 이루어짐.
6. React는 <mark style="color:purple;">\<Calculator /></mark> 컴포넌트가 전달한 props와 함께 각 <mark style="color:purple;">\<TemperatureInput /></mark> 컴포넌트의 render 메서드를 호출.
7. React DOM은 물의 끓는 여부와 올바른 입력값을 일치시키는 작업과 함께 DOM을 갱신함. 값을 변경한 입력 필드는 현재 입력 값을 그대로 받고, 다른 입력 필드는 변환된 온도 값으로 갱신됨.

## Lessons learned

React 애플리케이션에서 변경이 일어나는 데이터에 대해서는 source of truth를 하나만 두어야 한다. 보통 state는 렌더링에 해당 state를 필요로 하는 컴포넌트에 먼저 추가 되는데, 다른 컴포넌트 또한 해당 state가 필요하게 되면 그 state를 가장 가까운 공통 조상 컴포넌트로 끌어올리면 된다.

state를 끌어올리는 작업은 양방향 바인딩 접근 방식보다 더 많은 보일러 플레이트 코드를 유발하지만, 버그를 찾고 격리하기 더 쉽게 만든다는 장점이 있다. 모든 state는 특정 컴포넌트 안에 존재하고, 해당 컴포넌트만 state를 스스로 변경할 수 있으므로, 버그가 존재할 수 있는 범위가 크게 줄어든다. 사용자의 입력을 거부하거나 변형하는 자체 로직을 구현할 수도 있고.

> Reference
>
> [https://reactjs.org/](https://reactjs.org/)
>
> **2023.01.13**
