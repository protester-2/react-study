### 목록 렌더링

데이터 모음에서 유사한 컴포넌트를 여러 개 표시하고 싶을 때가 많다. JavaScript 배열 메서드를 사용해 데이터 배열을 조작할 수 있다. 이 페이지에서는 React에서 filter()와 map()을 사용해 데이터 배열을 필터링하고 컴포넌트 배열로 변환한다.

**배열에서 데이터 렌더링하기**

서로 다른 데이터를 사용하여 동일한 컴포넌트의 여러 인스턴스로 표시해야 할 경우 map()과 filter() 메서드를 사용하여 컴포넌트 목록을 렌더링할 수 있다.

```jsx
const people = [
  "Creola Katherine Johnson: mathematician",
  "Mario José Molina-Pasquel Henríquez: chemist",
  "Mohammad Abdus Salam: physicist",
  "Percy Lavon Julian: chemist",
  "Subrahmanyan Chandrasekhar: astrophysicist",
];

export default function List() {
  const listItems = people.map((person) => <li key={person.id}>{person}</li>);
  return <ul>{listItems}</ul>;
}
```

**key로 목록의 항목 순서 유지하기**

이때 각 배열 항목에는 해당 배열의 항목들 사이에서 **고유하게 식별할 수 있는 문자열 또는 숫자인 key**를 부여해야 한다.

컴포넌트에 key를 props로 전달하는 것처럼 보일 수 있지만 그렇지 않으며 react 자체에서 힌트로만 사용된다.

이때 key는 각 컴포넌트가 어떤 배열 항목에 해당하는지 react에 알려주어 나중에 매칭할 수 있도록 한다.

잘 만들어진 key는 react가 정확히 무슨 일이 일어났는지 추론하고 DOM 트리를 올바르게 업데이트하는 데 도움이 된다.

key가 없다면?

→ 변경이 필요하지 않은 리스트의 요소까지 변경이 일어나게 되므로 비효율적이다.

key가 있으면?

→ 배열이 업데이트될 때마다 변경되지 않은 값들은 그대로 두고 원하는 내용을 삽입하거나 삭제할 수 있다.

key의 올바른 사용법

- 형제간에 고유해야 하며 변경되지 않아야 한다.(ex. uuid)
- 배열의 index는 배열이 삽입되거나 삭제될 때와 같이 항목의 순서가 바뀔때마다 변경되므로 key로 사용하기에 적합하지 않다.
- math.random()과 같이 즉석으로 key를 생성하는 것도 좋지 않다. 렌더링시마다 key가 일치하지 않아 매번 모든 컴포넌트와 DOM이 다시 생성될 수 있다. 이 경우 속도가 느려질 뿐 아니라 목록 내부의 사용자 입력도 손실된다.

하지만 다음과 같은 경우, key로 인덱스를 사용할 수 있다.

- 리스트와 아이템이 고정되어, 다시 계산되거나 수정되지 않는다
- 리스트의 아이템에 id가 없다
- 리스트가 재배치되거나 필터되지 않는다
