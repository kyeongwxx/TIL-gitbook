# input 요소에서 엔터키를 사용하여 onClick 효과를 만드는 방법(feat. 검색창)

## TL;DR

* input 요소에 onKeyDown 핸들러 추가
* 핸들러에는 if (event.key === 'Enter') 조건에 따라 실행될 함수를 바인딩

회사에서 맡고 있는 프로젝트가 두 가지 있는데, 한 프로젝트에서는 검색 시 input 요소에 검색어를 입력하고 검색 버튼을 클릭하거나 엔터키를 치는 방법으로 검색 API가 호출된다.

반면, 다른 프로젝트에서는 검색 버튼 클릭시에만 API 호출이 되고 엔터키로는 검색 API 호출이 되지 않아 찾아 본 <mark style="color:purple;">**"input 요소에서 Enter 키를 사용하여 onClick 효과를 만드는 방법".**</mark>

```jsx
import { useState } from 'react';

function App() {
  const [value, setValue] = useState('');

  const handleClick = () => {
    console.log('Input submitted: ', value);
  };

  const handleKeyDown = (event) => {
    if (event.key === 'Enter') handleClick();
  };

  const handleChange = (event) => {
    setValue(event.target.value);
  };

  return (
    <>
      <input
        type='text'
        value={value}
        onChange={handleChange}
        onKeyDown={handleKeyDown}
      />
      <button onClick={handleClick}>Submit</button>
    </>
  );
}

export default App;
```

1. 입력값이 바뀔때마다 value의 상태를 업데이트하는 input 요소 생성
2. 버튼 클릭 시 실행시킬 handleClick() 함수 생성 및 button 요소에 바인딩
3. Enter 키를 누를 시 handleClick() 함수를 호출하는 onKeyDown 핸들러를 input 요소에 추가

> **2023.03.06**
