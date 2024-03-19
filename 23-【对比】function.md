
有了数据，那必然要有更新数据的方法，以及平时在业务上为了服务数据更新而派生出来的一堆处理数据或者操作页面的方法，那么方法在各个UI库中应该如何定义呢？

## React
在React中，为元素标签绑定方法使用`on[事件]`的驼峰命名形式，比如点击事件`onClick`，提交事件`onSubmit`等。  

### function component

上一节我们讲解了useState()返回的参数的含义，返回数组中第一个参数表示存储值的变量，第二个参数则用于更新数据。
```javascript
import { useState } from "react";

export default function Page() {
  const [count, setCount] = useState(0);

  const updateCount = () => {
    setCount(count + 1);
  };

  return (
    <div>
      count: {count}
      <button onClick={updateCount}>+1</button>
    </div>
  );
}
```

### class component

在class component中，我们沿用上一节的例子：

```javascript
import React from 'react';

export default class Page extends React.Component {
  constructor() {
    super();
    this.state = {
      count: 0
    }
  }
  updateCount() {
    this.setState({
      count: this.state.count + 1
    })
  }
  render() {
    const { count } = this.state;
    return (
      <div>
        count: {count}
        <button onClick={() => this.updateCount()}>+1</button>
      </div>
    )
  }
}
```

可以发现，如果我们使用class component，代码量比function component多了不少。

#### this
在class component中，我们还需要时刻关注this的指向和组件内的生命周期。拿上述的代码来说，如果我们把
`onClick={() => this.updateCount()}`改成`onClick={this.updateCount}`，那你在控制台便能看到
```
Uncaught TypeError: Cannot read properties of undefined (reading 'setState')
```
这种报错。当然造成这种现象的最根本原因是javascript的this指向问题
而要解决这种问题，有几种方式，

方式一：在constructor中进行bind绑定
```diff
export default class Page extends React.Component {
  constructor() {
    super();
    this.state = {
      count: 0
    }
+   this.updateCount = this.updateCount.bind(this);
  }
  updateCount() {
    this.setState({
      count: this.state.count + 1
    })
  }
  render() {
    const { count } = this.state;
    return (
      <div>
        count: {count}
        <button onClick={this.updateCount}>+1</button>
      </div>
    )
  }
}
```

方式二：像最开始的例子中，onClick中使用箭头函数二次调用
```diff
+  <button onClick={() => this.updateCount()}>+1</button>
```

方式三：在声明方法时就使用箭头函数
```diff
+ updateCount = () => {
    this.setState({
      count: this.state.count + 1
    })
  }
```
之所以举了这么多例子，就是想说明一件事，在react的class component中使用方法有一定的心智负担。

### Event

如果我们要使用事件中的event参数
```javascript
export default function Page() {
  const handleClick = (event) => {
    console.log(event);
  }
  const handleClick2 = (event, param) => {
    console.log(event);
  }
  return <div>
    <button onClick={handleClick}>click1</button>
    <button onClick={e => handleClick2(e, 'hello')}>click2</button>
  </div>
}
```

## Vue

在Vue中，为元素标签添加事件使用`@[事件]`或`v-on:[事件]`，带`@`符号的事件绑定是对`v-on`形式的缩写。

### 3.x
```html
<template>
  <div>
    count: {{count}}
    <button @click="updateCount">+1</button>
    <button v-on:click="updateCount">+1</button>
  </div>
</template>

<script setup>
  import { ref } from 'vue';

  const count = ref(0);

  const updateCount = () => {
    count.value++;
  }
</script>
```
我们这里可以先对比下vue和react的事件绑定方式的不同，拿click方法来说，vue使用的是`@click="func"`的形式，而React则是`onClick={func}`的形式。

### 2.x
在Vue 2.x中，事件的绑定方式不变，事件的声明则在options api的methods选项中。
```html
<template>
  <div>
    <button @click="updateCount">update count</button>
    count:{{ count }}
  </div>
</template>

<script>
export default {
  data() {
    return {
      count: 0,
    };
  },
  methods: {
    updateCount() {
      this.count++;
    },
  },
};
</script>
```

### Event
```html
<template>
  <div>
    <button @click="handleClick">click1</button>
    <button @click="handleClick2($event, 'hello')">click2</button>
  </div>
</template>

<script>
export default {
  methods: {
    handleClick(event) {
      console.log(event);
    },
    handleClick2(event, param) {
      console.log(event);
    },
  },
};
</script>
```

### 事件修饰符
在事件处理程序中调用`event.preventDefault()`或`event.stopPropagation()`是非常常见的需求。Vue提供了一些事件修饰符，我们可以通过他们来优化业务代码，减少关心dom事件细节。事件修复符之间可以链式调用，但要注意调用的顺序。
```html
<a v-on:click.stop.prevent="func"></a>
```

## Svelte

在Svelte中，添加事件绑定的方式通过`on:[事件]`的方式。
```html
<script>
  let count = 0;

  const updateCount = () => {
    count++;
  }
</script>


<div>
  count: {count}
  <button on:click={updateCount}>+1</button>
</div>
```
而且我们不用考虑方法this的指向，也无需通过this来获取变量。Svelte仍旧比其他两大框架在写法上更为简洁。

### Event
```html
<script>
  const handleClick = (event) => {
    console.log(event)
  }
  const handleClick2 = (event, param) => {
    console.log(event);
  }
</script>

<button on:click={handleClick}>click1</button>
<button on:click={(e) => handleClick2(e, 'hello')}>click2</button>
```

### 事件修饰符
同样Svelte也提供了一些事件修饰符，修饰符也可以链式调用，和Vue不同的是，Svelte的事件修饰符使用`|`来引用。
```html
<a v-on:click|stop|prevent="func"></a>
```

## 小结
这一章我们了解了事件绑定在三大框架中的不同写法，同时Vue和Svelte中均提供了事件修饰符来优化我们的方法。