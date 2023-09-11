### 컴포넌트에 props 전달하기

React 컴포넌트는 props를 이용해 서로 통신한다. 모든 부모 컴포넌트는 props를 줌으로써 몇몇의 정보를 자식 컴포넌트에게 전달할 수 있다. props는 HTML 속성(attribute)를 생각나게 할 수도 있지만 객체, 배열, 함수를 포함한 모든 javascript 값을 전달할 수 있다.

props를 사용하면 아래와 같이 부모 컴포넌트와 자식 컴포넌트를 독립적으로 생각할 수 있습니다.

→ 아래에서 Avatar의 person과 size에 어떤 값이 들어갈지 자식에서 Avatar에서 신경쓰지 않아도 된다.

React 컴포넌트 함수는 하나의 인자, 즉 props 객체를 받습니다.

→ 그래서 하나식 사용하고 싶으면 구조분해 할당으로 꺼내서 사용할 수 있고 함수의 인자이기 때문에 기본값을 넣어줄 수도 있다.

```jsx
import { getImageUrl } from "./utils.js";

function Avatar({ person, size }) {
  return (
    <img
      className="avatar"
      src={getImageUrl(person)}
      alt={person.name}
      width={size}
      height={size}
    />
  );
}

export default function Profile() {
  return (
    <div>
      <Avatar
        size={100}
        person={{
          name: "Katsuko Saruhashi",
          imageId: "YfeOqp2",
        }}
      />
      <Avatar
        size={80}
        person={{
          name: "Aklilu Lemma",
          imageId: "OKS67lh",
        }}
      />
      <Avatar
        size={50}
        person={{
          name: "Lin Lanying",
          imageId: "1bX5QH6",
        }}
      />
    </div>
  );
}
```

**JSX 전개 구문으로 props 전달하기**

```jsx
function Profile(props) {
  return (
    <div className="card">
      <Avatar {...props} />
    </div>
  );
}
```

컴포넌트의 props 전부를 자식 컴포넌트에 전달해야 한다면 전개 구문을 사용할 수 있다.

→ 가능하다는 이야기이지 권장되는 부분은 아니다. 실제로 props가 통째로 전달되야 한다면 설계가 잘못되었다고 생각한다.

**자식을 JSX로 전달하기**

브라우저 빌트인 태그는 중첩하는 것이 일반적이다.

컴포넌트끼리 중첩하고 싶을 수도 있다.

```jsx
<Card>
  <Avatar />
</Card>
```

JSX 태그 내에 콘텐츠를 중첩하면 부모 컴포넌트는 해당 컨텐츠를 children이라는 prop으로 받는다. 예를 들어 아래의 Card 컴포넌트는 Avatar로 설정된 children prop을 받아 이를 감싸는 div에 렌더링 한다.

Card는 children 내부에서 무엇이 렌더링되는지 알아야 할 필요가 없다.

children prop을 가지고 있는 컴포넌트가 임의로 JSX로 채울 수 있는 구멍을 가진 것이라고 생각할 수 있다. 패널 그리드 등 시각적 래퍼에 종종 children prop을 사용한다.

```jsx
import Avatar from "./Avatar.js";

function Card({ children }) {
  return <div className="card">{children}</div>;
}

export default function Profile() {
  return (
    <Card>
      <Avatar
        size={100}
        person={{
          name: "Katsuko Saruhashi",
          imageId: "YfeOqp2",
        }}
      />
    </Card>
  );
}
```

```jsx
import { FlyOut } from "./FlyOut";

export default function FlyoutMenu() {
  return (
    <FlyOut>
      <FlyOut.Toggle />
      <FlyOut.List>
        <FlyOut.Item>Edit</FlyOut.Item>
        <FlyOut.Item>Delete</FlyOut.Item>
      </FlyOut.List>
    </FlyOut>
  );
}
```

시간에 따라 props가 변하는 방식

아래의 clock 컴포넌트는 부모 컴포넌트로부터 color와 time이라는 두가지 props를 받는다.

```jsx
export default function Clock({ color, time }) {
  return <h1 style={{ color: color }}>{time}</h1>;
}
```

컴포넌트가 시간에 따라 다른 props를 받을 수 있음을 보여준다. **props는 고정되어 있지 않으며 컴포넌트의 데이터를 처음에만 반영하는 것이 아니라 모든 시점에 반영한다.**

그러나 props는 불변으로 변경할 수 없다. 컴포넌트가 props를 변경해야 하는 경우 부모 컴포넌트에 다른 props 즉 새로운 객체를 전달하도록 요청해야 한다. 그러면 이전의 props는 버려지고 가비지 컬렉터로 들어간다.

props는 변경해서는 안되며 만약 변경해야 할 경우 setState를 통해 변경해야 한다.
