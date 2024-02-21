## React

父组件

```javascript
import { useRef, useEffect, useState } from "react";
export default function Page() {
  const fatherRef = useRef(null);
  const childRef = useRef(null);
  const [className, setClassName] = useState("");

  useEffect(() => {
    if (fatherRef && fatherRef.current) {
      setClassName(fatherRef.current.className);
    }
  }, [fatherRef]);

  const sayHello = () => {
    if (childRef && childRef.current) {
      childRef.current.sayHello();
    }
  };

  return (
    <section>
      <div ref={fatherRef} className="w-10 h-10 border"></div>
      <div>className is: {className}</div>
      <button onClick={sayHello}>
        通过ref调用子应用方法
      </button>
      <Child ref={childRef} />
    </section>
  );
}

```

子组件

```javascript
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

## Vue

父组件

```html
<template>
  <section>
    <div ref="fatherRef" class="w-10 h-10 border"></div>
    <div>className is: {{className}}</div>
    <button @click="sayHello">
      通过ref调用子应用方法
    </button>
    <Child ref="childRef" />
  </section>
</template>

<script setup>
import { ref, watchEffect } from 'vue';
import Child from './Child.vue';

const fatherRef = ref(null);
const childRef = ref(null);

let className = ref('');

watchEffect(() => {
  if (fatherRef.value) {
    className.value = fatherRef.value.className;
  }
})

const sayHello = () => {
  if (childRef.value) {
    childRef.value.sayHello();
  }
}

</script>
```

子组件

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

## Svelte

父组件

```html
<script>
  import Child from "./Child.svelte";

  let fatherRef;
  let childRef;
  let className;

  $: {
    if (fatherRef) {
      className = fatherRef.className;
    }
  }

  const sayHello = () => {
    if (childRef) {
      childRef.sayHello();
    }
  };
</script>

<section>
  <div bind:this={fatherRef} class="w-10 h-10 border"></div>
  <div>className is: {className}</div>
  <button on:click={sayHello}>
    通过ref调用子应用方法
  </button>
  <Child bind:this={childRef} />
</section>

```

子组件

```html
<script>
  export const sayHello = () => {
    console.log('hello')
  }
</script>

<div>子应用</div>
```
