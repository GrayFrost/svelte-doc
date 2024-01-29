UI组件库的一大特性便是组件化，我们除了在当前组件设置数据之外，绝大多时候，都需要把数据传递给其他组件使用。比如我们抽象了一些公共逻辑，写了一个公共组件，公共组件通过接收数据来展示不同的功能，而不同的页面在引用公共组件时，便需要向这个公共组件传递数据。那我们应该如何向不同组件传递数据，甚至传递方法呢？

## React

父组件

```javascript
import Child from './Child';

export default function Page() {
  const [name] = useState("hello");
  const sayHello = () => {
    console.log("hello");
  };
  return (
    <Child name={name} sayHello={sayHello} />
  );
}
```

子组件

```javascript
export default function Child(props) {
  const { name, sayHello } = props;
  const onClickFunc = () => {
    sayHello();
  };
  return (
    <div>
      <span>name: {name}</span>
      <button onClick={onClickFunc}>
        调用父级方法
      </button>
    </div>
  );
}
```

react接收props需要考虑组件重复渲染的问题， memo(), usemomo, usecallback, shouldComponentUpdate等一系列优化的方法。ToDo如何限定props类型

## Vue

父组件

```html
<template>
  <Child :name="name" @sayHello="sayHello" />
</template>

<script setup>
  import { ref } from "vue";
  import Child from "./Child.vue";
  
  const name = ref("hello");
  const sayHello = () => {
    console.log("hello");
  };
</script>
```

子组件

```html
<template>
  <div>
    <span>name: {{ name }}</span>
    <button @click="onClickFunc">
      调用父级方法
    </button>
  </div>
</template>

<script setup>
import { ref } from "vue";
const props = defineProps(["name"]);
const name = ref(props.name);

const emit = defineEmits(["sayHello"]);

const onClickFunc = () => {
  emit("sayHello");
};
</script>

```

2.x中通过定义在props中来接收父组件data，还能限定数据类型

## Svelte

父组件

```html
<script>
  import Child from './Child.svelte';
  let name = 'hello';

  const sayHello = () => {
    console.log('hello');
  }
</script>

<Child name={name} on:sayHello={sayHello} />
```

子组件

```html
<script>
  import { createEventDispatcher } from "svelte";
  const dispatch = createEventDispatcher();

  export let name;

  const onClickFunc = () => {
    dispatch("sayHello");
  };
</script>

<div>
  <span>name: {name}</span>
  <button on:click={onClickFunc}>
    调用父级方法
  </button>
</div>

```
