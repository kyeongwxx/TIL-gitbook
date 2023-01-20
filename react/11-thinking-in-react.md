---
description: React로 상품을 검색할 수 있는 간단한 데이터 테이블을 만들어보자
---

# 11-Thinking In React

## Start with A Mock

목업과 JSON API 가지고 있다고 생각해보자. 목업은 다음과 비슷할 것이다.

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

JSON API는 아래와 같은 데이터를 반환한다.

```jsx
[
  {category: "Sporting Goods", price: "$49.99", stocked: true, name: "Football"},
  {category: "Sporting Goods", price: "$9.99", stocked: true, name: "Baseball"},
  {category: "Sporting Goods", price: "$29.99", stocked: false, name: "Basketball"},
  {category: "Electronics", price: "$99.99", stocked: true, name: "iPod Touch"},
  {category: "Electronics", price: "$399.99", stocked: false, name: "iPhone 5"},
  {category: "Electronics", price: "$199.99", stocked: true, name: "Nexus 7"}
];
```

## Step 1: UI를 컴포넌트 계층 구조로 나누기

첫 번째로 할 일은 모든 컴포넌트를 박스로 표시하고 이름을 붙이는 것.

컴포넌트를 구분하는 방법이 어렵다면 새로운 함수나 객체를 만든다고 생각하면 된다. 이때 <mark style="color:purple;">**단일 책임 원칙**</mark>을 지켜야 하며, 이는 하나의 컴포넌트는 하나의 일을 하는 것이 이상적이라는 뜻이다. 컴포넌트가 커지게 된다면 보다 작은 컴포넌트로 분리하는 것이 맞다.

보통 JSON 데이터를 사용자에게 보여주는 경우가 많으므로, 데이터가 올바르게 구축된 경우 UI와 잘 매핑된다는 것을 확인할 수 있다. 이는 UI와 데이터가 같은 information architecture를 가지기 때문이다. 각 컴포넌트가 데이터의 한 조각을 담당하도록 UI를 분리하면 된다.

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

앱을 다섯 개의 컴포넌트로 나누어 보았다.

<mark style="background-color:yellow;">FilterableProductTable</mark>: 예시 전체를 담는 컴포넌트

```jsx
class FilterableProductTable extends React.Component {
  render() {
    return (
      <div>
        <SearchBar />
        <ProductTable products={this.props.products} />
      </div>
    );
  }
}
```

<mark style="background-color:purple;">SearchBar</mark>: user input을 받는 컴포넌트

```jsx
class SearchBar extends React.Component {
  render() {
    return (
      <form>
        <input type='text' placeholder='Search...' />
        <p>
          <input type='checkbox' />
          {' '}
          Only show products in stock
        </p>
      </form>
    );
  }
}
```

<mark style="background-color:green;">ProductTable</mark>: user input을 기반으로 데이터를 필터링해서 보여주는 컴포넌트

```jsx
class ProductTable extends React.Component {
  render() {
    const rows = [];
    let lastCategory = null;
    
    this.props.products.forEach((product) => {
      if (product.category !== lastCategory) {
        rows.push(
          <ProductCategoryRow
            category={product.category}
            key={product.category}
          />
        );
      }
      
      rows.push(
        <ProductRow
          product={product}
          key={product.name}
        />
      );
      
      lastCategory = product.category;
    });
    
    return (
      <table>
        <thead>
          <tr>
            <th>Name</th>
            <th>Price</th>
          </tr>
        </thead>
        <tbody>{rows}</tbody>
      </table>
    );
  }
}
```

<mark style="background-color:blue;">ProductCategoryRow</mark>: 각 카테고리의 헤더를 보여주는 컴포넌트

```jsx
class ProductCategoryRow extends React.Component {
  render() {
    const category = this.props.category;
    
    return (
      <tr>
        <th colSpan='2'>{category}</th>
      </tr>
    );
  }
}
```

<mark style="background-color:red;">ProductRow</mark>: 각 제품에 해당하는 행을 보여주는 컴포넌트

```jsx
class ProductRow extends React.Component {
  render() {
    const product = this.props.product;
    const name = product.stocked
      ? product.name
      : <span style={{ color: 'red' }}>{product.name}<span/>
    
    return (
      <tr>
        <td>{name}</td>
        <td>{product.price}</td>
      </tr>
    );
  }
}
```

<mark style="background-color:green;">\<ProductTable /></mark> 컴포넌트를 내부를 보면 "Name"과 "Price" label을 갖는 컴포넌트는 따로 존재하지 않는다. 이 같은 데이터를 위한 컴포넌트를 따로 만드는 것은 취향 차이이고, 정렬 기능 추가 등 보다 label 부분이 복잡해진다면 독립된 컴포넌트를 만드는 것이 합리적일 것.

이제 컴포넌트를 계층 구조로 나타내보면 다음과 같다.

```jsx
<FilterableProductTable>
  <SearchBar />
  <ProductTable>
    <ProductCategoryRow />
    <ProductRow />
  </ProductTable>
</FilterableProductTable>
```

