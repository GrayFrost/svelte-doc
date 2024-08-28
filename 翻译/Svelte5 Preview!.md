# Svelte5 抢先看！


![Svelte 5](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/948e70dc97ef4fa0a20b91af52a3361c~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1280&h=720&s=32329&e=png&b=ff3c00)


## 安装

```bash
npm create svelte@latest svelte-5
```

When we are executing the above command, we will first see the following prompt:

    ┌  Welcome to SvelteKit!
    │
    ◆  Which Svelte app template?
    │  ● SvelteKit demo app (A demo app showcasing some of
    the features of SvelteKit - play a word guessing game
    that works without JavaScript!)
    │  ○ Skeleton project
    │  ○ Library project
    └

As you can see from the options that pop up, here we are given the option to experience Svelte 5.

![install](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0638e06edfea43d9ba37ff7555fde277~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=742&h=544&s=62816&e=png&b=010101)

Then comes the normal operation of installing dependencies.

```bash
cd svelte-5
npm install 
npm run dev
```

After installation, we can see the dependencies displayed in `package.json`:

```json
{
  "svelte": "^5.0.0-next.1",
}
```

If you don't want to perform the above operations and also want to experience Svelte 5, then you can try the officially provided [REPL](https://svelte-5-preview.vercel.app/).

## Runes

The biggest change to the Svelte 5 is the introduction of **Runes**.

Runes are a set of functional notations that can be used directly without additional introduction and are a feature of the Svelte5 language, which currently has the following Runes:

*   `$state`
*   `$state.frozen`
*   `$derived`
*   `$derived.by`
*   `$effect`
*   `$effect.pre`
*   `$effect.active`
*   `$effect.root`
*   `$props`
*   `$inspect`

### `$state`

```html
<script>
    let count = $state(0);
</script>

<button on:click={() => count++}>
    click
</button>
{count}
```

![$state](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ce594f3ec228453e83d0a37361c89139~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1169&h=722&s=20340&e=gif&f=12&b=fefefe)


We can use it in class:

```html
<script>
class Person {
    name = $state();

    constructor(name) {
        this.name = name;
    }
}

const person = new Person();
</script>

<input bind:value={person.name} /><br />
姓名：{person.name}
```

![$state in class](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2b9f06be79bb42c29a802f412fd12b7f~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1169&h=722&s=51374&e=gif&f=20&b=fefefe)


对比之前的数据声明，多了`$state`。但使用了`$state`声明的数组和对象，不再像Svelte4那样，需要使用一些小技巧来赋值更新。

在Svelte 5中，数组的更新就和在js操作中一般。

```html
<script>
const arr = $state([1,2]);

const onAdd = () => {
    arr.push(1);
}

const onSub = () => {
    arr.pop();
}
</script>

<p>
    <button on:click={onAdd}>增加</button>
    <button on:click={onSub}>减少</button>
</p>
数组：{arr.join(',')}
```

![$state array](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f516f92c1e6042d68052712cd22d5026~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1169&h=722&s=55210&e=gif&f=33&b=fefefe)

对象演示：
```html
<script>
const obj = $state({
    foo: {
        bar: 'hello'
    }
});

</script>

<input bind:value={obj.foo.bar} />
{obj.foo.bar}
```

![$state object](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/350b50454a814f0e81fec92305441d68~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1169&h=722&s=62550&e=gif&f=19&b=fefefe)


### `$state.frozen`

```html
<script>
let arr = $state.frozen([1,2]);

const update1 = () => {
    arr.push(1);
}

const update2 = () => {
    arr = [...arr, 1];
}
</script>

<p>
    <button on:click={update1}>更新1</button>
    <button on:click={update2}>更新2</button>
</p>
数组：{arr.join(',')}
```

使用了`$state.frozen`声明后的数组，无法直接操作数组。vscode会给出提示：
![error](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/138c58c828f64b48afe1038332f0ff24~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1634&h=862&s=126979&e=png&b=212121)

在浏览器中点击第一个更新，也会报错：
![error2](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/42117563860141c1a2364da0e2bd98f7~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=2148&h=240&s=82439&e=png&b=fdf8f8)

当我们调用update2时，数组能正常更新。

看下官网的一个提示：

> Objects and arrays passed to `$state.frozen` will be shallowly frozen using `Object.freeze()`. If you don't want this, pass in a clone of the object or array instead.


```html
<script>
let arr = $state.frozen([{
        obj: {
                name: 'bob',
                age: 18
        }
}]);
let arr2 = $state.frozen([{name: 'carter', age: 19}]);

let arr3 = $state.frozen(['david']);

arr[0].obj.name = 'svelte';
arr2[0].name = 'svelte';
arr3[0] = 'svelte';
</script>

{JSON.stringify(arr)}
{JSON.stringify(arr2)}
{JSON.stringify(arr3)}
```
在上面这个例子中，`arr3[0] = 'svelte';`会直接报错，剩下两个赋值则不会。把报错赋值的语句去掉，我们可以看到：

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1bb7b33b06444d38ae6f6628c3cff947~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1194&h=172&s=18185&e=png&b=fefefe)

