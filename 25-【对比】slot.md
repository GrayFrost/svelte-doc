default slot, name slot, slot 传递数据？

## React

父组件

```javascript
import SlotContainer1 from './Child1'
import SlotContainer2 from './Child2'

export default function Page() {
  return (
    <section>
      <SlotContainer1>hello world</SlotContainer1>
      <SlotContainer2 header={<span>父组件header</span>} footer="父组件footer">
        hello world2
      </SlotContainer2>
    </section>
  );
}
```

子组件1

```javascript
export default function SlotContainer1(props) {
  const { children } = props;
  return <div>{children}</div>;
}
```

子组件2

```javascript
export default function SlotContainer2(props) {
  const { header, footer, children } = props;
  return (
    <div>
      <header>{header}</header>
      <section>子组件原有内容</section>
      <section>{children}</section>
      <footer>{footer}</footer>
    </div>
  );
}
```

## Vue

父组件

```html
<template>
  <section>
    <SlotContainer1> hello world </SlotContainer1>
    <SlotContainer2>
      hello world2
      <template v-slot:header>父组件header</template>
      <template #footer>父组件footer</template>
    </SlotContainer2>
  </section>
</template>

<script setup>
import SlotContainer1 from "./Child1.vue";
import SlotContainer2 from "./Child2.vue";
</script>
```

子组件1

```html
<template>
  <div>
    <slot></slot>
  </div>
</template>
```

疑问？：能不能只写slot呢？

子组件2

```html
<template>
  <div>
    <header><slot name="header"></slot></header>
    <section>子组件原有内容</section>
    <slot></slot>
    <footer><slot name="footer"></slot></footer>
  </div>
</template>
```

## Svelte

父组件

```html
<script>
  import SlotContainer1 from './Child1.svelte';
  import SlotContainer2 from './Child2.svelte';
</script>

<section>
  <SlotContainer1>
    hello world
  </SlotContainer1>
  <SlotContainer2>
    hello world2
    <span slot="header">父组件header</span>
    <span slot="footer">父组件footer</span>
  </SlotContainer2>
</section>
```

子组件1

```html
<div>
  <slot></slot>
</div>
```

疑问？：能不能只写slot呢？

子组件2

```html
<div>
  <header><slot name="header"></slot></header>
  <section>子组件原有内容</section>
  <slot></slot>
  <footer><slot name="footer"></slot></footer>
</div>
```
