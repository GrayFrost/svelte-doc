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

上一节我们讲解了useState()返回的参数的含义，返回数组中第二个参数用于更新数据。

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

可以发现，如果我们使用class component，代码量比function component多了不少。而且在class component中，我们还需要时刻关注this的指向和组件内的生命周期。



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

在2.x版本中的使用变化不大。todo例子\modifiers



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

todo modifiers
