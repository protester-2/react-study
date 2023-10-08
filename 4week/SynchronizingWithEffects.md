## Effect와 동기화하기

리액트에서는 Effect를 사용하여 외부 시스템과 컴포넌트를 동기화할 수 있다.

### Effect란 무엇이며 이벤트와는 어떤게 다른가요?

Effect를 사용하면 특정 이벤트가 아닌 렌더링 자체로 인해 발생하는 사이드 이펙트를 명시할 수 있다.

Effect는 화면 업데이트 후 커밋이 끝날 때 실행된다. 이 때가 React 컴포넌트를 일부 외부 시스템과 동기화하기에 좋은 시기이다.

### Effect가 필요하지 않을 수도 있습니다.

일반적으로 Effect는 React 코드에서 벗어나 일부 외부 시스템과 동기화하는데 사용된다. 즉 Effect가 다른 state를 기반으로 일부 state만을 조정하는 경우 Effect가 필요하지 않을 수도 있다.

### Effect 작성 방법

1. Effect 선언, 기본적으로 Effect는 모든 렌더링 후에 실행된다.

   ```jsx
   import { useEffect } from "react";

   function MyComponent() {
     useEffect(() => {
       // Code here will run after *every* render
       // 여기의 코드는 매 렌더링 후에 실행됩니다.
     });
     return <div />;
   }
   ```

   컴포넌트가 렌더링될 때마다 react는 화면을 업데이트하고 useEffect 내부 코드를 실행한다. 즉 useEffect는 해당 렌더링이 화면에 반영이 될 때까지 코드 조각의 실행을 지연한다.

2. Effect의 의존성을 명시, 대부분의 Effect는 렌더링할 때마다가 아닌 필요할 때만 다시 실행해야 한다. 이를 위해 의존성을 지정하여 제어해야 한다.

   ```jsx
   useEffect(() => {
     // ...
   }, [state]);
   ```

   useEffect는 두번째 인자로 의존성 배열을 받는다. 의존성 배열에 값이 있을 경우 이전 렌더링 때와 같으면 Effect를 다시 실행하지 않는다.

   의존성 배열은 여러 개의 의존성을 포함할 수 있으며 React는 모든 의존성의 값이 이전 렌더링 때와 정확히 동일한 경우에만 Effect의 재실행을 건너뛴다.

   (React는 `[Object.is](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is)` (==)비교를 사용해 의존성 값을 비교한다.)

   ```jsx
   useEffect(() => {
     // This runs after every render
     // 렌더시마다 실행됩니다.
   });

   useEffect(() => {
     // This runs only on mount (when the component appears)
     // 오직 마운트시(컴포넌트가 나타날 때)에만 실행됩니다.
   }, []);

   useEffect(() => {
     // This runs on mount *and also* if either a or b have changed since the last render
     // 마운트시 뿐만 아니라 a 또는 b가 직전 렌더와 달라졌을 때에도 실행됩니다.
   }, [a, b]);
   ```

