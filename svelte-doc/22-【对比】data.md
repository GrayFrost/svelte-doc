## 三大框架写法对比 —— data

经过了入门篇与进阶篇的学习，相信大家对svelte都有了一定的了解。接下来我们将对比Svelte与React、Vue之间的语法差异，最后一次加强对Svelte的api的掌握程度。经过对比篇的学习之后，相信大家在脑海中都会形成“关于一个UI框架应该向使用者提供何种公共能力”的思维脑图。

从本篇开始，笔者会带领大家从多个维度认识和学习相同的功能在React、Vue和Svelte中是如何实现的。如果你已经是Vue或者React的老手，那可以跳过篇章或加强巩固；而如果你是其中一种框架的使用者想快速学习另外一种，或是毫无UI框架使用经验的新人，相信本篇章会大大提升你的学习速度。

### React

在React中，元素标签内使用变量的方式是把变量放置在 `{}`单个花括号内。

```javascript
import { useState } from "react";

export default function Page() {
  const [count] = useState(0);

  return (
    <div>变量：{count}</div>
  );
}
```

在React中声明组件有两种方式，我们称之为class component和function component
在16.8^的版本，推荐使用function component以及hooks。在function component中，使用useState来进行变量声明和更新

```javascript
const [value, setValue] = useState(null)
```

useState接收一个参数作为一个初始数据，返回一个数组，数组的第一个值表示变量，第二个值表示用于更新变量的方法。在上述例子中，我们只演示了变量的声明和使用，更新数据的方法，我们将在下一节进行展示。

而在16.8之前使用class component时，通过setState进行数据更新。

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

可以发现，如果我们使用class component，代码量比function component多了不少。

### Vue

在Vue中，元素标签内使用变量的方式是把变量放置在 `{{}}`双个花括号内。

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

在vue3 的composition api中，可以通过 `ref`或者 `reactive`来声明变量。todo ref和reactive的注意事项。如果你掌握vue3，那在面试中大概率会问到ref和reactive的区别，简单概括就是，如果你想声明一个引用类型的响应式对象，那使用reactive。而如果想把一个简单类型的对象声明成响应式，那使用ref来声明定义。然后使用ref定义的对象，在使用时需要通过 `.value`来取值。这点无疑加重了我们在使用时的心智负担。因为我们不是介绍vue的专题文章，这里就简述了解即可，待感兴趣的笔者深入探究。

在vue2.X中，变量声明在data方法中，在data()方法中，我们返回一个对象，把需要定义的变量全部申明在这个对象里。而在生命周期或声明的方法里使用数据时，则需要用 `this.数据`的方式来使用。

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

在vue中，使用v-model来进行双向绑定。

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

### Svelte

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

可以看到，变量声明在svelte中的声明相比其他两个库，写起来要简介不少，和我们平时直接使用js来声明变量无异。

在Svelte中，如果我们要使用双向绑定，则写法如下

```html
<script>
  let message = '';
</script>

<input type="text" bind:value={message} />
```

### 小结

在这一章中，我们分别对比了三个框架的声明变量的写法。知道了React在class component和function component中有不同的写法，Vue也因为版本不同提供了不同的写法。而Svelte相对其他两个框架来说，变量的声明更为简洁方便。同时我们比较了双向绑定在Vue和Svelte中的不同写法。