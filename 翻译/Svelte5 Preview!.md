# Svelte5 Preview


![Svelte 5](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/948e70dc97ef4fa0a20b91af52a3361c~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1280&h=720&s=32329&e=png&b=ff3c00)


## Installation

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


Compared with the data declaration in Svelte 4, there is an additional `$state`. However, for arrays and objects declared with `$state`, unlike in Svelte 4, there is no longer a need to use some tricks for assignment and update.

In Svelte 5, updating arrays is just like in normal JavaScript operations.

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

Object：
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

After an array is declared with `$state.frozen`, the array cannot be directly manipulated. VSCode will give a prompt:
![error](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/138c58c828f64b48afe1038332f0ff24~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1634&h=862&s=126979&e=png&b=212121)

Clicking on the first button to update in the browser will also result in an error:
![error2](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/42117563860141c1a2364da0e2bd98f7~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=2148&h=240&s=82439&e=png&b=fdf8f8)

When we call update2 function, array can be updated normally.

Let's see a tip:
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

In the above example, `arr3[0] = 'svelte';` will directly result in an error, while the remaining two assignments will not. Remove the statement that causes the error in assignment, and we can see:

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1bb7b33b06444d38ae6f6628c3cff947~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1194&h=172&s=18185&e=png&b=fefefe)

### `$derived`

`$derived` receives a parameter. This parameter is an expression without side effects.

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

We can pass `count * 2`, but we cannot pass `count++`.

In Svelte 4, if we want to declare a derived property, we need to do it within `$: `.

### `$derived.by`

`$derived.by` receives a function as parameter。

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

Therefore, `$effect` is equivalent to a combination of `$: {}` and `onMount`, `afterUpdate`. I warmly welcomes this change because I always feel that in some frameworks, a component provides a long list of cumbersome life cycles to the outside world, which indeed increases the mental burden on developers.

In Svelte 4:
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

In Svelte 5, use `$effect`rune：
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

To replace `beforeUpdate` lifecycle。

Let's see an example of Svelte 4：
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

In Svelte 5：
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

The reason why width is also printed here is that the subsequent execution of `$effect` and `$effect.pre` depends on width.

### `$effect.active`

It is used to determine whether it is in an effect or in a template.

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

After testing, it can be judged normally in `$effect`, `$effect.pre` and template. We tried to use it in `$derived.by`, but the printed result reminds us that `$effect.active` is not applicable in it.

### `$effect.root`
Using `#effect.root`, we can manually control whether `$effect` takes effect.

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

If the return function of `$effect.root` is not called, the dependency update is still tracked normally. When we execute the return function, we can see that `$effect` is no longer executed.

### `$props`

Obviously, the Rune is used to receive props.

In Svelte 4:
```html
<script>
  export let value;
</script>

子组件：{value}
```

In Svelte5:
```html
<script>
  let { value } = $props();
</script>

子组件：{value}
```

### `$inspect`

When state updates, will print out some information.
```html
<script>
    let count = $state(0);
    $inspect(count); // console.log something when count changes
</script>

<button onclick={() => count++}>add count</button>
```

![test2.gif](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dd406c4887a946bbabfdc7b34f0e96fd~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=897&h=532&s=73955&e=gif&f=31&b=fefefe)

## Snippets

Using Snippets can reuse html fragment content.

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

Use`{#snippet snippetName()}...{/snippet}` to define common logic fragment；
Use`{@render snippetName()}` to reuse fragment。

![snippet](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/06791dfff51e49658e67de51ff86d85a~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1008&h=326&s=28573&e=png&b=ffffff)


### Snippet as a prop

Snippets can be passed to a component as a property.

We define a component that can accept header and footer snippet parameters.

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

Pass the defined snippets to the component:
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


## Event

### Event Listener

When demonstrating Runes and Snippets, when I used data binding, I still used the form of Svelte 4 `on:eventname`. In fact, in Svelte 5, the use of methods has also been updated: from the original form of `on:eventname` to the form of `oneventname`.

```diff
<script>
  const onClick = () => {
    console.log('click');
  }
</script>

- <button on:click={onClick}>click</button>
+ <button onclick={onClick}>click</button>
```

### Event in component

Use `$props()` to receive methods. Finally, there is no need to use the cumbersome `createEventDispatcher` anymore.

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

In addition to receiving methods, we can also receive slot content. That's right. In Svelte 5, the use of slots has turned to the writing method of jsx. Slot content is received through `let { children } = $props()`.

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

The `{@render...}` here is related to the Snippets introduced later.

### Event modifiers

Event modifiers in Svelte 4:

```html
<button on:click|once|preventDefault={handler}>...</button>
```

Event modifiers like once, preventDefault, stopPropagation, etc. they need to be implemented manually.

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

As for `capture`, `passive`, `nonpassive`, etc., Svelte 5 still provides corresponding event modifiers.
```html
<button onclickcapture={...}>...</button>
```
To be honest, it's not very beautiful.

## Function

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

If we only want to output to the console when width is executed, then we need to not track the dependency of height.

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

Turn the reactive data (object or array) generated by using `$state()` into non-reactive data.

In Svelte 4:
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
Additional topic: If we need `$:` to take effect, we need `$: console.log(obj.name)` instead of `$: console.log(obj)`.

Example of unstate function:
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

After assigning a value to an array that uses unstate, `$effect` will not track this part of non-reactive data.

### mount

mount App in Svelte 4:
```javascript
import App from './App.svelte'

const app = new App({
     target: document.getElementById('app'),
})

export default app
```


mount App in Svelte 5:
```javascript
import { mount } from 'svelte';
import App from './App.svelte';

const app = mount(App, {
    target: document.querySelector('#app'),
    props: { some: 'property' }
});
```

### hydrate

Use in SSR.

```javascript
import { hydrate } from 'svelte';
import App from './App.svelte';

const app = hydrate(App, {
    target: document.querySelector('#app'),
    props: { some: 'property' }
});
```

### render

Used when server-side rendering. Receives a Component and returns an object with html and head parameters.

```javascript
import { render } from 'svelte/server';
import App from './App.svelte';

const result = render(App, {	
  props: { some: 'property' }
});
```

## Summary

Svelte 5 removes life cycles such as `onMount`, `beforeUpdate`, `afterUpdate`, etc., and removes `createDispatcher`. The methods of components are passed through `$props`, so that we no longer need to pay attention to details such as `CustomEvent.detail`, which obviously reduces the learning curve. At the same time, the concept of Runes is introduced, which can control the data state with a more granular degree. Snippets are introduced, which can reuse page content at the Element level.

The article only introduces the more critical features. For more detailed content, interested readers are invited to explore in depth on the official website.

## Reference

*   [svelte-5-preview](https://svelte-5-preview.vercel.app/docs/introduction)
*   [runes](https://svelte.dev/blog/runes)