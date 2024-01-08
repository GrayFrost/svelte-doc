## React

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

## Vue

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

## Svelte

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
