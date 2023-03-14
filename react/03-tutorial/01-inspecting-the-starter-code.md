# 01-Inspecting the starter code

## App.js

App.js의 코드는 컴포넌트를 생성한다. React에서 컴포넌트는 UI의 일부를 나타내는 재사용 가능한 코드 조각이다. 컴포넌트는 UI 요소를 렌더링, 관리 및 업데이트를 하는 데 사용된다.

{% code title="App.js" %}
```jsx
export default function Square() {
  return <button className="square">X</button>;
}
```
{% endcode %}

**첫 번째 줄**은 Square라는 함수를 정의한다. <mark style="color:purple;">**export**</mark> 키워드를 사용하면 이 파일 외부에서 해당 함수를 사용할 수 있게 해준다.

<mark style="color:purple;">**default**</mark> 키워드는 코드를 사용하는 다른 파일에 이 함수가 main 함수임을 알려준다.

**두 번째 줄**은 버튼을 return 한다. <mark style="color:purple;">**return**</mark> 키워드는 뒤에 오는 모든 것들이 함수를 호출한 곳에 값으로 반환됨을 의미한다.

<mark style="color:purple;">**\<button>**</mark>은 <mark style="color:purple;">**JSX**</mark> 요소이다. JSX 요소는 보여주고자 하는 것(view)을 자바스크립트 코드와 HTML 태그의 조합으로 나타낸 것을 의미한다.

<mark style="color:purple;">**className="square"**</mark>는 버튼의 스타일을 지정하는 방법을 CSS에 알려주는 버튼 프로퍼티이다.

<mark style="color:purple;">**X**</mark>는 버튼 안에 표시되는 텍스트이며, <mark style="color:purple;">**\</button>**</mark>은 JSX 요소를 닫아 다음 내용이 버튼 안에 들어가지 않아야 함을 나타낸다.

## styles.css

이 파일은 React 앱의 스타일을 정의한다.

{% code title="styles.css" %}
```css
* { ... }
body { ... }
.square { ... }
```
{% endcode %}

<mark style="color:purple;">**\***</mark>과 <mark style="color:purple;">**body**</mark> 선택자는 앱의 전체적인 부분의 스타일을 정의하고, <mark style="color:purple;">**.square**</mark> 선택자는 className 프로퍼티가 square로 설정된 모든 컴포넌트의 스타일을 정의한다.

이 코드에서는 App.js 파일에 있는 Square 컴포넌트의 버튼과 일치한다.

## index.js

App.js 파일에서 만든 컴포넌트와 웹 브라우저 사이의 다리 역할을 한다.

{% code title="index.js" %}
```jsx
import React, { StrictMode } from "react";
import { createRoot } from "react-dom/client";
import "./styles.css";
import App from "./App";

const root = createRoot(document.getElementById("root"));
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```
{% endcode %}

1 \~ 4번째 줄은 React, React 라이브러리, css, 컴포넌트 등 필요한 것들을 import 한다.

그 밑으로는 import한 모든 것들을 가져와 public 폴더의 Index.html 파일에 주입한다.

> Reference\
> [https://beta.reactjs.org/](https://beta.reactjs.org/)\
> **2023.03.14**
