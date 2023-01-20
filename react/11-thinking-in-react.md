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

컴포넌트를 구분하는 방법이 어렵다면 새로운 함수나 객체를 만든다고 생각하면 된다. 이때 <mark style="color:purple;">**단일 책임 원칙**</mark>을 지켜야 하며, 하나의 컴포넌트는 하나의 일을 하는 것이 이상적이라는 뜻이다. 하나의 컴포넌트가 커지게 된다면 보다 작은 컴포넌트로 분리하는 것이 맞다.

보통 JSON 데이터를 사용자에게 보여주는 경우가 많으므로, 데이터가 올바르게 구축된 경우 UI와 잘 매핑된다는 것을 확인할 수 있다. 이는 UI와 데이터가 같은 information architecture를 가지기 때문이다. 각 컴포넌트가 데이터의 한 조각을 담당하도록 UI를 분리하면 된다.

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

앱을 다섯 개의 컴포넌트로 나누어 보았다.

1. <mark style="background-color:yellow;">FilterableProductTable</mark>: 예시 전체를 담는 컴포넌트
2. <mark style="background-color:purple;">SearchBar</mark>: user input을 받는 컴포넌트
3. <mark style="background-color:green;">ProductTable</mark>: user input을 기반으로 데이터를 필터링해서 보여주는 컴포넌트
4. <mark style="background-color:blue;">ProductCategoryRow</mark>: 각 카테고리의 헤더를 보여주는 컴포넌트
5. <mark style="background-color:red;">ProductRow</mark>: 각 제품에 해당하는 행을 보여주는 컴포넌트

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

