# React

### 生命周期

#### 初始化
```
constructor -> componentWillMount -> render -> componentDidMount
```

#### 更新
```
(componentWillReceivedProps / state change) -> shouldComponentUpdate(true) -> componentWillUpdate -> render -> componentDidUpdate
```

---

### 高阶组件

#### 属性代理（Props Proxy）
```js
// 无状态
function HigherOrderComponent(WrappedComponent) {
    return props => <WrappedComponent {...props} />;
}
```
```js
// 有状态
function HigherOrderComponent(WrappedComponent) {
    return class extends React.Component {
        render() {
            return <WrappedComponent {...this.props} />;
        }
    };
}
```

作用
- 操作 props
- 抽离 state
- 通过 ref 访问到组件实例
- 用其他元素包裹传入的组件 WrappedComponent

#### 反向继承（Inheritance Inversion）
```js
function HigherOrderComponent(WrappedComponent) {
    return class extends WrappedComponent {
        render() {
            return super.render();
        }
    };
}
```

作用
- 操作 state
- 渲染劫持（Render Highjacking

#### 高阶组件存在的问题
- 静态方法丢失
- refs 属性不能透传（React.forwardRef 可解决）
- `反向继承`不能保证完整的子组件树被解析

#### 总结
- 高阶组件 `不是组件`，是 一个把某个组件转换成另一个组件的 `函数`
- 高阶组件的主要作用是 `代码复用`
- 高阶组件是 `装饰器模式在 React 中的实现`

---

### setState更新流程

- 在React可控的范围内，setState会把值放入队列中，全部收集完后，进行一次`更新State`举动，回调函数在更新state后立即执行。
- 在Promise里或者setTimeout等React无法掌控的地方，setState立即执行`更新State`举动，回调函数也是立即执行。

#### 相关文章
- [React从渲染原理到性能优化（二）-- 更新渲染](https://www.cnblogs.com/chaoyuehedy/p/9638848.html)
- [react的setstate原理](https://www.jianshu.com/p/89a04c132270)

---

### Hooks
在function component中，hook每次渲染都会执行，但react会保留state的值，effect每次也都会执行，所以需要return一个函数用于清除上一个effect。

#### 解决的问题
- 在组件之间复用状态逻辑很难
- 复杂组件变得难以理解（Hook 将组件中相互关联的部分拆分成更小的函数）
- 难以理解的 class（很多时候不需要用到class，但是需要用到state或一些事件处理，就无法处理了）

#### State Hook
```js
import React, { useState } from 'react';

function Example() {
  // 声明一个新的叫做 “count” 的 state 变量
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

#### Effect Hook
```js
import React, { useState, useEffect } from 'react';

function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    // Specify how to clean up after this effect:
    return function cleanup() {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  if (isOnline === null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}
```

---

### React事件 

考虑到浏览器的兼容性和性能问题，react拥有自己的事件系统，即合成事件。在下文中将深入了解react事件系统。

与原生事件直接在元素上注册的方式不同的是，react的合成事件不会直接绑定到目标dom节点上，而是利用`事件委托`绑定到最外层`document`上，用一个统一的监听器去监听，这个监听器上保存着目标节点与事件对象的映射，当你挂载或删除节点时，所对应的事件就会从映射表上绑定或删除。

在react中，`合成事件被调用后`，`合成事件对象`会被重用，所有`属性被置为null`
#### 相关文章
[React事件系统详解](https://www.jianshu.com/p/84505fbcf18c)

---

### Diff算法
- 只对同层级元素进行比较
- 元素有插入，移动，删除三个操作
- 增加key可以避免元素做一些没必要的删除插入