
即使当今的主流开发模式提倡的是数据驱动视图，然而我们仍旧无法完全摆脱对dom的操作，我们也不能摆脱。这里笔者可以随便举几个例子：input输入框的focus和blur的触发、使用`<input type="file" />`来自定义实现上传时的手动click等。

无论是在React还是在Vue中，都有提供对dom的引用的api操作，而这种对dom的引用通常称为Ref。那在Svelte中，我们要如何拿到我们的Ref呢？
## bind:this

在《数据与方法》一章，我们在使用双向绑定功能时，用到了`bind:value={value}`的方式。而如果想要访问真实的dom，同样需要使用到`bind`。

```javascript
bind:this={dom_node}
```

接下来，

input.focus()

  

input.click(); // 上传

## 子实例的数据与方法

## 多个子实例

子组件实例

  
组件bind:this
如何拿到子组件的数据 方法 export

多个ref ，数组形式存储



## 小结