### `$derived`

`$derived`接收一个参数，这个参数是一个没有副作用的表达式。

```html
<script>
let count = $state(0);

let double = $derived(count * 2);

const onClick = () => {
    count++;
}
</script>

<button on:click={onClick}>更新</button><br />
count: {count}
double: {double}
```

![$derived](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0ae620629ce24b36bde38e76bed6336d~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1169&h=722&s=49410&e=gif&f=22&b=fefefe)

我们可以传`count * 2`，但是不能传`count++`。

在Svelte4中，我们要声明一个派生属性，需在`$: `里进行。

### `$derived.by`

`$derived.by`接收一个函数。

```html
<script>
  let arr = $state([1,2,3]);
  let total = $derived.by(() => {
    return arr.reduce((pre, cur) => pre + cur, 0);
  });

  const onAdd = () => {
    arr.push(arr.length + 1);
  }
</script>

<button on:click={onAdd}>add</button>
total: {total}
```

![$derived.by](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/33e0b4e23a7941d2808cdd243521d393~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1169&h=722&s=42336&e=gif&f=11&b=fefefe)

### `$effect`

> runs when the component is mounted, and again whenever `count` or `doubled` change,after the DOM has been updated.

因此，`$effect`相当于`$: {}`和`onMount`、`afterUpdate`的结合体。笔者对此改动表示热烈欢迎，因为本人始终觉得在有些框架中，一个组件对外提供一大串又臭又长的生命周期，着实加大了开发者的心智负担。

在Svelte 4中：
```html
<script>
  import { afterUpdate } from 'svelte';
  let width = 10;
  let height = 10;

  $: console.log('width改变', width);

  afterUpdate(() => {
    console.log('afterUpdate')
  });
</script>

width: <input type="number" bind:value={width} />
```

![afterUpdate](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/13691ba49d694525b2ab13bc0381fe06~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1169&h=722&s=89493&e=gif&f=31&b=fefefe)

在Svelte 5中，使用`$effect`rune：
```html
<script>
  let width = $state(10);

  $effect(() => {
    console.log('width改变', width);
  });
</script>

width: <input type="number" bind:value={width} />
```

![$effect](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e104e7cb597746c2a5eccf64d8ac5899~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1169&h=722&s=112228&e=gif&f=31&b=fefefe)

### `$effect.pre`

用于替代`beforeUpdate`生命周期。

我们看个Svelte 4的例子：
```html
<script>
  import { beforeUpdate, afterUpdate } from 'svelte';
  let width = 10;

  beforeUpdate(() => {
    const dom = document.querySelector('#width');
    if (dom) {
      console.log('beforeUpdate', dom.innerHTML);
    }
  });

  afterUpdate(() => {
    const dom = document.querySelector('#width');
    if (dom) {
      console.log('afterUpdate', dom.innerHTML);
    }
  });
</script>

width: <input type="number" bind:value={width} />
<span id="width">{width}</span>
```

![test41.gif](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/06690b34d9d54e26ad65def62673c490~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1169&h=722&s=125313&e=gif&f=51&b=fefefe)

在Svelte 5中：
```html
<script>
  let width = $state(10);

  $effect.pre(() => {
    const dom = document.querySelector('#width');
    if (dom) {
      console.log('svelte5 beforeUpdate', dom.innerHTML, width);
    }
  })

  $effect(() => {
    const dom = document.querySelector('#width');
    if (dom) {
      console.log('svelte5 afterUpdate', dom.innerHTML, width);
    }
  });
</script>

width: <input type="number" bind:value={width} />
<span id="width">{width}</span>
```

![$effect.pre](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/45861f12f9f741f0bf60f5322628be80~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1169&h=722&s=191798&e=gif&f=55&b=fefefe)

在这里之所以要把width也一起打印出来，是因为`$effect`和`$effect.pre`的后续执行需要依赖width。

### `$effect.active`

官网给出的说明是用于判断是否在一个effect中或是否在template中。

```html
<script>
  let count = $state(0);

  $effect(() => {
    console.log('effect', count);
    console.log('isActive in $effect', $effect.active());
  });

  $effect.pre(() => {
    console.log('pre', count);
    console.log('isActive in $effect.pre', $effect.active());
  });

  let double = $derived.by(() => {
    console.log('isActive in derived.by', $effect.active());
    return count * 2;
  })

  console.log('isActive no runes', $effect.active());
</script>

<input type="number" bind:value={count} />
double: {double}
in template:{console.log('isActive in template', $effect.active())}
```