3. 필요한 경우 클린업을 추가한다.

   ex. connent ↔ disconnect, subscribe ↔ unsubscribe

   ```jsx
   import { useEffect } from "react";
   import { createConnection } from "./chat.js";

   export default function ChatRoom() {
     useEffect(() => {
       const connection = createConnection();
       connection.connect();
     }, []);
     return <h1>Welcome to the chat!</h1>;
   }
   ```

   위와 같은 컴포넌트가 있을 때 컴포넌트가 마운트되면 connection.connect()를 호출한다. 그런 다음 컴포넌트가 다시 마운트되면 두번째 연결이 설정되지만 처음에 연결되었던 것은 끊기지 않는다. 이러한 경우 문제가 발생한다.

   ```jsx
   useEffect(() => {
     const connection = createConnection();
     connection.connect();
     return () => {
       connection.disconnect();
     };
   }, []);
   ```

   이러한 문제를 개발단계에서 발견하기 위해 사용하는 것이 strict mode!

   Effect가 무언가를 구독하는 경우 클린업 함수는 구독을 취소해야 한다.

   ```jsx
   useEffect(() => {
     function handleScroll(e) {
       console.log(window.scrollX, window.scrollY);
     }
     window.addEventListener("scroll", handleScroll);
     return () => window.removeEventListener("scroll", handleScroll);
   }, []);
   ```

   Effect가 무언가를 애니메이션하는 경우 클린업 함수는 애니메이션을 초기값으로 설정해야 한다

   ```jsx
   useEffect(() => {
     const node = ref.current;
     node.style.opacity = 1; // Trigger the animation
     // 애니메이션 촉발
     return () => {
       node.style.opacity = 0; // Reset to the initial value
       // 초기값으로 재설정
     };
   }, []);
   ```

   Effect가 무언가를 fetch하면 클린업 함수는 페치를 중단하거나 그 결과를 무시해야 한다.

   ```jsx
   useEffect(() => {
     let ignore = false;

     async function startFetching() {
       const json = await fetchTodos(userId);
       if (!ignore) {
         setTodos(json);
       }
     }

     startFetching();

     return () => {
       ignore = true;
     };
   }, [userId]);
   ```

   **Deep Dive : Effect에서 데이터를 페칭하는 것의 대안**

   - Effect는 서버에서 실행되지 않는다.
   - Effect에서 직접 fetch할 경우 네트워크 워터폴이 만들어지기 쉽다.
   - Effect에서 직접 페치하는 것은 일반적으로 데이터를 미리 로드하거나 캐시하지 않음을 의미한다.

   → 위와 같은 이유로 Effect에서 데이터를 페칭하는 것이 아닌 프레임워크를 사용할 수 있다.

   [React Query](https://tanstack.com/query/latest), [useSWR](https://swr.vercel.app/), [React Router 6.4+.](https://beta.reactrouter.com/en/main/start/overview)

   ## Effect가 필요하지 않을 수도 있습니다.

   외부 시스템이 관여하지 않는 경우 Effect는 필요하지 않을 수 있다. 불필요한 Effect를 제거하면 코드를 더 쉽게 따라갈 수 이고 실행 속도가 빨라지며 오류 발생 가능성이 줄어든다.

   ### 불필요한 Effect를 제거하는 방법

   - 렌더링을 위해 데이터를 변환하는 경우 Effect는 필요하지 않다.
     → props 또는 state에 따라 state 업데이트하기
     → 고비용 계산 캐싱하기
     ```jsx
     import { useMemo, useState } from "react";

     function TodoList({ todos, filter }) {
       const [newTodo, setNewTodo] = useState("");
       // ✅ Does not re-run getFilteredTodos() unless todos or filter change
       // ✅ todos나 filter가 변하지 않는 한 getFilteredTodos()가 재실행되지 않음
       const visibleTodos = useMemo(
         () => getFilteredTodos(todos, filter),
         [todos, filter]
       );
       // ...
     }
     ```
   - 사용자 이벤트를 처리하는 데 Effect는 필요하지 않다.(이벤트 핸들러에서 처리)

   - 전체 컴포넌트 트리의 state를 재설정하려면 다른 `key`를 전달하세요.
   - prop 변경에 대한 응답으로 특정 state 일부를 조정하려면 렌더링 중에 설정하세요.
   - 컴포넌트가 *표시*되었기 때문에 실행해야 하는 코드는 Effect에 있어야 하고, 나머지는 이벤트에 있어야 합니다.
   - 여러 컴포넌트의 state를 업데이트해야 하는 경우 단일 이벤트에서 처리하는 것이 좋습니다.
   - 여러 컴포넌트에서 state 변수를 동기화하려고 할 때마다 state 끌어올리기를 고려하세요.
   - Effect로 데이터를 페치할 수 있지만, 경쟁 조건을 피하기 위해 클린업 로직을 구현해야 합니다.
