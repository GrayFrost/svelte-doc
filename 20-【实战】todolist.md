首先我们需要定义页面的基本布局和基本的数据结构

ToDo：画个图

基本数据结构

```
interface ITodoItem {
  id: number;
  name: string;
  done: boolean;
}
```

基本交互：

* 在输入框中输入待办项内容，点击确认进行添加
* 在todo Tab会展示未完成的待办项，completed tab显示已完成的待办项，all tab显示所有添加过的待办项
* 在todo tab点击checkbox表示该项已完成，从todo list中移除，同时在completed tab中会显示刚才勾选的待办项，在completed tab中同理，可以取消勾线，从completed list中移除，同时重新再todo tab中出现。在all tab中只展示，不允许用户操作


样式笔者使用了tailwindcss，如果你对tailwindcss感兴趣，可自行了解。在讲解过程中，凡是涉及到tailwindcss设置的部分，笔者将会一笔带过，因为不是本教程的重点。


目录结构

入口文件index.svelte

todo item组件 item.svelte

数据存储文件 store.js
