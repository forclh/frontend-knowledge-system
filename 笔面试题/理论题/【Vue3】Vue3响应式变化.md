# Vue2响应式回顾

> 面试题：说一说 Vue3 响应式相较于 Vue2 是否有改变？如果有，那么说一下具体有哪些改变？
> 

**观察者模式**

生活中的观察者模式：

假设顾客对新型号的手机感兴趣，但是目前商店还没到货，那么顾客及时如何买到新型号的手机？

1. 顾客每天去一趟商场 🙅
2. 商品到货后没所有顾客发出通知 🙅

![](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2024-03-22-074632.png)

我们似乎遇到了一个矛盾：要么让顾客浪费时间检查产品是否到货，要么让商店浪费资源去通知没有需求的顾客。

解决方案：其实很简单，让有需求的顾客（watcher）主动订阅即可，之后商店（dep）只需要给订阅了用户发送通知。

**Vue2响应式工作机制**

1. **data 中的数据会被 Vue 遍历（使用Object.defineProperty）生成 getter 和 setter**，这样一来当访问或设置属性时，Vue 就有机会做一些别的事情。
2. 每个组件实例都对应一个 watcher 实例，它会在组件渲染的过程中把“接触”过的数据 property 记录为依赖。之后当依赖项的 setter 触发时，会通知 watcher，从而使它关联的组件重新渲染。

![](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2024-09-01-074111.png)

几个比较重要的点：

1. 劫持数据：通过 Object.defineProperty 方法来做数据劫持，生成 getter 和 setter 从而让获取/设置值的时候可以做一些其他的事情。
2. 发布者：记录依赖，也就是数据和 watcher 之间的映射关系
3. 观察者：watcher 会被发布者记录，数据发生变化的时候，发布者会会通知 watcher，之后 watcher 执行相应的处理

**劫持数据**

劫持数据对象，是 Observer 的工作，它的目标很简单，就是把一个普通的对象转换为响应式的对象

为了实现这一点，Observer 把对象的每个属性通过 Object.defineProperty 转换为带有 getter 和 setter 的属性，这样一来，当访问或设置属性时，Vue 就有机会做一些别的事情。

![](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2024-09-01-052809.png)

Observer 是 Vue 内部的构造器，我们可以通过 Vue 提供的静态方法 Vue.observable( object ) 间接的使用该功能。

在组件生命周期中，这件事发生在 beforeCreate 之后，created 之前。

具体实现上，**它会递归遍历对象的所有属性，以完成深度的属性转换**。由于遍历时只能遍历到对象的当前属性，因此无法监测到将来动态增加或删除的属性，因此 Vue 提供了 $set 和 $delete 两个实例方法，让开发者通过这两个实例方法对已有响应式对象添加或删除属性。对于数组，Vue 会更改它的隐式原型，之所以这样做，是因为 Vue 需要监听那些可能改变数组内容的方法。

![](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2024-09-01-052949.png)

总之，Observer 的目标，就是要让一个对象，它属性的读取、赋值，内部数组的变化都要能够被 Vue 感知到。

**发布者(商店)**

发布者，也被称之为依赖管理器，对应英文 Dependency，简称 Dep.

其中最核心的两个功能：

- 能够添加观察者：当读取响应式对象的某个属性时，它会进行依赖收集
- 能够通知观察者：当改变某个属性时（商品发售了），它会派发更新（通知所有顾客）

![](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2024-09-01-053233.png)

**观察者**

当依赖的数据发生变化时，发布者会通知每一个观察者，而观察者需要调用 update 来更新数据。

**scheduler**

Vue2 内部实现中，还存在一个 Scheduler，因为Dep 通知 watcher 之后，如果 watcher 执行重运行对应的函数，就有可能导致函数频繁运行，从而导致效率低下

试想，如果一个交给 watcher 的函数，它里面用到了属性 a、b、c、d，那么 a、b、c、d 属性都会记录依赖，于是下面的代码将触发 4 次更新：

```jsx
state.a = "new data";
state.b = "new data";
state.c = "new data";
state.d = "new data";
```

这样显然是不合适的，因此，watcher 收到派发更新的通知后，实际上不是立即执行对应函数，而是把自己交给一个叫调度器的东西

调度器维护一个执行队列，该**队列同一个 watcher 仅会存在一次，队列中的 watcher 不是立即执行，它会通过一个叫做 nextTick 的工具方法，把这些需要执行的 watcher 放入到事件循环的微队列中**，也就是说，当响应式数据变化时，render 函数的执行是**异步**的，并且在**微队列**中。

**Vue2响应式整体流程**

![](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2024-09-01-053804.png)

几个核心部件：

