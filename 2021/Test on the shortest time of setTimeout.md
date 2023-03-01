---
order: 9
tags: browser
---

# Test on the shortest time of setTimeout

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

conclusion：the shortest time of V8 is 1ms，the shortest time of SpiderMonkey、Nitro is 0ms

Update:

chrome(110) is outputing the same as safari now. While nodejs(18) is still the above outputing，btw setTimeout is implemented by runtime rather than v8
