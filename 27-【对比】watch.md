## React

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

## Vue

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

## Svelte

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
