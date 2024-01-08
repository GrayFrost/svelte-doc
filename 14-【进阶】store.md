store

context 与store


每当引用 store时，都可以通过在其前面加上 `$`字符来访问其在component内部的值，这会使Svelte自动声明前缀变量，并设置将在适当时取消store subscription

```javascript
<script>
	import { writable } from 'svelte/store';

	const count = writable(0);
	console.log($count); // logs 0

	count.set(1);
	console.log($count); // logs 1

	$count = 2;
	console.log($count); // logs 2
</script>
```


store derived 相当于selector
