UI组件库的一大特性便是组件化，我们除了在当前组件设置数据之外，绝大多时候，都需要把数据传递给其他组件使用。比如我们抽象了一些公共逻辑，写了一个公共组件，公共组件通过接收数据来展示不同的功能，而不同的页面在引用公共组件时，便需要向这个公共组件传递数据。那我们应该如何向不同组件传递数据，甚至传递方法呢？  

组件之间的通信包括了父组件向子组件传值，这里的传值可能包括了传递数据和传递方法，子组件消费父组件的数据和调用父组件的方法；
也可能父组件直接使用子组件的数据和调用子组件的方法；
还存在跨层级的组件之间的传值。

## React

React由于使用的是jsx语法，写法非常灵活，定义一个继承React.Component的class或者写一个有html返回内容的function就是一个组件。

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

react接收props需要考虑组件重复渲染的问题， memo(), usemomo, usecallback, shouldComponentUpdate等一系列优化的方法。

### 类型限定
ToDo如何限定props类型

## Vue
Vue使用的是单文件的组织形式，在一个文件中，我们在template这种写html内容，然后分别在script标签内和style标签内定义组件的脚本和样式。

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

### 类型限定
TODO 2.x中通过定义在props中来接收父组件data，还能限定数据类型

## Svelte
Svelte同样使用的是sfc的形式。

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

svelte在父组件中通过`on:[事件]={父组件方法}`的形式为子组件添加事件绑定，通过`变量={父组件变量}`的形式传递。而在子组件中，通过export的方式，将原本限定在组件内的变量，改为能够接受外部的传值。而调用父组件的则通过调用createEventDispatcher方法来创建一个对象，通过调用该对象来调用父组件的方法。如果需要传值，则通过`dispatch(方法名,值)`的形式传递。

TODO: typescript类型限定
todo: 也能props传方法

## 小结
