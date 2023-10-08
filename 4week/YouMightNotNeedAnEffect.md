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
