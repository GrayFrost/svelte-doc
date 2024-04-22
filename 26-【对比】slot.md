## React

### 默认slot

在父组件中，在使用子组件时，直接在子组件标签内填充内容：
```javascript
// Father.jsx
import SlotContainer1 from './Child1'

export default function Page() {
  return (
    <SlotContainer1>hello world</SlotContainer1>
  );
}
```

在子组件中，通过props中的`children`属性来接收插槽内容。
```javascript
// Child1.jsx
export default function SlotContainer1(props) {
  const { children } = props;
  return <div>{children}</div>;
}
```
### 具名slot

在父组件中，除了在子组件标签内天加内容外，还可以通过props的形式来传递组件。
```javascript
// Father.jsx
import SlotContainer2 from './Child2';

export default function Page() {
  return (
    <SlotContainer2 header={<span>父组件header</span>} footer="父组件footer">
      hello world2
    </SlotContainer2>
  );
}
```

在子组件中，通过props除了接收到默认插槽内容children，还可以通过其他自定义属性来实现具名插槽。
```javascript
// Child2.jsx
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

### slot传值

在实际业务开发中，当然不可能都是简单的直接在子组件内填充静态内容，往往涉及到组件内部希望往插槽的内容传递数据。最常见的例子就是表格组件，笔者这里举个伪代码的例子：
```javascript
<Table data={list}>
  <column slot="a">{item}</column>
</Table
```
在单元格中，往往需要自定义单元格内容，此时我们就需要从组件内接收到单元格的数据，然后再自定义slot的内容展示。

这里演示一种称为[Render Props](https://legacy.reactjs.org/docs/render-props.html)的实现方式：
```javascript
const Child = ({ children }) => (
  <div>
    {children({ message: 'Hello World' })}
  </div>
);

export default function Father() {
  return (
    <Child>
      {({ message }) => <p>{message}</p>}
    </Child>
  );
} 
```

## Vue

### 默认slot

同样是在子组件中直接插入内容：
```html
<!-- Father.vue -->
<template>
  <SlotContainer1>hello world</SlotContainer1>
</template>

<script setup>
import SlotContainer1 from "./Child1.vue";
</script>
```

子组件中，声明用于接收插槽内容的标签`<slot>`：
```html
<!-- Child1.vue -->
<template>
  <slot></slot>
</template>
```

### 具名slot

在页面中，除了往组件标签内插入默认内容外，还能通过`v-slot`标签来表示往具体名称的插槽中填值，`#name`的表示形式是`v-slot:name`的简写。

```html
<!-- Father.vue -->
<template>
  <SlotContainer2>
    hello world2
    <template v-slot:header>父组件header</template>
    <template #footer>父组件footer</template>
  </SlotContainer2>
</template>

<script setup>
import SlotContainer2 from "./Child2.vue";
</script>
```

在子组件中，要定义一个具名插槽，需要在`<slot>`标签中定义`name="value"`。
```html
<!-- Child2.vue -->
<template>
  <div>
    <header><slot name="header"></slot></header>
    <section>子组件原有内容</section>
    <slot></slot>
    <footer><slot name="footer"></slot></footer>
  </div>
</template>
```

### slot传值

在拥有插槽定义的组件内，像普通传参一样传值：
```html
<!-- Child.vue -->
<template>
  <slot message="Hello World"></slot>
</template>
```

在外部引用组件时，通过`v-slot="props"`接收组件内传递过来的值：
```html
<!-- Father.vue -->
<template>
  <Child v-slot="slotProps">{{ slotProps.message }}</Child>
</template>

<script setup>
import Child from "./Child.vue";
</script>
```

## Svelte

### 默认slot

在父组件中，同样是正常往组件标签内填充内容：
```html
<script>
// Father.svelte
  import SlotContainer1 from './Child1.svelte';
</script>

<SlotContainer1>
  hello world
</SlotContainer1>
```

子组件内，同样使用`<slot>`标签接收插槽内容：
```html
<!-- Child1.svelte -->
<slot></slot>
```

### 具名slot

通过`slot="name"`来标注往具体名称的插槽中填充内容：
```html
<script>
// Father.svelte
  import SlotContainer2 from './Child2.svelte';
</script>

<SlotContainer2>
  hello world2
  <span slot="header">父组件header</span>
  <span slot="footer">父组件footer</span>
</SlotContainer2>
```

在子组件中，同样为`<slot>`标签设置`name="name"`来添加具名插槽。
```html
<!-- Child2.svelte -->
<div>
  <header><slot name="header"></slot></header>
  <section>子组件原有内容</section>
  <slot></slot>
  <footer><slot name="footer"></slot></footer>
</div>
```

### slot传值

在拥有插槽定义的组件内，像普通传参一样传值：
```html
<!-- Child.svelte -->
<slot message={"Hello World"}></slot>
```

在外部引用组件时，通过`let:name={value}`的形式来接收组件内传递过来的值：
```html
<script>
// Father.svelte
  import Child from './Child.svelte';
</script>

<Child let:message={message}>
  {message}
</Child>
```

## 小结

本章我们对比了：
- React通过props上的children来接收插槽内容，通过render props的方式来进行插槽和组件间的传值
- Vue通过`<slot>`标签来完成插槽功能。通过`<slot name="x">`的形式来定义特定名称的插槽占位，然后外部通过`v-slot:name`或`#name`来向具名插槽填值。Vue的slot和组件间的传值通过`<slot message>`和`v-slot`来实现。
- Svelte同样通过`<slot>`标签来实现插槽。通过`<slot name="x">`的形式来定义特定名称的插槽占位，外部通过`slot="name"`来向具名插槽填值。Svelte的slot和组件间的传值通过`<slot xxx={}>`和`let:xxx={}`的形式来实现。
