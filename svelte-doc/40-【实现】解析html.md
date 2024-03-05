## 解析标签
```javascript
function parseFragment() {
    return parseScript() ?? parseElement();
}
```

```javascript
function parseElement() {
	skipWhitespace();
	if (match('<')) {
	  eat('<');
	  const tagName = readWhileMatching(/[a-z]/);
	  eat('>');
	  const endTag = `</${tagName}>`;
	  const element = {
		type: 'Element',
		name: tagName,
		attributes: [],
		children: [],
	  };
	  eat(endTag);
	  skipWhitespace();
	  return element;
	}
}
```
attributes存储标签上的属性，children存储标签内的字标签对象。
因为我们parseElement中有返回一个对象，我们需要调整我们的parseFragments方法
```javascript
function parseFragments() {
    const fragments = [];
    while (i < content.length) {
      const fragment = parseFragment();
      if (fragment) {
        fragments.push(fragment);
      }
    }
    return fragments;
}
```
将parseFragments()返回值赋给ast.html
```javascript
function parse(content) {
  console.log('parse')
  let i = 0;
  const ast = {};
  ast.html = parseFragments();
  return ast;

  ...
}
```

## generate

接着我们开始完善generate的方法
```javascript
function generate(ast) {
  const code = {
    variables: [],
    create: [],
    destroy: [],
  };
  
  let counter = 1;

  function traverse(node, parent) {
    switch(node.type) {
      case 'Element': {
        const variableName = `${node.name}_${counter++}`;
        code.variables.push(variableName);        
        code.create.push(
          `${variableName} = element('${node.name}')`
        );
        code.create.push(`append(${parent}, ${variableName})`);
        code.destroy.push(`detach(${variableName})`);        
        break;
      }
    }
  }

  ast.html.forEach((fragment) => traverse(fragment, 'target'));

  return `
    ...
    
    export default function() {
	    ...
    }
  `;
}
```

我们定义一个变量code，里面用来收集在解析过程中的各种用于转换的参数。在这一章，我们创建create变量用量存储组件创建时的数据，destroy用来存储组件销毁时的数据。variables数组用来存储在编译时用来声明的变量。

ast.html中存储的是一个数据，遍历这个数组，然后对数组里的内容作转换。
因为目前我们只解析了标签，所以我们只处理标签的转换。还记得我们在基本流程章节中定义的运行时方法吗，这里便派上了用场：element、append、detach。
经过转换后，我们的create和destroy中已经存储了组件在创建时如何运行和销毁时如何运行的js代码。
现在我们重构字符串模板中的导出内容。
```javascript
function generate(ast) {
	...
	return `
		export default function() {
		${code.variables.map(v => `let ${v};`).join('\n')}
		
		${escodegen.generate(ast.script)}

			var lifecycle = {
		        create(target) {
		          ${code.create.join('\n')}
		        },
		        destroy(target) {
		          ${code.destroy.join('\n')}
		        }
			};
		    return lifecycle;
	    }	
    `
}
```

## 解析纯文本

```javascript
function parseFragment() {
    return parseScript() ?? parseElement() ?? parseText();
}
```

```javascript
function parseText() {
    const text = readWhileMatching(/[^<{]/);
    if (text.trim() !== '') {
      return {
        type: 'Text',
        value: text.trim(),
      }
    }
}
```

完善generate里的traverse方法，添加对Text类型的转换
```javascript
function traverse(node, parent) {
    switch(node.type) {
      case 'Element': {
        ...
      }
      case 'Text': {
        const variableName = `txt_${counter++}`;
        code.variables.push(variableName);
        code.create.push(`${variableName} = text('${node.value}');`);
        code.create.push(`append(${parent}, ${variableName})`);
        code.destroy.push(`detach(${variableName})`);
        break;
      }
    }
}
```

```html
<script>
  let msg = '解析文本';
</script>

文本内容1
<div></div>
文本内容2
```
看看`npm run compile`后的结果吧。
![[40-1.png]]
现在如果我们往div标签里添加内容，编译往往会报错。因为还没有实现递归解析html内容的逻辑。这也是为什么笔者在这里只演示了最基本的html标签的写法。

