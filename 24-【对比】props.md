UI组件库的一大特性便是组件化，我们除了在当前组件设置数据之外，绝大多时候，都需要把数据传递给其他组件使用。比如我们抽象了一些公共逻辑，写了一个公共组件，公共组件通过接收数据来展示不同的功能，而不同的页面在引用公共组件时，便需要向这个公共组件传递数据。那我们应该如何向不同组件传递数据，甚至传递方法呢？  

组件之间的通信包括了父组件向子组件传值，这里的传值可能包括了传递数据和传递方法，子组件消费父组件的数据和调用父组件的方法；也可能父组件直接使用子组件的数据和调用子组件的方法；甚至存在跨层级的组件之间的传值。

## React

### 传值
React由于使用的是jsx语法，写法非常灵活，定义一个继承React.Component的class或者写一个有html返回内容的function就是一个组件。

在父组件中，我们把定义好的数据和方法当做子组件的属性，以`key={value}`的形式进行传递。
```javascript
// Father.jsx
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

而在子组件中，通过props来接收从父组件传递过来的数据。
```javascript
// Child.jsx
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

在`class component`中的使用大同小异：
```javascript
import React from 'react';

class Child extends React.Component {
  constructor(props) {
    super(props)
  }
  render() {
    const { count } = this.props;
    return <div>子组件 {count}</div>
  }
}

export default class Father extends React.Component {
  constructor() {
    super();
    this.state = {
      count: 1
    };
  }
  render() {
    const {count} = this.state;
    return <Child count={count}></Child>
  }
}
```

React接收props需要考虑组件重复渲染的问题，为此，可以结合`memo()`、 `useMemo`、`useCallback`、`shouldComponentUpdate`等一系列优化的方法来使用。

### 类型限定
组件通过props接收外部传递的值，然而如果传递的值的类型不符合组件的要求，可能会导致组件内的逻辑错误，因此，限定props的传值类型是很有必要的。  

在React中，可以如下进行类型限定：

```javascript
/**
 * FUNCTIONAL COMPONENTS
 */
function ReactComponent(props) {
  // ...你的逻辑
}

ReactComponent.propTypes = {
  // ...prop的类型限定
}

/**
 * CLASS COMPONENTS: 方式1
 */
class ReactComponent extends React.Component {
  // ...你的逻辑
}

ReactComponent.propTypes = {
  // ...prop的类型限定
}

/**
 * CLASS COMPONENTS: 方式2
 */
class ReactComponent extends React.Component {
  // ...你的逻辑

  static propTypes = {
    // ...prop的类型限定
  }
}
```

