# 컴포넌트 import 및 export

## 요약
Root 컴포넌트가 있어야한다.

컴포넌트를 import 하고 export 하는데 방법이 두가지가 있다. 
1. default export
2. named export

### default export vs named export
```js
// default export
// 한 파일에서는 하나의 default export만 존재할 수 있다
export default function myFunc() {
    console.log('hello');
}

// 여기에서 import 에서 다른 이름을 사용할 수 있다. 
import myFunc from './myFunc';

// named export
// 여러 개가 존재할 수 있다.
export function myFunc() {
    console.log('hello');
}

// 여기에서 import 에서는 같은 이름을 사용해야 한다.
import { myFunc } from './myFunc';

```