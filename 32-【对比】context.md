
我们在本章对比一下三大框架的跨组件传值能力。
## React

### function component
```javascript
// Father.jsx
import { createContext, useState, useEffect } from 'react';
import Child from './Child';
import './Father.css';

export const ThemeContext = createContext(null);

export default function Page() {
  const [ count, setCount ] = useState(0);
  const [theme, setTheme] = useState("dark");

  useEffect(() => {}, [theme])

  return (
    <ThemeContext.Provider
      value={{
        count,
        theme,
        setTheme,
      }}
    >
      <button onClick={() => setCount(count + 1)}>add</button> {count}
      <Child />
      <div className={`theme-content ${theme}`}>theme content</div>
    </ThemeContext.Provider>
  );
}
```
首先我们定义一个父组件，父组件内count变量用来测试在外层更改后，内层接收到的变量是否有更改，theme和setTheme变量用来测试传递到孙子组件的变量与方法是否生效。
在function component中，通过`createContext`创建得到一个对象`ThemeContext`，然后使用Context.Provider来包裹住需要接收到context数据的组件。
```css
/* Father.css */
.theme-content {
  width: 100px;
  height: 100px;
}
.dark {
  background-color: black;
  border: 1px solid black;
  color: white;
}
.light {
  background: white;
  color: black;
  border: 1px solid black;
}
```

在子组件中，只有引用孙子组件的逻辑：
```javascript
// Child.jsx
import GrandSon from './GrandSon';

export default function Child() {
  return <GrandSon />;
};
```

在孙子组件中，通过`useContext`来接收context数据：
```javascript
// GrandSon.jsx
import { useContext } from 'react';
import { ThemeContext } from './Father';

const GrandSon = () => {
  const { count, theme, setTheme } = useContext(ThemeContext);
  return (
    <div>
      <p>最外层count: {count}</p>
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

export default GrandSon;
```
![[test85.gif]]
可以看到，通过context传递的变量在跨组件内接收后，能够正常使用，而且传递到组件内的父组件的方法，也能够正常被执行。需要注意的一点时，context并非是万能的银弹，使用context时需要注意传递的值会不会影响到页面的刷新，这对于有性能要求的页面来说非常重要。

### class component
在class component中，父组件同样使用`createContext`，而孙子组件中，可以使用Context.Consumer来接收context的传值
```javascript
// GrandSon.jsx
import React from "react";
import { ThemeContext } from "./Father";

class GrandSon extends React.Component {
  render() {
    return (
      <ThemeContext.Consumer>
        {(context) => {
          const { count, theme, setTheme } = context;
          return (
            <div>
              <p>最外层count: {count}</p>
              <p>主题：{theme}</p>
              <button onClick={() => setTheme("dark")}>Dark</button>
              <button onClick={() => setTheme("light")}>Light</button>
            </div>
          );
        }}
      </ThemeContext.Consumer>
    );
  }
}

export default GrandSon;
```

## Vue

### 3.x
在父组件中，使用`provide`的形式来传递值。
```html
<!-- Father.vue -->
<template>
  <div>
    <button @click="add">add</button> {{ count }}
    <Child />
    <div :class="['theme-content', theme]">theme content</div>
  </div>
</template>

<script>
	export const ContextKey = "ThemeContext";
</script>

<script setup>
	import { ref, provide } from "vue";
	import Child from "./Child.vue";
	
	const count = ref(0);
	const theme = ref("dark");
	
	const add = () => {
	  count.value++;
	};
	
	const setTheme = (newTheme) => {
	  theme.value = newTheme;
	};
	
	provide(ContextKey, {
	  count,
	  theme,
	  setTheme,
	});
</script>

<style scoped>
.theme-content {
  width: 100px;
  height: 100px;
}
.dark {
  background-color: black;
  color: white;
  border: 1px solid black;
}
.light {
  background-color: white;
  color: black;
  border: 1px solid black;
}
</style>
```

作为中间层级的子组件Child.vue，我们不再演示，其就只是引用孙子组件而已。

在孙子组件GrandSon.vue内部，通过`inject`的形式来接收对应Context的值：
```html
<!-- GrandSon.vue -->
<template>
  <div>
      <p>最外层count: {{count}}</p>
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

  const { count, theme, setTheme } = inject(ContextKey);
</script>
```

![[test86.gif]]

### 2.x
```html
<!-- Father.vue -->
<template>
  <div>
    <button @click="add">add</button> {{ count }}
    <Child />
    <div :class="['theme-content', theme]">theme content</div>
  </div>
</template>

<script>
import Child from "./Child.vue";

export default {
  data() {
    return {
      count: 0,
      theme: 'dark'
    }
  },
  methods: {
    add() {
      this.count++
    },
    setTheme(value) {
      this.theme = value;
    }
  },
  components: {
    Child,
  },
  provide() {
    return {
      ThemeContext: {
        count: this.count,
        theme: this.theme,
        setTheme: this.setTheme
      }
    }
  }
}
</script>

<style scoped>
...
</style>
```