## 解析标签属性

修改parseElement的内容
```diff
function parseElement() {
    skipWhitespace();
    if (match('<')) {
      eat('<');
      const tagName = readWhileMatching(/[a-z]/);
+     const attributes = parseAttributes();
      eat('>');
      const endTag = `</${tagName}>`;
      const element = {
        type: 'Element',
        name: tagName,
-       attributes: [],
+       attributes,
        children: [],
      };
      eat(endTag);
      skipWhitespace();
      return element;
    }
}
```

```javascript
  function parseAttributes() {
    skipWhitespace();
    const attributes = [];
    while(!match('>')) {
      attributes.push(parseAttribute());
      skipWhitespace();
    }
    return attributes;
  }

  function parseAttribute() {
    const name = readWhileMatching(/[^=]/);
    if (match('={')) {
      eat('={');
      const value = parseJavaScript();
      eat('}');
      return {
        type: 'Attribute',
        name,
        value,
      };
    }
  }
```

## 行内表达式
```javascript
function parseJavaScript() {
    const js = acorn.parseExpressionAt(content, i, { ecmaVersion: 2023 });
    i = js.end;
    return js;
}
```

完善generate里的traverse方法
```javascript
function traverse(node, parent) {
    switch(node.type) {
      case 'Element': {
        ...
        node.attributes.forEach(attribute => {
          traverse(attribute, variableName);
        });
      }
      case "Attribute": {
        if (node.name.startsWith("on:")) {
          const eventName = node.name.slice(3);
          const eventHandler = node.value.name;
          const eventNameCall = `${eventName}_${counter++}`;
          code.variables.push(eventNameCall);
          code.create.push(
            `${eventNameCall} = listen(${parent}, "${eventName}", ${eventHandler})`
          );
          code.destroy.push(`${eventNameCall}()`);
        }
        break;
      }
    }
  }
  ```
在开篇时，笔者已和大家说明了，本次实现我们只实现其中的事件绑定。
因为我们对addEventListener做了封装，当我们执行listen()方法后，会返回一个用于移除事件监听的方法。我们在destroy阶段调用这个方法。

修改App.svelte的内容
```html
<script>
  let count = 0;
  const updateCount = () => {
    count++;
    console.log('update count', count);
  }
</script>

文本内容1
<button on:click={updateCount}></button>
文本内容2
```
因为现在仍不支持标签内添加元素，因此，我们单独在index.html内给button添加样式，方便我们测试
```html
<style>
  button {
	width: 100px;
	height:100px;
	background: orange;
  }
</style>
```

执行`npm run compile`看一下效果吧。
![[40-2.gif]]

