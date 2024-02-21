## React

```javascript
import { useState } from "react";

export default function Page() {
  const [list] = useState([1, 2, 3]);

  return (
    <ul>
      {list.map((item, index) => {
        return <li key={index}>item - {item}</li>;
      })}
    </ul>
  );
}

```

## Vue

```html
<template>
  <ul>
    <li v-for="(item, index) in list" :key="index">item - {{ item }}</li>
  </ul>
</template>

<script setup>
import { reactive } from "vue";

const list = reactive([1, 2, 3]);
</script>

```

## Svelte

```html
<script>
  const list = [1,2,3];
</script>

<ul>
  {#each list as item, index}
    <li>item - {item}</li>
  {/each}
</ul>
```
