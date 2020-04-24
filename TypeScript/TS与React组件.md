# TS与React组件
## steps
- install
  ```js
  yarn add -D typescript@next tslib  
  // init tsconfig.json
  tsc --init
  // declare something about react
  cnpm i -D @types/react @types/react-dom react react-dom
  ```
### stateless component 无状态组件
- 写法
  ```js
  import React, { MouseEvent, SFC } from 'react';

  type Props = { onClick(e: MouseEvent<HTMLElement>): void };

  const Button: SFC<Props> = ({ onClick: handleClick, children }) => (
    <button onClick={handleClick}>{children}</button>
  );
  ```
- 在`@types/react`中已经预定义一个类型`type SFC<P>`，它也是类型`interface StatelessComponent<P>`的一个别名
### 有状态组件