## 完整代码
按照惯例，本章最后附上代码：
```javascript
import * as fs from "fs";
import { fileURLToPath } from "url";
import { dirname, resolve } from "path";
import * as acorn from "acorn";
import * as escodegen from "escodegen";

const modulePath = dirname(fileURLToPath(import.meta.url));

function bootstrap() {
  try {
    const inputPath = resolve(modulePath, "./App.svelte");
    const outputPath = resolve(modulePath, "./app.js");
    const content = fs.readFileSync(inputPath, "utf-8");
    fs.writeFileSync(outputPath, compile(content), "utf-8");
  } catch (e) {
    console.error(e);
  }
}

function compile(content) {
  const ast = parse(content); // 解析svelte文件内容成ast
  return generate(ast);
}

function parse(content) {
  let i = 0;
  const ast = {};
  ast.html = parseFragments();
  
  return ast;

  function parseFragments() {
    const fragments = [];
    while (i < content.length) {
      const fragment = parseFragment();
      if (fragment) {
        fragments.push(fragment);
      }
    }
    return fragments;
  }

  function parseFragment() {
    return parseScript() ?? parseElement() ?? parseText();
  }

  function parseScript() {
    skipWhitespace();
    if (match("<script>")) {
      eat("<script>");
      const startIndex = i;
      const endIndex = content.indexOf("</script>", i);
      const code = content.slice(startIndex, endIndex);
      ast.script = acorn.parse(code, { ecmaVersion: 2023 });
      i = endIndex;
      eat("</script>");
      skipWhitespace();
    }
  }

  function parseElement() {
    skipWhitespace();
    if (match('<')) {
      eat('<');
      const tagName = readWhileMatching(/[a-z]/);
      const attributes = parseAttributes();
      eat('>');
      const endTag = `</${tagName}>`;
      const element = {
        type: 'Element',
        name: tagName,
        attributes,
        children: [],
      };
      eat(endTag);
      skipWhitespace();
      return element;
    }
  }

  function parseAttributes() {
    skipWhitespace();
    const attributes = [];
    while(!match('>')) {
      attributes.push(parseAttribute());
      skipWhitespace();
    }
    return attributes;
  }

  function parseAttribute() {
    const name = readWhileMatching(/[^=]/);
    if (match('={')) {
      eat('={');
      const value = parseJavaScript();
      eat('}');
      return {
        type: 'Attribute',
        name,
        value,
      };
    }
  }

  function parseJavaScript() {
    const js = acorn.parseExpressionAt(content, i, { ecmaVersion: 2023 });
    i = js.end;
    return js;
  }

  function parseText() {
    const text = readWhileMatching(/[^<{]/);
    if (text.trim() !== '') {
      return {
        type: 'Text',
        value: text.trim(),
      }
    }
  }

  function match(str) {
    return content.slice(i, i + str.length) === str;
  }

  function eat(str) {
    if (match(str)) {
      i += str.length;
    } else {
      throw new Error(`Parse error: expecting "${str}"`);
    }
  }

  function readWhileMatching(reg) {
    let startIndex = i;
    while(i < content.length && reg.test(content[i])) {
      i++;
    }
    return content.slice(startIndex, i);
  }

  function skipWhitespace() {
    readWhileMatching(/[\s\n]/);
  }
}

function generate(ast) {

  const code = {
    variables: [],
    create: [],
    destroy: [],
  };

  let counter = 1;

  function traverse(node, parent) {
    switch(node.type) {
      case 'Element': {
        const variableName = `${node.name}_${counter++}`;
        code.variables.push(variableName);
        code.create.push(
          `${variableName} = element('${node.name}')`
        )
        node.attributes.forEach(attribute => {
          traverse(attribute, variableName);
        });

        code.create.push(`append(${parent}, ${variableName})`);
        code.destroy.push(`detach(${variableName})`);
        break;
      }
      case 'Text': {
        const variableName = `txt_${counter++}`;
        code.variables.push(variableName);
        code.create.push(`${variableName} = text('${node.value}');`);
        code.create.push(`append(${parent}, ${variableName})`);
        code.destroy.push(`detach(${variableName})`);
        break;
      }
      case "Attribute": {
        if (node.name.startsWith("on:")) {
          const eventName = node.name.slice(3);
          const eventHandler = node.value.name;
          const eventNameCall = `${eventName}_${counter++}`;
          code.variables.push(eventNameCall);
          code.create.push(
            `${eventNameCall} = listen(${parent}, "${eventName}", ${eventHandler})`
          );
          code.destroy.push(`${eventNameCall}()`);
        }
        break;
      }
    }
  }

  ast.html.forEach((fragment) => traverse(fragment, 'target'));

  return `
    function element(name) {
      return document.createElement(name);
    }

    function text(data) {
      return document.createTextNode(data);
    }

    function append(target, node) {
      target.appendChild(node);
    }

    function detach(node) {
      if (node.parentNode) {
        node.parentNode.removeChild(node);
      }
    }

    export function listen(node, event, handler) {
      node.addEventListener(event, handler);
      return () => node.removeEventListener(event, handler);
    }
    
    export default function() {
      ${code.variables.map(v => `let ${v};`).join('\n')}

      ${escodegen.generate(ast.script)}

      var lifecycle = {
        create(target) {
          ${code.create.join('\n')}
        },
        destroy(target) {
          ${code.destroy.join('\n')}
        }
      };
      return lifecycle;
    }
  `;
}

bootstrap();
```


## 小结