![$effect.active](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8022402c88cd41c0a52f1b31f42a7fa1~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1169&h=722&s=293843&e=gif&f=106&b=fefefe)

经过试验，在`$effect`、`$effect.pre`和template中能够正常判断。我们尝试在`$derived.by`中使用，可是打印结果提醒我们`$effect.active`在其中并不适用。

### `$effect.root`
使用`#effect.root`，我们可以手动地控制`$effect`的生效与否。
```html
<script>
let count = $state(0);

const offEffect = $effect.root(() => {
    $effect(() => {
        console.log(count);
    });

    return () => {
        console.log('关闭$effect');
    };
});
</script>

<button onclick={() => offEffect()}>关闭$effect</button>
<button onclick={() => count++}>更新</button>
```

![$effect.root](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6761dfa9d000401696f0cd3235f666b5~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=897&h=532&s=76217&e=gif&f=40&b=fdfdfd)

如果不调用`$effect.root`的返回函数，依旧正常追踪依赖更新。当我们执行了返回函数后，可以看到`$effect`不再执行。

### `$props`

很明显，用来接收props的Rune。

Svelte 4中：
```html
<script>
  export let value;
</script>

子组件：{value}
```

Svelte 5中：
```html
<script>
  let { value } = $props();
</script>

子组件：{value}
```

### `$inspect`
当状态更新时，会打印相关信息。
```html
<script>
    let count = $state(0);
    $inspect(count); // 当count变化时console.log出来
</script>

<button onclick={() => count++}>add count</button>
```

![test2.gif](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dd406c4887a946bbabfdc7b34f0e96fd~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=897&h=532&s=73955&e=gif&f=31&b=fefefe)

## Snippets

俗称片段。使用Snippets可以进行内容复用。

```html
<script>
  let arr = $state([{
    name: 'carter',
    age: 18,
    gender: '男'
  }, {
    name: 'lily',
    age: 19,
    gender: '女'
  }]);
</script>

{#snippet person({ name, age, gender })}
  <p>
    <span>姓名：{name}</span>
    <span> 年龄：{age}</span>
    <span>性别：{gender}</span>
  </p>
{/snippet}

{#each arr as item, i}
  {@render person(item)}
{/each}
```

使用`{#snippet snippetName()}...{/snippet}`来定义我们要复用的片段；
使用`{@render snippetName()}`来复用定义好的片段。

![snippet](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/06791dfff51e49658e67de51ff86d85a~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1008&h=326&s=28573&e=png&b=ffffff)


### 传递给组件

能够把片段当成一个属性传递给组件。

我们定义一个组件，可以接受header和footer片段参数：
```html
<script>
  let { header, footer } = $props();
</script>

<div>
  <header>{@render header()}</header>
  组件内部
  <footer>{@render footer()}</footer>
</div>
```

将我们定义好的片段传递给组件：
```html
<script>
    import Svelte5 from './Svelte5.svelte';
</script>

{#snippet header()}
    <div>头部内容</div>
{/snippet}

{#snippet footer()}
    <div>尾部内容</div>
{/snippet}

<Svelte5 {header} {footer} />
```

![snippet传递](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3b1239f7bcc4478bbb5126bfff3c9ff2~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1450&h=238&s=24060&e=png&b=ffffff)


## 事件

### 事件监听

在演示Runes和Snippets时，笔者在使用到数据绑定时，仍旧使用的是Svelte4`on:eventname`的形式。其实在Svelte5中，关于方法的使用也有更新：从原来`on:eventname`的形式转变为`oneventname`的形式。

```diff
<script>
  const onClick = () => {
    console.log('click');
  }
</script>

- <button on:click={onClick}>click</button>
+ <button onclick={onClick}>click</button>
```

### 组件事件

使用`$props()`来接收方法。终于不用再使用难用的`createEventDispatcher`了。

```html
<script>
  let { onClick, onClick2 } = $props();
</script>

<button onclick={onClick}>click</button>
<button onclick={e => onClick2('hello svelte')}>click2</button>
```

```html
<script>
import Svelte5 from './Svelte5.svelte';

const onClick = (event) => {
    console.log('event', event);
}

const onClick2 = (value) => {
    console.log('value', value);
}
</script>

<Svelte5 {onClick} {onClick2} />
```

![function](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/571546dc203e4a919f5d1ccf8c035fc1~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1169&h=722&s=61732&e=gif&f=27&b=fefefe)

除了接收方法，我们还能接收插槽内容。没错，在Svelte5中，插槽的使用转而投向了jsx的写法，通过`let { children } = $props()`来接收插槽内容。

```html
<script>
  let { children } = $props();
</script>

<div>
  <header>头部</header>
  <main>{@render children() }</main>
  <footer>底部</footer>
</div>
```

