TODO: 演示两个例子，及其编译后的js代码。第一个例子，直接简单的hello world，第二个例子，count更新。
SvelteComponent 则是包含了 svelte 组件内置的属性和生命周期，它们与 Fragment 的属性和生命周期是息息相关，SvelteComponent 是依赖于 Fragment，组件的变化会触发 Fragment 的变化





通过 dirty 位检查变量是否发生更新，如果发生更新调用 dom 操作函数对 dom 进行局部更新。上面例子的 set_data 函数作用是给 dom 设置 innerText。根据数据更新的视图位置的不同，还会有 set_props之类的更新 dom 属性的函数等

每个数据的赋值语句，svelte都会生成对$$invalidate的调用，invalidate的调用主要做的是对某个改动的变量进行标记，然后在微任务中调用patch函数，根据变量改动的脏标记进行局部更新

* instance 方法：可以理解为 instance方法是 svelte 组件的构造器。写在 script 里的代码，会被生成在 instance 方法里。每个组件实例都会调用一次形成自己的闭包，从而隔离各自的数据，通过 instance 方法返回的数组就是上下文。代码中的赋值语句，会被生成为数据更新逻辑。变量定义会被收集生成上下文数组。
* 上下文：每个 svelte 组件都会有自己的上下文，上下文存储的就是 script 标签内定义的变量的值。svelte 会为每个组件实例内定义的数据生成上下文，按照变量的声明顺序保存在一个名为 ctx 数组内
