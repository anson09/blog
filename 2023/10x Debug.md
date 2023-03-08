---
order: 26
tags: debug
---

# 10x Debug

vscode debugger 和 chrome devtools 都是 client, 分别通过 DAP（TCP） 和 CDP（websocket） 协议与各自 server 端使用 json 对象通信。

- [DAP](https://microsoft.github.io/debug-adapter-protocol/overview) 用了适配器模式，在 server 端去完成各种语言的 debugger 对接
- [CDP](https://chromedevtools.github.io/devtools-protocol/) 是控制 chrome 的底层协议，浏览器本身作为 server 端，debugger 只是其中一个模块。从 devtool 的 protocol monitor 可以看到全部通讯消息，devtool 和 puppeteer（封装了一套上层的协议）的实现都建立在这之上

node 的 debugger 直接使用了 v8-inspector protocol，属于 CDP 子集，所以 devtool 可以直接连上 node runtime，而 vscode 通过 DAP server 完成 v8-inspector protocol 对接

📎 附 [vscode node debug usage](https://code.visualstudio.com/docs/nodejs/nodejs-debugging)

一些补充 tips：

- node 的 `--inspect(-brk)` 参数需在 file_path 之前
- js debugger 语句在调试模式中可代替普通断点