```html
<script>
    import Svelte5 from './Svelte5.svelte';
</script>

<Svelte5>
    内容
</Svelte5>
```

这里的`{@render ...}`和后面介绍的Snippets有关。

### 事件修饰符

Svelte4的事件修饰符如下：

```html
<button on:click|once|preventDefault={handler}>...</button>
```

像once、preventDefault、stopPropagation等需要自己手动实现：

```html
<script>
function once(fn) {
    return function (event) {
        if (fn) fn.call(this, event);
        fn = null;
    };
}

function preventDefault(fn) {
    return function (event) {
        event.preventDefault();
        fn.call(this, event);
    };
}
</script>

<button onclick={once(preventDefault(handler))}>...</button>
```

而`capture`、`passive`、`nonpassive`等，Svelte5仍提供了对应的事件修饰符：
```html
<button onclickcapture={...}>...</button>
```
说实话，不太美观。

## 方法

### untrack

```html
<script>
    let width = $state(10);
    let height = $state(10);
    let area;

    $effect(() => {
        console.log('width or height change', width, height);
    });
</script>

width: <input type="number" bind:value={width} />, 
height: <input type="number" bind:value={height} />
```

![不追踪依赖](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/151cdc1c4f7849659941d29f03e0f4d0~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1169&h=722&s=128426&e=gif&f=31&b=fefefe)

如果我们只想在width执行时输出console，那就需要不追踪height的依赖。

```html
<script>
    import { untrack } from 'svelte';

    let width = $state(10);
    let height = $state(10);

    $effect(() => {
        console.log('width or height change', width, untrack(() => height));
    });
</script>

width: <input type="number" bind:value={width} />, 
height: <input type="number" bind:value={height} />
```

![untrack](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/37e535717a0843209b4bf4435a81c2f5~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1169&h=722&s=97632&e=gif&f=37&b=fefefe)

### unstate

把使用`$state()`生成的响应式数据（对象或数组）变成非响应式的。

演示一个Svelte 4的例子：
```html
<script>
  // svelte 4
  export let obj = {
    name: 'text'
  };

  const onUpdate = () => {
    obj.name = '' + Math.random();
  }

  $: console.log('obj change', obj.name);
</script>

<button on:click={onUpdate}>update</button><br />
obj: {obj.name}<br />
```
额外话题：如果我们需要`$:`生效，需要`$: console.log(obj.name)`而不是`$: console.log(obj)`。

演示一下unstate的功能：
```html
<script>
  // svelte 5
  import { unstate } from 'svelte';

  let obj = $state({
    name: 'text'
  });
  let _obj = unstate(obj);

  const onUpdate = () => {
    obj.name = '' + Math.random();
    _obj.name = '' + Math.random();
  }

  $effect(() => {
    console.log('$state data change', obj.name);
  });

  $effect(() => {
    console.log('unstate data cahnge', _obj.name);
  })

</script>

<button onclick={onUpdate}>update</button><br />
obj: {obj.name}<br />
_obj: {_obj.name}
```

![unstate](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3d42626638ce4e8284c30ef32bc2f6fa~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1169&h=722&s=163877&e=gif&f=72&b=fdfdfd)
使用了unstate的数组在赋值后，`$effect`不会追踪这部分非响应式数据。

### mount

Svelte 4中挂载App：
```javascript
import App from './App.svelte'

const app = new App({
     target: document.getElementById('app'),
})

export default app
```


Svelte 5中挂载App：
```javascript
import { mount } from 'svelte';
import App from './App.svelte';

const app = mount(App, {
    target: document.querySelector('#app'),
    props: { some: 'property' }
});
```

### hydrate

SSR使用。

```javascript
import { hydrate } from 'svelte';
import App from './App.svelte';

const app = hydrate(App, {
    target: document.querySelector('#app'),
    props: { some: 'property' }
});
```

### render

服务端渲染时使用。接收一个Component并返回一个带有html和head参数的对象。

```javascript
import { render } from 'svelte/server';
import App from './App.svelte';

const result = render(App, {	
    props: { some: 'property' }
});
```

## 总结

Svelte 5移除了`onMount`、`beforeUpdate`、`afterUpdate`等生命周期，移除了`createDispatcher`，将组件的方法通过`$props`来传递，让我们不用再关注`CustomEvent.detail`等细节，很明显降低了学习的曲线。同时引入了Runes的概念，对数据状态能够进行更为颗粒度的控制，引入了Snippets，可以对页面内容进行Element层级的复用。

文章只介绍了较为关键的特性，更多细节内容，还请感兴趣的读者们去官网深度探索。

## 参考

*   [svelte-5-preview](https://svelte-5-preview.vercel.app/docs/introduction)
*   [runes](https://svelte.dev/blog/runes)