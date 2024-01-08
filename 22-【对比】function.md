有了数据，那必然要有更新数据的方法，以及平时在业务上为了服务数据更新而派生出来的一堆处理数据或者操作页面的方法，那么方法在各个UI库中应该如何定义呢？

## React

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

## Vue

```html
<template>
  <div>
    count: {{count}}
    <button @click="updateCount">+1</button>
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

## Svelte

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
