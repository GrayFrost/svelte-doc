ref主要用于获取组件实例或访问 DOM 节点,也可以用于组件间通信。

## React

React中使用ref的主要形式如下：
```javascript
import { useRef } from 'react';

export default function Page() {
  const ref = useRef();
  return <div ref={ref} />
}
```

### 元素标签

如果我们把ref绑定到html标签上，可以直接获取到DOM节点。
首先通过`useRef`声明变量（在React中，`useRef`还能用来存储不会触发重新渲染的值），这个变量是一个对象，当其有值时，需要通过`ref.current`来获取其中的值，比如下列例子中的`faterRef.current`：

```javascript
// Father.jsx
import { useRef, useEffect, useState } from "react";

export default function Page() {
  const fatherRef = useRef(null);
  const [className, setClassName] = useState("");

  useEffect(() => {
    if (fatherRef && fatherRef.current) {
      setClassName(fatherRef.current.className);
    }
  }, [fatherRef]);

  return (
    <section>
      <div ref={fatherRef} className="w-10 h-10 border"></div>
      <div>className is: {className}</div>
    </section>
  );
}
```

### 组件

如果把ref绑定到组件上，可以获取取件的实例。

在子组件中，我们定义了一个方法，如果我们希望把子组件的方法对外暴露，需要使用`useImperativeHandle`和`React.forwardRef`的方式。

```javascript
// Child.jsx
import React, { useImperativeHandle } from "react";

function _Child(props, ref) {
  const sayHello = () => {
    console.log("hello");
  };

  useImperativeHandle(ref, () => {
    return {
      sayHello
    }
  });

  return <div>子应用</div>;
}
export default React.forwardRef(_Child);
```

而在父组件中：
```javascript
import { useRef, useEffect, useState } from "react";
export default function Page() {
  const childRef = useRef(null);

  const sayHello = () => {
    if (childRef && childRef.current) {
      childRef.current.sayHello();
    }
  };

  return (
    <section>
      <button onClick={sayHello}>
        通过ref调用子应用方法
      </button>
      <Child ref={childRef} />
    </section>
  );
}
```

在`class component`中，使用`createRef`来创建ref对象：
```javascript
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();
  }
  render() {
    return <div ref={this.myRef} />;
  }
}
```

## Vue

在Vue 3.x中，使用`ref()`来定义ref变量，通过`ref="ref"`来引用。
```html
<template>
  <div ref="ref" />
</template>

<script setup>
  import { ref } from 'vue';
  const ref = ref(null);
</script>
```
### 元素标签

同样参照React的例子，我们使用ref绑定到html标签上以获取DOM，然后通过DOM上的className属性能够得到绑定的节点的class类名：
```html
<template>
  <section>
    <div ref="fatherRef" class="w-10 h-10 border"></div>
    <div>className is: {{className}}</div>
  </section>
</template>

<script setup>
import { ref, watchEffect } from 'vue';

const fatherRef = ref(null);

let className = ref('');

watchEffect(() => {
  if (fatherRef.value) {
    className.value = fatherRef.value.className;
  }
});
</script>
```

### 组件

在子组件中，使用`defineExpose`来将子组件内部的方法暴露出去。
```html
<template>
  <div>子应用</div>
</template>

<script setup>
const sayHello = () => {
  console.log('hello')
}
defineExpose({
  sayHello
});
</script>
```

在父组件中，通过ref.value来引用子组件的方法：
```html
<template>
  <section>
    <button @click="sayHello">
      通过ref调用子应用方法
    </button>
    <Child ref="childRef" />
  </section>
</template>

<script setup>
import { ref, watchEffect } from 'vue';
import Child from './Child.vue';

const childRef = ref(null);

const sayHello = () => {
  if (childRef.value) {
    childRef.value.sayHello();
  }
}
</script>
```


如果是在Vue 2.x中，在使用了`ref="ref"`声明后，直接通过`this.$refs.ref`的形式来使用即可。
```html
<template>
  <div ref="divRef"></div>
</template>

<script>
export default {
  mounted() {
    console.log(this.$refs.divRef);
  }
}
</script>
```

## Svelte

在Svelte中，使用`bind:this`指令来实现ref功能，Svelte的ref对象不用像React要再多一层current和Vue多一层value来获取值。
### 元素标签

如果`bind:this`绑定到正常html标签上，可以直接获取到DOM节点，然后便能直接使用DOM节点上的属性。

```html
<script>
  let fatherRef;
  let className;

  $: {
    if (fatherRef) {
      className = fatherRef.className;
    }
  }
</script>

<section>
  <div bind:this={fatherRef} class="w-10 h-10 border"></div>
  <div>className is: {className}</div>
</section>
```

### 组件

在子组件中，使用`export`的方式导出组件内部的方法。
```html
<script>
  export const sayHello = () => {
    console.log('hello')
  }
</script>

<div>子应用</div>
```

在父组件中：
```html
<script>
  import Child from "./Child.svelte";

  let childRef;

  const sayHello = () => {
    if (childRef) {
      childRef.sayHello();
    }
  };
</script>

<section>
  <button on:click={sayHello}>
    通过ref调用子应用方法
  </button>
  <Child bind:this={childRef} />
</section>
```
如果想要父组件操作子组件的数据，需要对外`export`一个能够获取该数据的方法，而不是直接`export`数据。因为直接export数据在Svelte中是把该数据声明为一个对外的prop，亦或者像我们在《特定标签》中学习到的配置`<svelte:options>`的`accessors`属性为true。

## 小结

本章我们对比了：
- 框架的ref功能如果绑定到正常的html标签，可以获取DOM节点的引用，如果绑定到组件上，可以获取组件的实例
- React的`function component`中通过`useRef`来定义ref，通过`useImperativeHandle`来定义组件对外暴露的方法。在`class component`中使用`createRef`来定义ref。ref的具体值在ref.current上。
- 在Vue`3.x`中通过`ref()`来定义ref，通过`defineExpose()`来定义组件对外暴露的方法。在`2.x`中通过`this.$refs`来调用。
- Svelte通过`bind:this`来实现ref。组件内通过`export`来定义对外暴露的方法。
