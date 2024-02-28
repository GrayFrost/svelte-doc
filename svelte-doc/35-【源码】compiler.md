经过了前两章的铺垫，我们正式开始对源码进行解读。到笔者目前写此文章时（2024-02-28），Svelte的最新版本是4.2.12
![[Pasted image 20240228161643.png]]

笔者不会对源码过于细致的讲解，因为如果需要对每个细节都进行解读，那完全可以再开一本小册单独讲解了。

TODO: https://zhuanlan.zhihu.com/p/409291132

把项目下载下来，切换到4.2.12分支。

```bash
git clone git@github.com:sveltejs/svelte.git
```

![[Pasted image 20240228162645.png]]
在compiler目录下
compile
parse
preprocess

### compile
packages/svelte/src/compiler/compile/index.js
```javascript
/**
 * `compile` takes your component source code, and turns it into a JavaScript module that exports a class.
 *
 * https://svelte.dev/docs/svelte-compiler#svelte-compile
 * @param {string} source
 * @param {import('../interfaces.js').CompileOptions} options
 */
export default function compile(source, options = {}) {
	const ast = parse(source, options);
	const component = new Component(
		ast,
		source,
		options.name || get_name_from_filename(options.filename) || 'Component',
		options,
	);
	const result = render_dom(component, options);
	return component.generate(result);
}
```
我们去掉不相关的代码，可以看到基本的compile逻辑。

通过解读svelte文件的字符串内容，得到ast，将ast转成Component类，然后render dom，最后generate。

### parse
![[Pasted image 20240228164257.png]]
```javascript
export default function parse(template, options = {}) {
	const parser = new Parser(template, options);
	
	const instance_scripts = parser.js.filter((script) => script.context === 'default');
	const module_scripts = parser.js.filter((script) => script.context === 'module');

	return {
		html: parser.html,
		css: parser.css[0],
		instance: instance_scripts[0],
		module: module_scripts[0]
	};
}
```

#### Parser

##### fragment
```javascript
import tag from './tag.js';
import mustache from './mustache.js';
import text from './text.js';

/**
 * @param {import('../index.js').Parser} parser
 */
export default function fragment(parser) {
	if (parser.match('<')) {
		return tag;
	}

	if (parser.match('{')) {
		return mustache;
	}

	return text;
}
```
##### tag

##### mustache

##### text


### Component
#### Fragment
```javascript
export default class Fragment extends Node {
	/** @type {import('../render_dom/Block.js').default} */
	block;

	/** @type {import('./interfaces.js').INode[]} */
	children;

	/** @type {import('./shared/TemplateScope.js').default} */
	scope;

	/**
	 * @param {import('../Component.js').default} component
	 * @param {import('../../interfaces.js').TemplateNode} info
	 */
	constructor(component, info) {
		const scope = new TemplateScope();
		super(component, null, scope, info);
		this.scope = scope;
		this.children = map_children(component, this, scope, info.children);
	}
}
```
map_children
```javascript
export default function map_children(component, parent, scope, children) {
	let last = null;
	let ignores = [];
	return children.map((child) => {
		const constructor = get_constructor(child.type);
		const use_ignores = child.type !== 'Text' && child.type !== 'Comment' && ignores.length;
		if (use_ignores) component.push_ignores(ignores);
		const node = new constructor(component, parent, scope, child);
		if (use_ignores) component.pop_ignores(), (ignores = []);
		if (node.type === 'Comment' && node.ignores.length) {
			push_array(ignores, node.ignores);
		}
		if (last) last.next = node;
		node.prev = last;
		last = node;
		return node;
	});
}
```

get_constructor经过new得到一个node对象
```javascript
function get_constructor(type) {
	switch (type) {
		case 'AwaitBlock':
			return AwaitBlock;
		case 'Body':
			return Body;
		case 'Comment':
			return Comment;
		case 'ConstTag':
			return ConstTag;
		case 'Document':
			return Document;
		case 'EachBlock':
			return EachBlock;
		case 'Element':
			return Element;
		case 'Head':
			return Head;
		case 'IfBlock':
			return IfBlock;
		...
		default:
			throw new Error(`Not implemented: ${type}`);
	}
}```
### render_dom
#### Renderer

## 小结