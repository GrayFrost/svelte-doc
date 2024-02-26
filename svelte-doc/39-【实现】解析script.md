## 解析fragment
ast.html ast.script
```javascript
function parse(content) {
  let i = 0;
  const ast = {};
  parseFragments();
  return ast;

  ...
}
```

parseFragments的内容如下

```javascript
function parseFragments() {
	while (i < content.length) { // 因为i是从0开始的，所以使用<比较即可
	  parseFragment();
	}
}

function parseFragment() {
    return parseScript();
}
```

因为本章我们讲解的是解析script，所以parseFragment内部只有一个parseScript方法。

## acorn
引入acorn
```bash
npm install acorn -D
```

```javascript
import * as fs from "fs";
import { fileURLToPath } from "url";
import { dirname, resolve } from "path";
import * as acorn from "acorn";

...
```

parseScript内容如下：
```javascript
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
```

读取script标签内的内容，使用acorn对读取到的字符串内容进行解析，然后把解析出来的script内容存到ast.script变量中

## escodegen

引入escodegen
```bash
npm install escodegen -D
```

```javascript
import * as fs from "fs";
import { fileURLToPath } from "url";
import { dirname, resolve } from "path";
import * as acorn from "acorn";
import * as escodegen from "escodegen";

...
```

将ast.script变量的内容重新生成出来。在generate方法返回的模板字符串中
```javascript
function generate(ast) {
  return `
    ...
    
    export default function() {
      ${escodegen.generate(ast.script)}
      return {
        create(target) {
          ...
        }
      }
    }
  `;
}
```

现在让我们在App.svelte中添加一个script标签，在里面定义一些内容，然后`npm run compile`解析一下吧。
```html
<script>
  let msg = '解析script';
</script>
```

相信你能看到在app.js中有以下内容
```javascript
export default function() {
  let msg = '解析script';
  return {
	create(target) {
	  const div = document.createElement('div');
	  div.textContent = 'hello svelte';
	  target.appendChild(div);
	}
  }
}
```
如果读者们想试验一下，甚至能够把我们在上一节在create方法中的内容做一个更改，
```diff
create(target) {
    const div = document.createElement('div');
-   div.textContent = 'hello svelte'
+   div.textContent = msg;
    target.appendChild(div);
}
```

重新`npm run compile`后便能得到：
```javascript
export default function() {
  let msg = '解析script';
  return {
	create(target) {
	  const div = document.createElement('div');
	  div.textContent = msg;
	  target.appendChild(div);
	}
  }
}
```
访问localhost:5173
![[Pasted image 20240226104315.png]]

最后，为了防止读者们在跟着步骤来实现时出现代码混淆的情况，在每一章结束后，笔者都会把本章实现后的svelte.js的内容呈现出来，读者可一一比对实现。

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
  parseFragments();
  return ast;

  function parseFragments() {
    while (i < content.length) {
      const fragment = parseFragment();
    }
  }

  function parseFragment() {
    return parseScript();
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
      ${escodegen.generate(ast.script)}
      return {
        create(target) {
          const div = document.createElement('div');
          div.textContent = msg;
          target.appendChild(div);
        }
      }
    }
  `;
}

bootstrap();
```

## 小结