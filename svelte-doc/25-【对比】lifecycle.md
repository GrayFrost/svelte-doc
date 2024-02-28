组件都伴随着生命周期，一个组件通常都存在着创建、更新、销毁等这几个相同的生命周期，而不同的框架由于各自实现的不同，往往提供了除这些共同生命周期之外的一些差异化生命周期。

## React
static getDerivedStateFromProps

componentDidMount

shouldComponentUpdate

getSnapshotBeforeUpdate

componentDidUpdate

static getDerivedStateFromError

componentDidCatch

componentWillUnmount

老实说，笔者一直对react的生命周期感到头疼


## Vue

beforeCreate

created

beforeMount

mounted

beforeUpdate

updated

beforeDestroy

destroyed

activated

deactivated

当为页面添加了keep—alive属性后，需要注意在activated和deactivated这两个生命钩子中进行一些操作。

## Svelte

onMount
beforeUpdate
afterUpdate
onDestroy

tick， vue的$nextTick

## 小结