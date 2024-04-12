# Svelte5 抢先看！

![](image-20.png)

## 安装
```bash
npm create svelte@latest svelte-5
```
当我们在执行上述命令后，首先会看到如下提示：
```
┌  Welcome to SvelteKit!
│
◆  Which Svelte app template?
│  ● SvelteKit demo app (A demo app showcasing some of
the features of SvelteKit - play a word guessing game
that works without JavaScript!)
│  ○ Skeleton project
│  ○ Library project
└
```

从弹出的选项中可以看到，这里给我们提供了体验Svelte 5的选项。
![](image-17.png)

接着是正常的安装依赖操作。
```bash
cd svelte-5
npm install 
npm run dev
```

安装完后，我们可以看到package.json里的依赖显示：
```json
{
  "svelte": "^5.0.0-next.1",
}
```

当然，如果不想执行上述操作也想体验Svelte 5，那可以尝试官方提供的[REPL](https://svelte-5-preview.vercel.app/)。


## Runes
Svelte 5最大的改动便是引入了**Runes**。  

也许大家对Runes不太熟悉，但如果说到英雄联盟里的[瑞兹](https://www.leagueoflegends.com/en-au/champions/ryze/)，相信大家耳熟能详。瑞兹的英文全称是**THE RUNE MAGE**，中文翻译是符文法师。不错，Runes即符文。 
![灾难始终慢我一步](https://ddragon.leagueoflegends.com/cdn/img/champion/splash/Ryze_0.jpg)

为了读起来更自然点，文章中将继续以Runes来说明。  
Runes是一组函数式的符号，无需额外引入，可以直接使用，是Svelte5语言的特性，目前有以下Runes：

* `$state`
* `$state.frozen`
* `$derived`
* `$derived.by`
* `$effect`
* `$effect.pre`
* `$effect.active`
* `$effect.root`
* `$props`
* `$inspect`

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
![](test31.gif)

在class中也能使用
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

![](test32.gif)

对比之前的数据声明，多了`$state`。

数组
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
![](test33.gif)

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
![](test34.gif)


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
![](image-18.png)

![](image-19.png)

当我们调用update2时，数组能正常更新。

浅拷贝？

在Svelte4中，数据的声明只能在script的顶部作用域中
```html
<script>
  let data = any;
</script>
```
而使用`$state`Runes则可以摆脱这个限制。

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
![](test35.gif)

我们可以传`count * 2`，但是不能传`count++`。

在Svelte4中，我们要声明一个派生属性，需在`$: `里进行。

### `$derived.by`
接收一个函数。
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
![](test36.gif)

### `$effect`
> runs when the component is mounted, and again whenever `count` or `doubled` change,after the DOM has been updated.
因此，`$effect`相当于`$: {}`和`afterUpdate`的结合体。笔者对此改动表示热烈欢迎，因为本人始终觉得在有些框架中，一个组件对外提供一大串又丑又长的生命周期，着实加大了开发者的心智负担。

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
![](test39.gif)

```html
<script>
  let width = $state(10);

  $effect(() => {
    console.log('width改变', width);
  });
</script>

width: <input type="number" bind:value={width} />
```
![](test40.gif)

### `$effect.pre`

用于替代`beforeUpdate`生命周期。
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
![](test41.gif)

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
![](test42.gif)
在这里之所以要把width也一起打印出来，是因为`$effect`和`$effect.pre`的后续执行需要依赖width。

### `$effect.active`

官网给出的说明是用于判断是否在一个effect中或是否在template中
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
![](test43.gif)
经过试验，在`$effect`、`$effect.pre`和template中能够正常判断。  
我们尝试在`$derived.by`中使用，可是打印结果提醒我们`$effect.active`在其中并不适用。

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
![](test1.gif)

### `$props`
和明显，用来接收props的Runes。
```html
<script>
  export let value;
</script>

子组件：{value}
```

```html
<script>
  let { value } = $props();
</script>

子组件：{value}
```

### `$inspect`
```html
<script>
    let count = $state(0);
    $inspect(count); // 当count变化时console.log出来
</script>

<button onclick={() => count++}>add count</button>
```
![](test2.gif)

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
使用`{#snippet snippetName()}...{/snippet}`来定义我们要复用的片段，使用`{@render snippetName()}`来复用定义好的片段。

![](image-21.png)
在之前，如果我们要复用这一段代码，只能把它放入到另一个svelte文件中，当成组件来引用。

### 传递给组件
能够把片段当成一个属性传递给组件。
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
![](image-22.png)

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
使用`$props()`来接收方法。终于不用使用难用的`createEventDispatcher`了。

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
![](test44.gif)

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
这里的`{@render ...}`和后面介绍的Snippets有关。思考：如何接收具名插槽？

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
![](test45.gif)
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

![](test46.gif)


### unstate
把使用`$state()`生成的响应式数据（对象或数组）变成非响应式的。
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
![](test47.gif)

### mount
```javascript
import { mount } from 'svelte';
import App from './App.svelte';

const app = mount(App, {
	target: document.querySelector('#app'),
	props: { some: 'property' }
});
```

Svelte4
```javascript
import App from './App.svelte'

const app = new App({
  target: document.getElementById('app'),
})

export default app
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
Svelte 5移除了`onMount`、`beforeUpdate`、`afterUpdate`等生命周期，移除了`createDispatcher`和`slot`，将组件的方法和插槽内容都通过`$props`来传递，让我们不用再关注`CustomEvent.detail`等细节，很明显降低了学习的曲线。同时引入了Runes的概念，对数据状态能够进行更为颗粒度的控制，引入了Snippets，可以对页面内容进行Element层级的复用。  
文章只介绍了较为关键的特性，更多细节内容，还请感兴趣的读者们去官网深度探索。

## 参考
* [svelte-5-preview](https://svelte-5-preview.vercel.app/docs/introduction)
* [runes](https://svelte.dev/blog/runes)