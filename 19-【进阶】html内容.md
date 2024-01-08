```
{@html expression}
```

需要对html内容进行转义


Svelte不会在注入HTML之前转义表达式。如果数据来源不受信任，则必须对其进行转义，否则将用户暴露于XSS漏洞之中。
