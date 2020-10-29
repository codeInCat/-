# React相关

## React 16 生命周期的变化

   * render 阶段：纯净且没有副作用，可能会被 React 暂停、终止或重新启动。
   * pre-commit 阶段：可以读取 DOM。
   * commit 阶段：可以使用 DOM，运行副作用，安排更新。
    
   ![](https://s0.lgstatic.com/i/image/M00/5D/CF/Ciqc1F-FVn6AEtlxAAIomWwVcQU485.png)

   componentWillMount，componentWillReceiveProps, componentWillUpdate准备废除。理由：主要是16 版本 render 之前的生命周期可能会被多次执行。在 Fiber 机制下，render 阶段是允许暂停、终止和重启的。
    
   static getDerivedStateFromProps和 getSnapshotBeforeUpdate新增，用于补充上述的生命周期。
    
   getSnapshotBeforeUpdate 的返回值会作为第三个参数给到 componentDidUpdate。它的执行时机是在 render 方法之后，真实 DOM 更新之前。在这个阶段里，我们可以同时获取到更新前的真实 DOM 和更新前后的 state&props 信息。getSnapshotBeforeUpdate 要想发挥作用，离不开 componentDidUpdate 的配合。 
    
   --- 
    
## Redux

   * Store的角色是整个应用的数据存储中心，集中大部分页面需要的状态数据；
   * ActionCreators ,view 层与data层的介质；
   * Reduce ，接收action并更新Store。
    
   所以流程是 用户通过界面组件 触发ActionCreator，携带Store中的旧State与Action 流向Reducer,Reducer返回新的state，并更新界面。
    
   redux 其实也是一个发布订阅，但是 redux 可以做到数据的可预测和可回溯。
    
   react-redux 的核心组件只有两个，Provider 和 connect，Provider 存放 Redux 里 store 的数据到 context 里，通过 connect 从 context 拿数据，通过 props 传递给 connect 所包裹的组件。
    
   三大原则: 
   * 单一数据源
   * State 是只读的
   * 使用纯函数来执行修改
    
   reducer 一定要保持纯净。只要传入参数相同，返回计算得到的下一个 state 就一定相同。没有特殊情况、没有副作用，没有 API 请求、没有变量修改，单纯执行计算。
   combineReducers()实现reducer的拆分
    
    
   import { createStore } from 'redux'
   import todoApp from './reducers'
   let store = createStore(todoApp, window.STATE_FROM_SERVER)
    
   createStore() 的第二个参数是可选的, 用于设置 state 初始状态。这对开发同构应用时非常有用，服务器端 redux 应用的 state 结构可以与客户端保持一致, 那么客户端可以将从网络接收到的服务端 state 直接用于本地数据初始化。
    
    
  Provider传入state
  
  connect()通过context.state获取state 处理 mapStateToProps mapDispatchToProps
  
  compose()可以包裹Redux的多个中间件
  
  
  ---


## PureComponent Vs Component

1. 它们几乎完全相同，但是PureComponent通过prop和state的浅比较来实现shouldComponentUpdate，某些情况下可以用PureComponent提升性能



    

