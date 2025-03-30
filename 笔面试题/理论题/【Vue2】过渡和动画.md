# 内置组件Transition

> 官网详细文档：https://v2.cn.vuejs.org/v2/guide/transitions.html

## 时机

`Transition`组件会监控`slot`中**唯一**根元素的出现和消失，并会在**其出现和消失时**应用过渡效果

具体的监听内容是：

- 它会对**新旧两个虚拟节点进行对比（diff）**，如果**旧节点被销毁，则应用消失效果，如果新节点是新增的，则应用进入效果**
- 如果不是上述情况，则它会对比新旧节点，**观察其`v-show`是否变化，`true->false`应用消失效果，`false->true`应用进入效果**

## 流程

> 类名规则：
> 
> 1. 如果`transition`上没有定义`name`，则类名为`v-xxxx`
> 2. 如果`transition`上定义了`name`，则类名为`${name}-xxxx`
> 3. 如果指定了类名，直接使用指定的类名
> 
> 指定类名见：[自定义过渡类名](https://cn.vuejs.org/v2/guide/transitions.html#%E8%87%AA%E5%AE%9A%E4%B9%89%E8%BF%87%E6%B8%A1%E7%9A%84%E7%B1%BB%E5%90%8D)
> 

**1. 进入效果**

![](http://mdrs.yuanjin.tech/img/20210309212357.png)

**2. 消失效果**

![](http://mdrs.yuanjin.tech/img/20210309213426.png)

## 过渡组

`Transision`可以监控其内部的**单个dom元素**的出现和消失，并为其附加样式
 
如果要**监控一个dom列表**，就需要使用`TransitionGroup`组件

它会对列表的新增元素应用**进入效果**，删除元素应用**消失效果**，对被移动的元素应用`v-move`样式

> 被移动的元素之所以能够实现过渡效果，是因为TransisionGroup内部使用了Flip过渡方案


## 头像切换效果（也可以做无缝轮播图）

```vue
<template>
  <div class="container">
    <div class="btns">
      <button @click="prev">prev</button>
      <button @click="next">next</button>
    </div>
    <transition
      :name="`image-${direction}`"
      enter-active-class="image-enter-active"
      leave-active-class="image-leave-active"
    >
      <img class="image" :src="curImage" :key="curIndex" />
    </transition>
  </div>
</template>

<script>
export default {
  data() {
    return {
      images: [
        "https://10.idqqimg.com/eth/ajNVdqHZLLAJib8odhz8Th2Z4Gat0axooYaxANJlaLEwTomre0hx8Y5yib6FxDZxsgiaYG1W2ETbrU/130?tp=webp",
        "https://10.idqqimg.com/eth/ajNVdqHZLLDqYf0PtFibF9JNOnRbAw7DicWPicmfRkQwPeK2mnZ7ZJzZFdsCwCWdcwhEqoVphXiaDHE/130?tp=webp",
        "https://thirdqq.qlogo.cn/g?b=sdk&k=LaERpMuX1ZjWTQmhrhst6Q&s=100&t=0&tp=webp",
        "https://10.idqqimg.com/eth/ajNVdqHZLLDXIjdTYsqbfkxiaibd3lYGEgfiaEwficYfK2ogZDicCxaKibVibGA2Cj2ltgOvCm1tbRs1iac/130?tp=webp",
        "https://thirdqq.qlogo.cn/g?b=sdk&k=pfIficic6WRliaLULZudVI5Tw&s=640&t=1600139160&tp=webp",
      ],
      curIndex: 0,
      direction: "next",
    };
  },
  computed: {
    curImage() {
      return this.images[this.curIndex];
    },
    maxIndex() {
      return this.images.length - 1;
    },
  },
  methods: {
    next() {
      this.curIndex++;
      if (this.curIndex > this.maxIndex) {
        this.curIndex = 0;
      }
      this.direction = "next";
    },
    prev() {
      this.curIndex--;
      if (this.curIndex < 0) {
        this.curIndex = this.maxIndex;
      }
      this.direction = "prev";
    },
  },
};
</script>

<style>
.container {
  text-align: center;
}
.btns button {
  margin: 1em 0.5em;
}
.image {
  position: absolute;
  top: 100px;
  width: 200px;
  height: 200px;
  border-radius: 50%;
  left: 50%;
  margin-left: -100px;
}
.image-next-enter,
.image-prev-leave-to {
  opacity: 0;
  transform: translateX(200px);
}
.image-next-leave-to,
.image-prev-enter {
  opacity: 0;
  transform: translateX(-200px);
}
.image-enter-active,
.image-leave-active {
  transition: 0.5s;
}
</style>


```


## `TransitionGroup` demo

```vue
<template>
  <div id="app">
    <button @click="addNumber">add number</button>
    <button @click="delNumber">delete number</button>
    <button @click="shuffle">shuffle</button>
    <transition-group name="nums" tag="ul">
      <li v-for="item in nums" :key="item">{{ item }}</li>
    </transition-group>
  </div>
</template>

<script>
export default {
  data() {
    return {
      nums: [1, 2, 3, 4, 5, 6],
      next: 7,
    };
  },
  methods: {
    getRandomIndex() {
      return Math.floor(Math.random() * this.nums.length);
    },
    addNumber() {
      var index = this.getRandomIndex();
      this.nums.splice(index, 0, this.next);
      this.next++;
    },
    delNumber() {
      var index = this.getRandomIndex();
      this.nums.splice(index, 1);
    },
    shuffle() {
      this.nums.sort(() => Math.random() - 0.5);
    },
  },
};
</script>

<style>
#app {
  width: 500px;
  margin: 0 auto;
}
.nums-enter,
.nums-leave-to {
  opacity: 0;
  transform: translateX(100px);
}
.nums-enter-active,
.nums-leave-active,
/* 元素移动时起作用 */
.nums-move {
  transition: 0.5s;
}

.nums-leave-active {
  position: absolute;  /* 删除的时候，需要通过改变定位来让 move 样式起作用。因此元素真正删除的时候，move样式类已经被移除了 */
}
</style>
```

## 代办事项效果

```vue
<template>
  <div class="container">
    <input
      type="text"
      @keypress.enter="addTodo"
      v-model="newTodoContent"
      class="todo-content"
      placeholder="input todo"
    />
    <button class="shuffle" @click="shuffle">随机排序</button>
    <transition-group tag="ul" name="todo" class="todo-container">
      <li v-for="item in todos" :key="item.id" class="todo">
        <span>{{ item.content }}</span>
        <button @click="finishTodo(item)">完成</button>
      </li>
    </transition-group>
  </div>
</template>

<script>
function randomId() {
  return Math.random()
    .toString(16)
    .substr(2, 5);
}

export default {
  data() {
    return {
      todos: [
        { id: randomId(), content: "完成「vue过渡和动画的」的学习" },
        { id: randomId(), content: "看一部电影" },
        { id: randomId(), content: "学一首新歌" },
      ],
      newTodoContent: "",
    };
  },
  methods: {
    addTodo() {
      if (!this.newTodoContent) {
        return;
      }
      this.todos.unshift({
        id: randomId(),
        content: this.newTodoContent,
      });
      this.newTodoContent = "";
    },
    finishTodo(item) {
      this.todos = this.todos.filter((it) => it !== item);
    },
    shuffle() {
      this.todos.sort(() => Math.random() - 0.5);
    },
  },
};
</script>

<style scoped>
.container {
  width: 600px;
  margin: 1em auto;

  padding: 1.5em;
  border-radius: 5px;
}
.shuffle {
  margin: 1em 0;
}
.todo-content {
  box-sizing: border-box;
  width: 100%;
  height: 50px;
  border-radius: 5px;
  outline: none;
  font-size: 1.3em;
  padding: 0 1em;
  border: 1px solid #ccc;
}
.todo-container {
  list-style: none;
  padding: 0;
  margin: 1em 0;
}
.todo {
  padding: 0.5em 0;
  border-bottom: 1px solid #ccc;
  display: flex;
  justify-content: space-between;
  margin-bottom: 1em;
}

.todo-enter {
  opacity: 0;
  transform: translateX(-100%);
}
.todo-enter-active,
.todo-leave-active,
.todo-move {
  transition: 0.5s;
}
.todo-leave-to {
  opacity: 0;
  transform: translateX(100%);
}
.todo-leave-active {
  position: absolute;
}
</style>


```


## `v-move`类的实现原理（Flip）

```vue

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <ul>
      <li>title 1</li>
      <li>title 2</li>
    </ul>
    <button>交换</button>

    <script>
      var btn = document.querySelector("button");

      // 当浏览器下一次渲染之后，调用回调
      function raf(callback) {
        requestAnimationFrame(() => {
          requestAnimationFrame(callback);
        });
      }

      function getY(dom) {
        return dom.getBoundingClientRect().top;
      }

      function applyTransition(dom, initY) {
        var curY = getY(dom); //记录变化后的位置
        var dis = initY - curY; //计算位置的差值
        dom.style.transform = `translateY(${dis}px)`;
        // 渲染之后
        raf(() => {
          dom.style.transition = ".5s";
          dom.style.transform = "none";
          dom.addEventListener(
            "transitionend",
            () => {
              dom.style.transition = "none";
            },
            {
              once: true,
            }
          );
        });
      }

      btn.onclick = function () {
        var ul = document.querySelector("ul");
        var li1 = document.querySelector("ul li:nth-child(1)");
        var li1Y = getY(li1); // 拿到li1的初始位置
        var li2 = document.querySelector("ul li:nth-child(2)");
        var li2Y = getY(li2); // 拿到li2的初始位置
        ul.insertBefore(li2, li1); // 没有任何过渡效果的变化

        applyTransition(li1, li1Y);
        applyTransition(li2, li2Y);
      };
    </script>
  </body>
</html>


```