```html
<!-- GrandSon.vue -->
<template>
  <div>
      <p>最外层count: {{ThemeContext.count}}</p>
      <p>主题：{{ThemeContext.theme}}</p>
      <button @click="ThemeContext.setTheme('dark')">
        Dark
      </button>
      <button @click="ThemeContext.setTheme('light')">
        Light
      </button>
    </div>
</template>

<script>
export default {
  inject: ['ThemeContext'],
}
</script>
```

![[test87.gif]]

不管是在3.x还是2.x中，`provide`和`inject`传递的数据都是非响应性的，但是由于引用类型的特殊性，在子孙组件拿到了数据之后，它们的属性还是能够正常地响应变化，这也是为什么我们直接在3.x中传递`ref`定义的数据后能够实现响应性。那在2.x中，当我们传递基本数据类型的值，也想组件内接收到的值能够响应式地变化，该如何实现呢？

要想拿到基本数据类型的最新值，我们可以将传值改为一个方法，在这个方法中return我们的基本数据类型数值。

首先是改造App里provide的返回：
```javascript
provide() {
	return {
	  ThemeContext: {
		count: () => this.count,
		theme: () => this.theme,
		setTheme: this.setTheme
	  }
	}
}
```

然后在组件内以方法的形式调用：
```html
<template>
  <div>
      <p>最外层count: {{ThemeContext.count()}}</p>
      <p>主题：{{ThemeContext.theme()}}</p>
      <button @click="ThemeContext.setTheme('dark')">
        Dark
      </button>
      <button @click="ThemeContext.setTheme('light')">
        Light
      </button>
    </div>
</template>
```
## Svelte

```html
<script context="module">
// Father.svelte
  export const ContextKey = "ThemeContext";
</script>

<script>
  import { setContext } from "svelte";
  import Child from "./Child.svelte";

  let count = 0;
  let theme = "dark";

  const add = () => {
    count++;
  };
  const setTheme = (value) => {
    theme = value;
  };

  setContext(ContextKey, {
    count,
    theme,
    setTheme,
  });
</script>

<button on:click={add}>add</button>{count}
<Child />
<div class={`theme-content ${theme}`}>theme content</div>

<style>
  .theme-content {
    width: 100px;
    height: 100px;
  }
  .dark {
    background-color: black;
    color: white;
    border: 1px solid black;
  }
  .light {
    background-color: white;
    color: black;
    border: 1px solid black;
  }
</style>
```

同样，子组件Child.svelte只有引用孙子组件的逻辑，不展示代码。

孙子组件GrandSon.svelte：
```html
<script>
  // GrandSon.svelte
    import { getContext } from "svelte";
    import { ContextKey } from "./Father.svelte";
    const { count, theme, setTheme } = getContext(ContextKey);
</script>

<div>
	<p>最外层count: {count}</p>
	<p>主题：{theme}</p>
	<button on:click={() => setTheme("dark")}>
	  Dark
	</button>
	<button on:click={() => setTheme("light")}>			
	  Light
	</button>
</div>
```
![[test88.gif]]
Svelte的Context传值同样不支持响应性，要想使传递的值具有响应性，我们需要结合`svelte/store`进行使用。

首先是对Father.svelte进行改造：
```html
<script context="module">
  export const ContextKey = "ThemeContext";
</script>

<script>
  import { setContext } from "svelte";
  import { writable } from "svelte/store";
  import Child from "./Child.svelte";

  let countStore = writable(0);
  let themeStore = writable("dark");

  const add = () => {
    $countStore++;
  };
  const setTheme = (value) => {
    $themeStore = value;
  };

  setContext(ContextKey, {
    countStore,
    themeStore,
    setTheme,
  });
</script>

<button on:click={add}>add</button>{$countStore}
<Child />
<div class={`theme-content ${$themeStore}`}>theme content</div>
```

然后是改造GrandSon.svelte：
```html
<script>
  // GrandSon.svelte
  import { getContext } from "svelte";
  import { ContextKey } from "./Father.svelte";
  const { countStore, themeStore, setTheme } = getContext(ContextKey);
</script>

<div>
  <p>最外层count: {$countStore}</p>
  <p>主题：{$themeStore}</p>
  <button on:click={() => setTheme("dark")}> Dark </button><button
    on:click={() => setTheme("light")}
  >
    Light
  </button>
</div>
```
## 小结

对比篇到这里就告一段落，相信大家经过前面一些篇章的学习，已经有所发现：一个最基本的前端框架，应该向使用者提供xxxxx方法。
