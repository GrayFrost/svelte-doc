TODO: 演示两个例子，及其编译后的js代码。第一个例子，直接简单的hello world，第二个例子，count更新。
SvelteComponent 则是包含了 svelte 组件内置的属性和生命周期，它们与 Fragment 的属性和生命周期是息息相关，SvelteComponent 是依赖于 Fragment，组件的变化会触发 Fragment 的变化

new App()
->
App extends SvelteComponent
->
SvelteComponent中的constructor中调用名为init的函数
->
init方法中会调用create_fragment
->
create_fragment中是由compiler生成的关于一个组件如何创建更新卸载的生命周期函数



通过 dirty 位检查变量是否发生更新，如果发生更新调用 dom 操作函数对 dom 进行局部更新。上面例子的 set_data 函数作用是给 dom 设置 innerText。根据数据更新的视图位置的不同，还会有 set_props之类的更新 dom 属性的函数等

每个数据的赋值语句，svelte都会生成对`$$invalidate`的调用，invalidate的调用主要做的是对某个改动的变量进行标记，然后在微任务中调用patch函数，根据变量改动的脏标记进行局部更新

* instance 方法：可以理解为 instance方法是 svelte 组件的构造器。写在 script 里的代码，会被生成在 instance 方法里。每个组件实例都会调用一次形成自己的闭包，从而隔离各自的数据，通过 instance 方法返回的数组就是上下文。代码中的赋值语句，会被生成为数据更新逻辑。变量定义会被收集生成上下文数组。
* 上下文：每个 svelte 组件都会有自己的上下文，上下文存储的就是 script 标签内定义的变量的值。svelte 会为每个组件实例内定义的数据生成上下文，按照变量的声明顺序保存在一个名为 ctx 数组内


### init

路径：packages/svelte/src/runtime/internal/Component.js

```
function init(
	component,
	options,
	instance,
	create_fragment,
	not_equal,
	props,
	append_styles = null,
	dirty = [-1]
) {}
```

往component示例的 `$$`属性上挂载数据，形成一个context上下文，整个组件的行为都被记录在里面

```
const $$ = (component.$$ = {})
```



`$$.instance` ? todo 待定

首先执行一次 `$$.update`，因为执行了一次
执行所有的`$$.before_update`

判断`$$.fragment`是有有值，`$$.fragment`则是由compile生成的create_fragment方法返回的组件fragment生命周期，如果`$$.fragment`有值，则调用里面的c方法，即fragment的创建。

mount_component挂载组件到页面target上
调用flush()

#### mount_component

调用create_fragment生命周期里的m方法
执行`$$.after_update`里的回调


通过 dirty 位检查变量是否发生更新，如果发生更新调用 dom 操作函数对 dom 进行局部更新。上面例子的 set_data 函数作用是给 dom 设置 innerText。根据数据更新的视图位置的不同，还会有 set_props之类的更新 dom 属性的函数等

1. 事件或者其他操作出发更新流程
2. 在instance的`$$invalidate`方法中，比较操作前后ctx中的值有没有发生改变，如果发生改变则继续往下
3. 执行make_dirty函数标记为脏值，添加带有脏值需要更新的组件，从而继续触发更新
4. 执行schedule_update函数
5. 执行flush函数，将所有的脏值组件取出，以此执行其update方法
6. 在update方法中，执行的是Fragment自身的p方法，p方法做的事情就是确定需要更新组件，并操作和更新dom组件，从而完成了最后的流程