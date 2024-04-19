
经过了入门篇与进阶篇的学习，相信大家对Svelte都有了一定的了解。接下来我们将对比Svelte与React、Vue之间的语法差异，最后一次加强对Svelte的api的掌握程度。经过对比篇的学习之后，相信大家在脑海中都会形成“关于一个UI框架应该向使用者提供何种公共能力”的思维脑图。

从本篇开始，笔者会带领大家从多个维度认识和学习相同的功能在React、Vue和Svelte中是如何实现的。如果你已经是Vue或者React的老手，那可以跳过篇章或加强巩固；而如果你是其中一种框架的使用者想快速学习另外一种，或是毫无UI框架使用经验的新人，相信本篇章会大大提升你的学习速度。

## React

在React中，使用的是[jsx](https://legacy.reactjs.org/docs/introducing-jsx.html)的语法。
元素标签内使用变量的方式是把变量放置在`{}`单个花括号内。

### function component

在React中声明组件有两种方式，我们称之为`class component`和`function component`。
在16.8^的版本，推荐使用`function component`以及`hooks`。在`function component`中，使用`useState`来进行变量声明和更新。UI展示内容写在页面函数的`return`当中。

```javascript
const [value, setValue] = useState(null)
```

具体如下：
```javascript
import { useState } from "react";

export default function Page() {
  const [count] = useState(0);

  return (
    <div>变量：{count}</div>
  );
}
```

`useState`接收一个参数作为一个初始数据，返回一个数组，数组的第一个值表示变量，第二个值表示用于更新变量的方法。在上述例子中，我们只演示了变量的声明和使用，更新数据的方法，我们将在下一节进行展示。

### class component
而在16.8之前使用`class component`时，通过`this.state`来存储、获取数据，通过`setState`来进行数据更新。UI展示内容写在组件类的`render`方法的`return`当中。

```javascript
import React from 'react';

export default class Page extends React.Component {
  constructor() {
    super();
    this.state = {
      count: 0
    }
  }
  render() {
    const { count } = this.state;
    return (
      <div>
        count: {count}
      </div>
    )
  }
}
```

可以发现，如果我们使用`class component`，代码量比`function component`多了不少。

## Vue

Vue使用的是[sfc](https://cn.vuejs.org/guide/scaling-up/sfc)的文件组织方式。html、css、js分块管理。html内容写在`<template></template>`内，css写在`<style></style>`内，js写在`<script></script>`内。

在Vue中，元素标签内使用变量的方式是把变量放置在 `{{}}`双个花括号内。

### 3.x

```html
<template>
  <div>变量：{{ count }}</div>
</template>

<script setup>
import { ref, reactive} from 'vue';

const count = ref(0);
const obj = reactive({ a: 'b' });
console.log(count.value, obj);
</script>
```

在Vue 3.x的composition api中，可以通过`ref`或者`reactive`来声明变量。如果你掌握vue3，那在面试中大概率会问到`ref`和`reactive`的区别，简单概括就是，如果你想声明一个引用类型的响应式对象，那使用`reactive`。而如果想把一个简单类型的对象声明成响应式，那使用ref来声明定义。
使用`ref`定义的对象，要求我们在使用时需要通过 `.value`来取值。这点无疑加重了我们在使用时的心智负担。因为我们不是介绍Vue的专题文章，这里就简述了解即可，待感兴趣的读者深入探究。

### 2.x
在Vue 2.x中，变量声明在data()方法中，在data()方法中，我们返回一个对象，把需要定义的变量全部申明在这个对象里。而在生命周期或声明的方法里使用数据时，则需要用 `this.数据`的方式来使用。

```html
<template>
  <div></div>
</template>

<script>
export default {
  data() {
    return {
      count: 0
    }
  },
  mounted() {
    console.log(this.count)
  }
}
</script>
```

### 双向绑定
在vue中，使用`v-model`来进行双向绑定。

```html
<template>
  <input type="text" v-model="message" />
</template>

<script>
export default {
  data() {
    return {
      message: ''
    };
  },
};
</script>
```

## Svelte

Svelte的文件内容同样使用sfc的组织方式。

Svelte元素标签中使用变量的方式和React相同，使用单个花括号的形式。当然以上这些框架内的mustache标签内使用的变量都是简单类型，如果是引用类型，那么需要使用者进行解构。

```html
<script>
  let count = 0;
  let arr = [];
  let obj = {a: 'a'};
</script>

<div>变量：{ count }</div>
<div>{ obj }</div> // 错误
<div>{ obj.a }</div> // 正确
```

可以看到，变量声明在Svelte中的声明相比其他两个库，写起来要简介不少，和我们平时直接使用js来声明变量无异。

### 双向绑定
在Svelte中，如果我们要使用双向绑定，则写法如下：

```html
<script>
  let message = '';
</script>

<input type="text" bind:value={message} />
```

## 小结

本章我们对比了：
- React在`function component`中可以通过`useState()`来声明和更新数据，而在`class component`中则通过`this.state`来实现。变量在`{}`单花括号中展示。
- Vue在`3.x`中通过`ref`和`reactive`来声明变量，在`2.x`中通过`data() { return {}}`来声明存储变量。变量在`{{}}`双花括号中展示。
- Svelte中像正常js般声明变量即可。变量也是在`{}`单花括号中展示。
- Vue的双向数据绑定使用`v-model`来实现
- Svelte的双向数据绑定使用`bind:`来实现。
