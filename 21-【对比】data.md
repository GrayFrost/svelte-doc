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

## Svelte

```html
<script>
  let count = 0;
</script>

<div>变量：{ count }</div>
```
