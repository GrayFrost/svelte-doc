经过了入门篇与进阶篇的学习，相信大家对svelte都有了一定的了解。接下来我们将对比svelte与react、vue之间的语法差异，最后一次加强对svelte的api的掌握程度。经过对比篇的学习之后，相信大家在脑海中都会形成“关于一个UI框架应该向使用者提供何种公共能力”的思维脑图。

从本篇开始，笔者会带领大家从多个维度认识和学习相同的功能在react、vue和svelte中是如何实现的。如果你已经是vue或者react的老手，那可以跳过篇章或加强巩固；而如果你是其中一种框架的使用者想快速学习另外一种，或是毫无UI框架使用经验的新人，相信本篇章会大大提升你的学习速度。

UI框架最突出的能力便是数据驱动视图，毫无疑问，数据是最为关键的存在。本篇我们将介绍最为基础的数据存储。

## React

```javascript
import { useState } from "react";

export default function Page() {
  const [count] = useState(0);

  return (
    <div>变量：{count}</div>
  );
}
```

在16.8^的版本，推荐使用function component以及hooks。在function component中，使用useState来进行变量声明和更新

```
const [value, setValue] = useState(null)
```

useState接收一个参数作为一个初始数据，返回一个数组，数组的第一个值表示变量，第二个值表示用于更新变量的方法。在上述例子中，我们只演示了变量的声明和使用，更新数据的方法，我们将在下一节进行展示。

而在16.8之前使用class component时，通过setState进行数据更新。

## Vue

```html
<template>
  <div>变量：{{ count }}</div>
</template>

<script setup>
import { ref } from 'vue';

const count = ref(0);
</script>
```

在vue3 的composition api中，可以通过ref或者reactive来声明变量。在vue2.X中，变量声明在data()中

## Svelte

```html
<script>
  let count = 0;
</script>

<div>变量：{ count }</div>
```

可以看到，变量声明在svelte中的声明相比其他两个库，写起来要简介不少。
