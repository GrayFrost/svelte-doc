## 计算属性
所谓“计算属性”，是指某些变量依赖于其他变量而更新，当其依赖的变量更新时，这些计算属性才会执行更新。比如设置一个area为计算属性，它依赖于width和height两个变量，当width和height其中一个变量发生变化时，area也会响应式地进行自身的更新。

### React

```javascript
import { useState, useMemo } from "react";

export default function Page() {
  const [count, setCount] = useState(0);

  const doubleCount = useMemo(() => {
    return count * 2;
  }, [count]);

  const updateCount = () => {
    setCount(count + 1);
  };

  return (
    <section>
      <button onClick={updateCount}>+1</button>
      <span>
        {count}的2倍是：{doubleCount}
      </span>
    </section>
  );
}

```
React中可以使用`useMemo`这个hooks来实现计算属性。`useMemo(() => {}, [])`，在第二个数组中添加需要监听的依赖，执行useMemo得到一个计算属性。当数组中的变量发生更新，计算属性也会重新计算。

### Vue

在Vue 3.x的composition api中，使用`computed()`来实现计算属性：
```html
<template>
  <section>
    <button @click="updateCount">+1</button>
    <span>
      {{count}}的2倍是：{{doubleCount}}
    </span>
  </section>
</template>

<script setup>
import { ref, computed } from 'vue';

let count = ref(0);

let doubleCount = computed(() => {
  return count.value * 2
});

const updateCount = () => {
  count.value++;
}
</script>
```

在Vue 2.x的options api中，把计算属性统一放在`computed: {}`中：
```html
<template>
  <section>
    <button @click="updateCount">+1</button>
    <span> {{ count }}的2倍是：{{ doubleCount }} </span>
  </section>
</template>

<script>
export default {
  data() {
    return {
      count: 0,
    };
  },
  computed: {
    doubleCount() {
      return this.count * 2;
    },
  },
  methods: {
    updateCount() {
      this.count++;
    },
  },
};
</script>
```

### Svelte

```html
<script>
  let count = 0;

  $: doubleCount = count * 2;

  const updateCount = () => {
    count++;
  };
</script>

<section>
  <h1>第六章 —— computed</h1>
  <button on:click={updateCount}>+1</button>
  <span>
    {count}的2倍是：{doubleCount}
  </span>
</section>

```

Svelte中，把需要设置成计算属性的变量放入`$:`中。  

计算属性只是一种便捷的响应式地更改数据的方式，我们当然也可以在监听响应事件时对这些数据进行更改，只是那样就稍微有点多余了。下面和大家一起了解下响应事件。

## 响应事件

对于一些比较简单的变量，可以通过简单的表达式将它们转变为计算属性。然而有时，我们需要监听这些变量的改变，不是为了得到一个计算属性，而是想进行更为复杂的操作，那这时我们就需要有能够监听到变量改变就执行的方法，笔者暂时称其为响应事件。

### React

```javascript
import { useEffect, useState } from 'react';

export default function Page() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log('count改变了');
  }, [count]);

  const updateCount = () => {
    setCount(count + 1);
  }

  return (
    <section>
      <button onClick={updateCount}>+1</button>
      <span>count: {count}</span>
    </section>
  )
}
```
在React中，`useEffect(() => {}, [])`有多种功能，当我们不在第二个数组参数里添加任何依赖时，它能够代替mount生命周期来调用，当我们在数组里添加变量时，`useEffect`会监听这些变量的更新，从而执行第一个参数里的函数逻辑。

### Vue

在Vue 3.x中，使用`watch()`监听依赖变更，从而执行相应回调：
```html
<template>
  <section>
    <button @click="updateCount">+1</button>
    <span>count: {{count}}</span>
  </section>
</template>

<script setup>
import { watch, ref } from "vue";

let count = ref(0);

const updateCount = () => {
  count.value++;
};

watch(
  () => count.value,
  () => {
    console.log("count改变了");
  }
);
</script>
```

在Vue 2.x中，使用`watch: {}`来监听变量的更新：
```html
<template>
  <section>
    <button @click="updateCount">+1</button>
    <span>count: {{ count }}</span>
  </section>
</template>

<script>
export default {
  data() {
    return {
      count: 0,
    };
  },
  watch: {
    count(value) {
      console.log("count改变了");
    },
  },
  methods: {
    updateCount() {
      this.count++;
    },
  },
};
</script>
```

### Svelte

```html
<script>
  let count = 0;
  let oldCount = count;
  
  $: if (count !== oldCount) {
    console.log('count改变了');
    oldCount = count;
  }

  const updateCount = () => {
    count++;
  }
</script>

<section>
  <button on:click={updateCount}>+1</button>
  <span>count: {count}</span>
</section>
```

在计算属性中我们提到，可以把一个变量放到`$:`中，通过在里面执行表达式而把这个变量转变成计算属性，其实我们也可以把这个变量放置在`$:`之外，然后在`$:`中监听变量更新来操作。
`$: area = width * height;`其实等价于：
```javascript
let area;
$: {
	area = width * height
}
```

不错，`$:`除了能执行简单的表达式，还能执行复杂的函数逻辑，如：
```javascript
$: if () {}

$: {}
```
我们要确保需要监听的变量在`$: {}`或`$: if()`内。

## 小结

本章我们对比了：
- React可以使用`useMemo`来实现计算属性，通过`useEffect`来实现监听变量后的操作。
- Vue通过`computed`来实现计算属性，通过`watch`来实现监听变量后的操作。
- Svelte通过`$`来实现计算属性，通过`$: {}`来实现监听变量的操作。