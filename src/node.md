# NodeJS相关

  Node.js 是 Javascript 在服务端的运行环境，构建在 chrome 的 V8 引擎之上，基于事件驱动、非阻塞I/O模型，充分利用操作系统提供的异步 I/O 进行多任务的执行，适合于 I/O 密集型的应用场景，因为异步，程序无需阻塞等待结果返回，而是基于回调通知的机制，原本同步模式等待的时间，则可以用来处理其它任务，

## 多进程和多线程
  进程是资源分配的最小单位，线程是CPU调度的最小单位  一个线程只能隶属于一个进程，但是一个进程是可以拥有多个线程的。
  单线程就是一个进程只开一个线程。

  Node.js 虽然是单线程模型，但是其基于事件驱动、异步非阻塞模式，可以应用于高并发场景，避免了线程创建、线程之间上下文切换所产生的资源开销。
  当你的项目中需要有大量计算，CPU 耗时的操作时候，要注意考虑开启多进程来完成了。
  Node.js 开发过程中，错误会引起整个应用退出，应用的健壮性值得考验，尤其是错误的异常抛出，以及进程守护是必须要做的。
  单线程无法利用多核CPU，但是后来Node.js 提供的API以及一些第三方工具相应都得到了解决，文章后面都会讲到。

  在单核 CPU 系统之上我们采用 单进程 + 单线程 的模式来开发。在多核 CPU 系统之上，可以通过 child_process.fork 开启多个进程（Node.js 在 v0.8 版本之后新增了Cluster 来实现多进程架构） ，即 多进程 + 单线程 模式。注意：开启多进程不是为了解决高并发，主要是解决了单进程模式下 Node.js CPU 利用率不足的情况，充分利用多核 CPU 的性能。

  如果多个Node进程监听同一个端口时会出现 Error:listen EADDRIUNS的错误，而cluster模块为什么可以让多个子进程监听同一个端口呢?
  原因是master进程内部启动了一个TCP服务器，而真正监听端口的只有这个服务器，当来自前端的请求触发服务器的connection事件后，master会将对应的socket具柄发送给子进程。

  无论是 child_process 模块还是 cluster 模块，为了解决 Node.js 实例单线程运行，无法利用多核 CPU 的问题而出现的。核心就是父进程（即 master 进程）负责监听端口，接收到新的请求后将其分发给下面的 worker 进程。


https://github.com/ElemeFE/node-interview/tree/master/sections/zh-cn


## 浏览器与Node的事件循环(Event Loop)有何区别?

  nodejs libuv事件循环?
  ![](https://image.fundebug.com/2019-01-14-005.png)
  timers 阶段：这个阶段执行 timer（setTimeout、setInterval）的回调
  I/O callbacks 阶段：处理一些上一轮循环中的少数未执行的 I/O 回调
  idle, prepare 阶段：仅 node 内部使用
  poll 阶段：获取新的 I/O 事件, 适当的条件下 node 将阻塞在这里
  check 阶段：执行 setImmediate() 的回调
  close callbacks 阶段：执行 socket 的 close 事件回调 

  process.nextTick其实是独立于 Event Loop 之外的，它有一个自己的队列，当每个阶段完成后，如果存在 nextTick 队列，就会清空队列中的所有回调函数，并且优先于其他 microtask 执行。

  浏览器的事件循环?
  ![](https://image.fundebug.com/2019-01-14-002.png)
  当某个宏任务执行完后,会查看是否有微任务队列。如果有，先执行微任务队列中的所有任务，如果没有，会读取宏任务队列中排在最前的任务，执行宏任务的过程中，遇到微任务，依次加入微任务队列。栈空后，再次读取微任务队列里的任务，依次类推。


  Node 与浏览器的 Event Loop 差异?
  浏览器环境下，microtask 的任务队列是每个 macrotask 执行完之后执行。而在 Node.js 中，microtask 会在事件循环的各个阶段之间执行，也就是一个阶段执行完毕，就会去执行 microtask 队列的任务。