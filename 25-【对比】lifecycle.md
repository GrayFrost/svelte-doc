组件都伴随着生命周期，一个组件通常都存在着创建、更新、销毁等这几个相同的生命周期，而不同的框架由于各自实现的不同，往往提供了除这些共同生命周期之外的一些差异化生命周期。

## React

![[Pasted image 20240321113020.png]] 
[图片来源](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)

- `constructor`：一个构造器，里面可以接收一个父组件传来的props然后初始化state值。
- `static getDerivedStateFromProps`：取代了旧的`componentWillMount`、`componentWillReceiveProps`、`componentWillUpdate`这三个钩子。
- `render`：定义展示到页面上的html内容，render执行完后将内容渲染到浏览器上。
- `componentDidMount`：在组件挂载完毕后执行。
- `shouldComponentUpdate`：当更新state值的时候会执行这个函数，通常用做优化页面的手段。
- `getSnapshotBeforeUpdate`：这个钩子可获取到即将要更新的props和state。
- `componentDidUpdate`：组件更新完毕执行的钩子函数。
- `componentWillUnmount`：组件将要卸载时执行。

除此之外，还有`static getDerivedStateFromError`、`componentDidCatch`用来处理页面报错的钩子。老实说，笔者一直对react的生命周期感到头疼。

## Vue

![[lifecycle.DLmSwRQE.png]]
[图片来源](https://vuejs.org/guide/essentials/lifecycle)

在Vue 2.x中，
- `beforeCreate`：实例化之后，数据的观测和事件的配置之前的时候调用。
- `created`：在实例创建完成后被立即同步调用。
- `beforeMount`：在挂载开始之前被调用。
- `mounted`：实例被挂载后调用。
- `beforeUpdate`：在数据发生改变后，DOM被更新之前调用。
- `updated`：由于数据更改导致的虚拟 DOM 重新渲染，在更新完后会调用该钩子。
- `beforeDestroy`：在实例销毁之前调用。
- `destroyed`：实例销毁后调用。

当为页面添加了keep-alive属性后，需要注意在`activated`和`deactivated`这两个生命钩子中进行一些操作。

在Vue3.x中：
- `onBeforeMount`：在挂载开始之前被调用。
- `onMounted`：组件挂载时调用。
- `onBeforeUpdate`：数据更新时调用。
- `onUpdated`：由于数据更新导致的虚拟 DOM 重新渲染，在更新之后会调用该钩子。
- `onBeforeUnmount`：在卸载组件实例之前调用。
- `onUnmounted`：卸载组件实例后调用。

同时也提供了在keep-alive时使用的生命周期钩子`onActivated`、`onDeactivated`和用于捕获页面错误的钩子`onErrorCaptured`。

## Svelte

- `onMount`
- `beforeUpdate`
- `afterUpdate`
- `onDestroy`
- `tick`

TODO
## 小结

本章我们简单对比了三大框架的生命周期，对外提供的生命周期数量的优势，仁者见仁。一方面，繁多的生命周期钩子可以让开发者更细致地控制组件；而另一方面，大量的生命周期钩子也提升了框架的学习曲线。