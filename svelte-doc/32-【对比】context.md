## React

父组件 Father.jsx

```javascript
import { createContext, useState } from 'react';
import Child from './Child';

export const ThemeContext = createContext(null);

export default function Page() {
  const [text] = useState('hello world');
  const [theme, setTheme] = useState("dark");

  return (
    <ThemeContext.Provider
      value={{
        text,
        theme,
        setTheme,
      }}
    >
      <Child />
    </ThemeContext.Provider>
  );
}
```

子组件 Child.jsx

```javascript
import GrandSon from './GrandSon';

export default function Child() {
  return <GrandSon />;
};
```

孙组件 GrandSon.jsx

```javascript
import { useContext } from 'react';
import { ThemeContext } from './Father';

const GrandSon = () => {
  const { text, theme, setTheme } = useContext(ThemeContext);
  return (
    <div>
      <p>最外层text: {text}</p>
      <p>主题：{theme}</p>
      <button onClick={() => setTheme("dark")}>
        Dark
      </button>
      <button onClick={() => setTheme("light")}>
        Light
      </button>
    </div>
  );
};
```

## Vue

父组件 Father.vue

```html
<template>
  <Child />
</template>

<script>
  export const ContextKey = 'ThemeContext';
</script>
<script setup>
  import { ref, provide } from 'vue'
  import Child from './Child.vue';

  const text = 'hello world';
  const theme = ref('dark');

  const setTheme = (newTheme) => {
    theme.value = newTheme;
  }

  provide(ContextKey, {
    text,
    theme,
    setTheme
  })
</script>
```

子组件 Child.vue

```html
<template>
  <GrandSon />
</template>

<script setup>
  import GrandSon from './GrandSon.vue';
</script>
```

孙组件 GrandSon.vue

```html
<template>
  <div>
      <p>最外层text: {{text}}</p>
      <p>主题：{{theme}}</p>
      <button @click="setTheme('dark')">
        Dark
      </button>
      <button @click="setTheme('light')">
        Light
      </button>
    </div>
</template>

<script setup>
  import { inject } from 'vue';
  import { ContextKey } from './Father.vue';

  const { text, theme, setTheme } = inject(ContextKey);
</script>
```

## Svelte

父组件 Father.svelte

```html
<script context="module">
  export const ContextKey = 'ThemeContext'
</script>
<script>
  import { setContext } from 'svelte';
  import { writable } from 'svelte/store';
  import Child from './Child.svelte';

  let text = 'hello world';
  let theme = writable('dark');

  const setTheme = (newTheme) => {
    $theme = newTheme
  }

  setContext(ContextKey, {
    text,
    theme,
    setTheme
  })
</script>

<Child />
```

子组件 Child.svelte

```html
<script>
  import GrandSon from './GrandSon.svelte';
</script>

<GrandSon />
```

孙组件 GrandSon.svelte

```html
<script>
  import { getContext } from "svelte";
  import { ContextKey } from "./Father.svelte";
  const { text, theme, setTheme } = getContext(ContextKey);
</script>

<div>
  <p>最外层text: {text}</p>
  <p>主题：{$theme}</p>
  <button on:click={() => setTheme("dark")}>
    Dark
  </button><button
    on:click={() => setTheme("light")}
  >
    Light
  </button>
</div>

```

### 小结
总结：对比篇到这里就告一段落，相信大家经过前面一些篇章的学习，已经有所发现：一个最基本的前端UI库，应该向使用者提供xxxxx方法。