1. Observer：用于劫持数据对象，把对象的每个属性通过 Object.defineProperty 转换为带有 getter 和 setter 的属性
2. Dep(商店)：发布者，也被称之为依赖管理器
    - 能够添加观察者：当读取响应式对象的某个属性时，它会进行依赖收集
    - 能够通知观察者：当改变某个属性时，它会派发更新
3. Watcher（顾客）：负责具体的更新操作（可以理解为用户收到商场的邮件后，自身要做什么事情）
4. Scheduler：负责调度。

---

# Vue3响应式变化

> 面试题：说一说 Vue3 响应式相较于 Vue2 是否有改变？如果有，那么说一下具体有哪些改变？
> 

**1. 变化一**

首当其冲的就是数据拦截的变化：

- Vue2: 使用 Object.defineProperty 进行拦截
- Vue3: 使用 Proxy + Object.defineProperty 进行拦截

**两者的共同点**

- 都可以针对对象成员拦截
- 都可以实现深度拦截

**两者的差异点**

- 拦截的广度
    - Object.defineProperty 是针对对象特定**属性**的**读写**操作进行拦截，这意味着之后新增加/删除的属性是侦测不到的
    - Proxy 则是针对**一整个对象**的多种操作，包括属性的读取、赋值、属性的删除、属性描述符的获取和设置、原型的查看、函数调用等行为能够进行拦截。
- 性能上的区别：在大多数场景下，Proxy 比 Object.defineProperty 效率更高，拦截方式更加灵活。

**2. 变化二**

创建响应式数据上面的变化：

- Vue2: 通过 data 来创建响应式数据
- Vue3: 通过 ref、reactvie 等方法来创建响应式数据
    - ref：使用 Object.defineProperty + Proxy 方式
    - reactive：使用 Proxy 方式

**对应源码**

```jsx
class RefImpl<T> {
  private _value: T
  private _rawValue: T

  public dep?: Dep = undefined
  public readonly __v_isRef = true

  constructor(
    value: T,
    public readonly __v_isShallow: boolean,
  ) {
    this._rawValue = __v_isShallow ? value : toRaw(value)
    // 有可能是原始值，有可能是 reactive 返回的 proxy
    this._value = __v_isShallow ? value : toReactive(value)
  }

  get value() {
    // 收集依赖 略
    return this._value
  }

  set value(newVal) {
    // 略
  }
}

// 判断是否是对象，是对象就用 reactive 来处理，否则返回原始值
export const toReactive = <T extends unknown>(value: T): T =>
  isObject(value) ? reactive(value) : value
```

```jsx
function createReactiveObject(
  target: Target,
  isReadonly: boolean,
  baseHandlers: ProxyHandler<any>,
  collectionHandlers: ProxyHandler<any>,
  proxyMap: WeakMap<Target, any>,
) {
  // ...
    
  // 创建 Proxy 代理对象
  const proxy = new Proxy(
    target,
    targetType === TargetType.COLLECTION ? collectionHandlers : baseHandlers,
  )
  proxyMap.set(target, proxy)
  return proxy
}

export function reactive(target: object) {
  // ...
  
  return createReactiveObject(
    target,
    false,
    mutableHandlers,
    mutableCollectionHandlers,
    reactiveMap,
  )
}
```

**3. 变化三**

依赖收集上面的变化：

- Vue2：Watcher + Dep
    - 每个响应式属性都有一个 Dep 实例，用于做依赖收集，内部包含了一个数组，存储依赖这个属性的所有 watcher
    - 当属性值发生变化，dep 就会通知所有的 watcher 去做更新操作
- Vue3：WeakMap + Map + Set
    - Vue3 的依赖收集粒度更细
    - WeakMap 键对应的是响应式对象，值是一个 Map，这个 Map 的键是该对象的属性，值是一个 Set，Set 里面存储了所有依赖于这个属性的 effect 函数

总结起来，Vue3相比Vue2的依赖追踪粒度更细，Vue2依赖收集收集的是具体的Watcher（组件），Vue3依赖收集收集的是对应的副作用函数。

> 面试题：说一说 Vue3 响应式相较于 Vue2 是否有改变？如果有，那么说一下具体有哪些改变？
> 
> 
> 参考答案：
> 
> 相比较 Vue2，Vue3 在响应式的实现方面有这么一些方面的改变：
> 
> 1. 数据拦截从 Object.defineProperty 改为了 Proxy + Object.defineProperty 的拦截方式，其中
>     - ref：使用 ObjectdefineProperty + Proxy 方式
>     - reactive：使用 Proxy 方式
> 2. 创建响应式数据在语法层面有了变化：
>     - Vue2: 通过 data 来创建响应式数据
>     - Vue3: 通过 ref、reactvie 等方法来创建响应式数据
> 3. 依赖收集上面的变化
>     - Vue2：Watcher + Dep
>     - Vue3：WeakMap + Map + Set
>     - 这种实现方式可以实现更细粒度的依赖追踪和更新控制

---

- EOF-