另一种方式是使用[props-type](https://github.com/facebook/prop-types/tree/main)库。
```bash
npm install prop-types --save
```

```javascript
import React from 'react';
import PropTypes from 'prop-types';

class MyComponent extends React.Component {
  render() {
    // ... do things with the props
  }
}

MyComponent.propTypes = {
  // You can declare that a prop is a specific JS primitive. By default, these
  // are all optional.
  optionalArray: PropTypes.array,
  optionalBigInt: PropTypes.bigint,
  optionalBool: PropTypes.bool,
  optionalFunc: PropTypes.func,
  optionalNumber: PropTypes.number,
  optionalObject: PropTypes.object,
  optionalString: PropTypes.string,
  optionalSymbol: PropTypes.symbol,
  ...
}
```

## Vue

### 传值
Vue使用的是单文件的组织形式，在一个文件中，我们在template这种写html内容，然后分别在script标签内和style标签内定义组件的脚本和样式。

在Vue中，父组件将数据以`:key="value"`或`key="value"`的形式向子组件传值（传变量时使用`:key`，传常量时使用`key`）；以`@function="callback"`的形式来监听子组件派发的事件。

```html
<!-- Father.vue -->
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

在Vue 3.x中，子组件通过`defineProps([key])`接收对应的传值，通过`defineEmits(['function'])`的方式对外派发事件。
```html
<!-- Child.vue -->
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


以上例子在Vue 2.x中如下：
```html
<!-- Father.vue -->
<template>
  <Child :name="name" @sayHello="sayHello" />
</template>

<script>
import Child from "./Child.vue";

export default {
  data() {
    return {
      name: "hello",
    };
  },
  methods: {
    sayHello() {
      console.log("hello");
    },
  },
  components: {
    Child,
  },
};
</script>
```

子组件通过`props: {}`来接收数据，通过`this.$emit('function')`的方式来派发事件。
```html
<!-- Child.vue -->
<template>
  <div>
    <span>name: {{ name }}</span>
    <button @click="onClickFunc">
      调用父级方法
    </button>
  </div>
</template>

<script>
export default {
  props: {
    name,
  },
  methods: {
    onClickFunc() {
      this.$emit('sayHello');
    }
  }
}
</script>
```
### 类型限定

在Vue 3.x中，在`defineProps({ key: Type })`中定义：
```html
<!-- Child.svelte -->
<template>
  <div>子组件</div>
</template>

<script setup>
defineProps({
  str: String,
  num: Number,
  bool: Boolean,
  arr: Array,
  obj: Object,
  func: Function,
  promise: Promise,
});
</script>
```

```html
<!-- App.svelte -->
<template>
  <Child :str="str" :num="num" />
</template>

<script setup>
import { ref } from 'vue';
import Child from "./Child.vue";

let str = ref(1);
let num = ref(1);
</script>
```

在Vue 2.x中，通过`props: { key: Type }`的形式进行类型限定：
```html
<!-- Child.svelte -->
<template>
  <div>子组件</div>
</template>

<script>
export default {
  props: {
    str: String,
    num: Number,
    bool: Boolean,
    arr: Array,
    obj: Object,
    func: Function,
    promise: Promise,
  },
};
</script>
```

```html
<!-- App.svelte -->
<template>
  <Child :str="str" :num="num" />
</template>

<script>
import Child from "./Child.vue";

export default {
  data() {
    return {
      str: 1,
      num: 1,
    };
  },
  components: {
    Child,
  },
};
</script>
```

当我们传递了错误的类型时，可以看到控制台的警告信息。
![](./img/24-1.png)

## Svelte
Svelte同样使用的是sfc的形式。

Svelte在父组件中通过`on:[function]={callback}`的形式为子组件添加事件绑定，通过`key={value}`的形式传递。
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

在子组件中，通过`export`的方式，将原本限定在组件内的变量，改为能够接受外部的传值。而调用父组件的则通过调用`createEventDispatcher`方法来创建一个对象，通过调用该对象来调用父组件的方法。如果需要传值，则通过`dispatch(方法名, 值)`的形式传递。

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

在父组件传值的形式比较像React，子组件派发事件的形式比较像Vue。

### 类型限定

Svelte目前官方没有给出内置的props校验api，但强大的Typescript能够帮助我们完成这一功能。

## Typescript

[Typescript](https://www.typescriptlang.org/)是一种基于 JavaScript 构建的强类型编程语言。我们不仅能在Svelte中使用Typescript进行类型校验，在React、Vue中同样可以使用。

在React中，需要在`tsx`文件中使用Typescript。
```javascript
// Child.tsx
function MyButton({ title }: { title: string }) {
  return (
    <button>{title}</button>
  );
}
```

在Vue中，需要把`<script>`标签置为ts类型
```html
<script setup lang="ts">
interface Props {
  foo: string
  bar?: number
}

const props = defineProps<Props>()
</script>
```

在Svelte中，同样需要把`<script>`标签类型置为ts。
```html
<script lang="ts">
  export let name: string;
</script>
```

## 小结

本章我们对比了：
- React页面往组件传值使用`key={value}`的形式；组件接收外部的值通过`props`属性接收
- Vue页面往组件传值使用`:key="value"`或`key="value"`的形式；在`2.x`中组件接收外部的值通过`props: {}`来接收，在`3.x`中组件接收外部的值通过`defineProps([key])`的形式
- Svelte同样是通过`key={value}`的形式往组件内传值，而组件接收外部的值则通过`export let key`的形式
- React父子页面事件的沟通仍通过props传递实现
- Vue通过`@function="callback"`的形式监听子页面派发的事件，在`2.x`中子页面通过`this.$emit()`的方式派发事件，在`3.x`中子页面通过`defineEmits()`来派发事件
- Svelte中父页面通过`on:[function]={callback}`的形式监听子页面派发的事件，在子页面中则需借助`createEventDispatcher`来进行事件派发
- 三大框架对props传值类型的限定

