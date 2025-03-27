> 面试题：请你阐述一下对vue虚拟DOM的理解

## 什么是虚拟DOM？

虚拟DOM本质上就是一个**普通的JS对象**，用于**描述视图的界面结构**

```vue
<script>
export default {
	mounted() {
		console.log(this._vnode)
	}
}
</script>
```

在vue中，每个组件都有一个`render`函数，每个`render`函数都会返回一个虚拟DOM树，这也就意味着每个组件都对应一棵虚拟DOM树

```vue
<script>
import ComponentA from "./components/ComponentA";
import ComponentB from "./components/ComponentB";
export default {
  components: {
    ComponentA,
    ComponentB,
  },

  render(h) {
    return h("div", [h(ComponentA), h(ComponentB)]);
  },
};
</script>
```

**在`vue-cli`中，如果存在`template`标签，则会覆盖`render`函数**

![](http://mdrs.yuanjin.tech/img/20210225140726.png)


## 为什么需要虚拟DOM？

在`vue`中，渲染视图会调用`render`函数，这种渲染不仅发生在组件创建时，同时发生在视图依赖的数据更新时。如果在渲染时，直接使用真实`DOM`，**由于真实`DOM`的创建、更新、插入等操作会带来大量的性能损耗，从而就会极大的降低渲染效率。**

因此，`vue`在渲染时，**使用虚拟DOM来替代真实DOM，主要为解决渲染效率**的问题。


## 虚拟DOM是如何转换为真实DOM的？

在一个组件实例首次被渲染时，通过render函数它先生成虚拟DOM树，然后根据虚拟DOM树创建真实DOM，并把真实DOM挂载到页面中合适的位置，此时，每个虚拟DOM便会对应一个真实的DOM。（Vue第一次渲染的效率更低，多了一个生成虚拟DOM的步骤）
 
如果一个组件受响应式数据变化的影响，需要重新渲染时，它**仍然会重新调用render函数，创建出一个新的虚拟DOM树，用新树和旧树对比**，通过对比，vue会找到最小更新量，然后直接在新树上更新必要的真实DOM节点（patch）。
  
这样一来，就保证了对真实DOM达到最小的改动。
  
 ![](http://mdrs.yuanjin.tech/img/20210225144108.png)

 
## 模板和虚拟DOM的关系
  
  vue框架中有一个`compile`模块，它主要负责将模板转换为`render`函数，而`render`函数调用后将得到虚拟DOM。
  
  编译的过程分两步：
 
 1. 将**模板字符串**转换成为`AST`（抽象语法树）
 2. 将`AST`转换为`render`函数

 如果使用传统的引入方式，则**编译时间发生在组件第一次加载**时，这称之为**运行时编译**。

```html
<script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
```

 如果是在`vue-cli`的**默认配置**下，**编译发生在打包时，这称之为模板预编译**。


编译是一个极其耗费性能的操作，预编译可以有效的提高运行时的性能，而且，由于运行的时候已不需要编译，`vue-cli`在打包时会排除掉`vue`中的`compile`模块，以减少打包体积
 
 模板的存在，仅仅是为了让开发人员更加方便的书写界面代码
  
  **vue最终运行的时候，最终需要的是render函数，而不是模板，因此，模板中的各种语法，在虚拟DOM中都是不存在的，它们都会变成虚拟DOM的配置**