## Step 2: Build A Static Version in React

컴포넌트 계층 구조가 만들어졌으니 앱을 실제로 구현해보자. 가장 쉬운 방법은 데이터를 가지고 UI를 렌더링을 하는 정적 버전(동작은 없는 버전)을 만들어 보는 것. state는 생략하고 props를 이용해 데이터를 전달하자.

간단한 예시는 보통 하향식(<mark style="background-color:yellow;">\<FilterableProductTable /></mark> 컴포넌트부터)으로 만드는 게 쉽지만, 대규모 프로젝트는 상향식(<mark style="background-color:red;">\<ProductRow /></mark> 컴포넌트부터)으로 만들고 테스트를 작성하면서 개발하기가 더 쉽다.

계층 구조의 최상단 <mark style="background-color:yellow;">\<FilterableProductTable /></mark> 컴포넌트는 prop으로 데이터 모델을 받는다. 데이터 모델이 변경되면 root.render()를 다시 호출해서 UI가 업데이트 된다.

```jsx
const PRODUCTS = [
  {category: 'Sporting Goods', price: '$49.99', stocked: true, name: 'Football'},
  {category: 'Sporting Goods', price: '$9.99', stocked: true, name: 'Baseball'},
  {category: 'Sporting Goods', price: '$29.99', stocked: false, name: 'Basketball'},
  {category: 'Electronics', price: '$99.99', stocked: true, name: 'iPod Touch'},
  {category: 'Electronics', price: '$399.99', stocked: false, name: 'iPhone 5'},
  {category: 'Electronics', price: '$199.99', stocked: true, name: 'Nexus 7'}
];
 
const root = ReactDOM.createRoot(document.getElementById('container'));
root.render(<FilterableProductTable products={PRODUCTS} />);
```

## Step 3: 최소한의 State 규정하기

UI를 상호작용하게 하려면 데이터를 변경할 수 있는 방법이 있어야 하는데, React에서는 이를 state를 통해 해결한다.

애플리케이션을 올바르게 만들기 위해서는 애플리케이션에서 필요로 하는 만큼만, 즉 최소한의 state만을 가져야 한다. 여기서의 핵심은 중복 배제 원칙이다.

예를 들어 Todo 리스트를 만든다면 Todo 아이템을 저장하는 배열만 유지하고, Todo 아이템의 개수를 표현하는 state를 별도로 만들지 않는다. Todo 갯수가 필요하다면 Todo 아이템 배열의 길이를 가져오면 된다.

위 예시에서는 다음과 같은 데이터를 가지고 있다.

* The original list of products
* The search text the user has entered
* The value of the checkbox
* The filtered list of products

이때 다음 질문을 통해 여기서 누가 state가 되어야 하는지 살펴보자.

1. 부모로부터 props를 통해 전달받는가? 그렇다면 state가 아니다.
2. 시간이 지나도 변하지 않는가? 그렇다면 state가 아니다.
3. 컴포넌트 내 다른 state나 props를 가지고 계산이 가능한가? 그렇다면 state가 아니다.

The original list of products는 props를 통해 전달되므로 state가 아니다. The search text와 checkbox는 시간에 따라 변하고 다른 것들로부터 계산될 수 없기 때문에 state로 볼 수 있다. 마지막으로 the filtered list of products는 search text와 value of the checkbox를 조합해서 계산할 수 있기 때문에 state가 아니다.

결과적으로 애플리케이션은 다음과 같은 state를 갖는다.

* The search text the user has entered
* The value of the checkbox

## Step 4: State 위치 규정하기

최소한의 state를 규정한 후 할 일은 어떤 컴포넌트가 state를 변경하거나 소유할지를 찾는 것.

기억해야 할 것은 React는 항상 컴포넌트 계층구조를 따라 아래로 내려가는 단방향 데이터 흐름을 따른다는 것이다. 다음 과정을 기억하면서 state의 위치를 규정해보자.

애플리케이션 각각의 state에 대해서

* state를 통해 렌더링하는 모든 컴포넌트를 찾는다.
* 계층구조 내에서 특정 state가 있어야 하는 최상위 컴포넌트인 공통 소유 컴포넌트를 찾는다.
* 보다 상위에 있는 컴포넌트가 state를 가져야 한다.
* state를 소유할 적절한 컴포넌트를 찾지 못했다면, 컴포넌트를 하나 만들어서 공통 소유 컴포넌트의 상위 계층에 추가한다.

이를 예시에 적용하면

* <mark style="background-color:green;">\<ProductTable /></mark> 컴포넌트는 state에 의존한 상품 리스트를 필터링해서 보여주어야 하고, <mark style="background-color:purple;">\<SearchBar /></mark> 컴포넌트는 text와 checkbox의 state를 보여주어야 한다.
* 공통 소유 컴포넌트는 <mark style="background-color:yellow;">\<FilterableProductTable /></mark> 컴포넌트이다.
