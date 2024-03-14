## React

### 行内样式

在React中，行内样式需要以`style={styleObj}`的形式
```javascript
export default function Page() {
  return <div style={{
    border: '2px solid black',
    backgroundColor: 'purple',
    color: 'white'
  }}>style</div>
}
```

![[Pasted image 20240314155402.png]]
在样式对象内，之前在行内样式中使用`-`连接的属性需要转成使用驼峰的形式，比如`font-size`要写成`fontSize`、`background-color`要写成`backgroundColor`等。

### 非行内样式
### class属性

### 模块化
TODO 演示
create-react-app module.css
自定义类型BEM

## Vue

### 行内样式
```html
<template>
  <div :style="{ border: '1px solid black', fontSize: 16 }">hello world</div>
</template>
```

### 非行内样式

### class属性

### 模块化

```html
<!-- Father.vue -->
<template>
  <div>
    <div class="style">father style</div>
    <Child />
  </div>
</template>

<script>
import Child from './Child.vue';
export default {
  components: {
    Child,
  }
}
</script>

<style>
  .style {
    color: red;
  }
</style>
```

```html
<template>
  <div class="style">style</div>
</template>

<script>
export default {}
</script>

<style>
  .style {
    color: green;
  }
</style>
```
我们原本期望子组件的颜色是绿色，然而得到的却是如下：
![[Pasted image 20240314161849.png]]

要想实现样式模块化，需要在`<style>`标签中添加scoped属性：
```diff
<template>
  <div class="style">style</div>
</template>

<script>
export default {}
</script>

- <style>
+ <style scoped>
  .style {
    color: green;
  }
</style>
```
![[Pasted image 20240314162045.png]]
可以看到，Vue是通过添加`data-v-xxx`属性的形式来实现模块化。

## Svelte

### 行内样式

在Svelte中，行内样式的书写和在正常html标签内写style样式一样，均是以字符串的形式。
```html
<div style="border: 2px solid black; background-color: purple; color: white;">style</div>
```

### 非行内样式
正常我们的样式都写在`<style></style>`标签中。

### class属性

### 模块化

Svelte文件的`<style></style>`标签内的样式，默认启用了模块化特性。
```html
<div class="style">style</div>

<style>
  .style {
    color: red;
  }
</style>
```

![[Pasted image 20240314160708.png]]
翻看编译后的结果，发现我们的样式类型都带上了后缀。
## 小结

除了以上特性外，还可以结合诸如Tailwind等原子类库、Emotion等cssinjs库以及Less、Sass等样式预处理器来实现页面样式。

