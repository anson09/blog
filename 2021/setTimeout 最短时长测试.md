---
order: 9
---

# setTimeout 最短时长测试

```js
setTimeout(() => console.log(5), 5);
setTimeout(() => console.log(4), 4);
setTimeout(() => console.log(3), 3);
setTimeout(() => console.log(2), 2);
setTimeout(() => console.log(1), 1);
setTimeout(() => console.log(0), 0);
```

chrome(103) output：
1
0
2
3
4
5

safari(15.5) firefox(102) output：
0
1
2
3
4
5

结论：V8 最短时长 1ms，SpiderMonkey、Nitro 最短时长 0ms
