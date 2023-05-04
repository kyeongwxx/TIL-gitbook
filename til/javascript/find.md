# find() 메서드의 적절한 사용법

## TL;DR

* `find()` 메서드는 배열에서 특정 조건과 일치하는 첫 번째 요소를 찾으려고 할 때 사용
* 특히, 이 첫 번째 요소에 대해 작업을 수행하려고 할 때 유용

| 품목명 | 수량 | 단가    |
| --- | -- | ----- |
| aaa | 10 | 3000  |
| bbb | 20 | 5000  |
| ccc | 30 | 10000 |

회사 업무 간 위와 같은 표에서 다음 step으로 넘어갈 시 수량과 단가에 대해 valid한 값이 들어가 있는지에 대한 validation을 만들고 있었다.

입력을 하지 않았는지(null), 숫자인지, 1이상 인지, 정수 인지 등 여느 때처럼 `filter()` 메서드를 이용하여 작성하고 있었는데, 받아오는 품목이 10,000개가 되면 과연 모든 배열을 검색해야하는 `filter()` 메서드가 옳은 방법일까? 차례로 검색하다가 하나만 걸려도 체크되게 할 수 있지 않을까? 하는 생각이 들었다.

`find()` 메서드 사용을 권유받던 다른 누군가의 코드 리뷰를 본 기억이 나서 이번 기회에 사용해보았다.

```javascript
arr.find(callback[, thisArg])
```

* `매개변수`
  * `callback` - 배열의 각 값에 대해 실행할 함수. 아래의 세 인자를 받는다.
    * `element` - 콜백함수에서 처리할 현재 요소.
    * `index(optional)` - 콜백함수에서 처리할 현재 요소의 인덱스.
    * `array(optional)` - find() 메서드를 호출한 배열.
  * `thisArg(optional)` - 콜백함수가 호출될 때 this로 사용할 객체.
* `return` - 주어진 판별 함수를 만족하는 첫 번째 요소의 값. 그 외에는 undefined.

```javascript
const validateProductInfo = async () => {
    if (productInfoList?.find((productInfo) => !productInfo.price)) {
      swal("단가를 입력해주세요.", "", "error");
      return;
    }
    
    if (productInfoList?.find((productInfo) => productInfo.price < 1)) {
      swal("1 이상의 단가를 입력해주세요.", "", "error");
      return;
    }
    
    if (productInfoList?.find((productInfo) => isNaN(productInfo.price))) {
      swal("올바른 단가(숫자)를 입력해주세요.", "", "error");
      return;
    }
    
    if (productInfoList?.find((productInfo) => !isInteger(productInfo.price)) {
      swal("올바른 단가(정수)를 입력해주세요.", "", "error");
      return;
    }
    // ...
```

`filter()` 메서드 대신 사용한 이점으로는

* 보다 명확하다는 점
* 전체 배열을 검색할 필요가 없다는 점

이 있겠고,

반대로 `filter()` 메서드는

* 배열에서 특정 조건과 일치하는 모든 요소를 찾으려고 할 때
* 이 모든 요소에 대해 작업을 수행하려고 할 때
* 특정 조건과 일치하는 요소만으로 새 배열을 생성하려고 할 때

사용해주면 되겠다.

> Reference\
> [https://developer.mozilla.org/ko/](https://developer.mozilla.org/ko/)\
> **2023.05.04**
