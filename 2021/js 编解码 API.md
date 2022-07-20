---
order: 12
---

# js 编解码 API

- 常用汉字 utf-16 2 字节可以覆盖，utf-8 大部分 3 字节

- unicode 编码解码  
  `str.charCodeAt(idx) <--> String.fromCharCode(code)` BMP 适用，超出要用代理对换算  
  `str.codePointAt(idx) <--> String.fromCodePoint(code)`，idx=0 取到实际 unicode  
  `escape(str) <--> unescape(code)` 已废弃

- unicode 转译  
  `'\u{xxxxx}'` 有括号可长于四位 0x 数，否则只能使用 4 位('\u0073',前面需要补 0 这种)，超出用代理对

- 英文字符外的 utf-8 编解码  
  `encodeURI(URI) <--> decodeURI(encodeURI)` url 使用的字符不处理  
  `encodeURIComponent(str) <--> decodeURIComponent(encodeURI)`

- utf-8 编码  
  `new TextEncoder().encode(string)`

- 任意进制转十进制  
  `parseInt(string,radix)`

- 十进制转任意进制  
  `number.toString(radix)`

- base64 编解码  
  `btoa(stringToEncode) <--> atob(encodedData)`

- string.length 结果是 utf-16 编码的长度，字符个数使用 [...string].length 计算

---

js string（utf-16 编码） 和 binary string(string 子集，范围 00-FF，用于代表 uint) 互转

```js
function toBinary(string) {
  const codeUnits = new Uint16Array(string.length);
  for (let i = 0; i < codeUnits.length; i++) {
    codeUnits[i] = string.charCodeAt(i);
  }
  const charCodes = new Uint8Array(codeUnits.buffer);
  let result = "";
  for (let i = 0; i < charCodes.byteLength; i++) {
    result += String.fromCharCode(charCodes[i]);
  }
  return result;
}
```

```js
function fromBinary(binary) {
  const bytes = new Uint8Array(binary.length);
  for (let i = 0; i < bytes.length; i++) {
    bytes[i] = binary.charCodeAt(i);
  }
  const charCodes = new Uint16Array(bytes.buffer);
  let result = "";
  for (let i = 0; i < charCodes.length; i++) {
    result += String.fromCharCode(charCodes[i]);
  }
  return result;
}
```

---

get query 或者 post body(content-type: application/x-www-form-urlencoded)

```js
decodeURIComponent(
  Qs.stringify({
    a: 1,
    a: 2,
    b: [1, 2, 3],
    c: { d: 1 },
    e: [{ f: 1 }],
    g: { h: [1] },
  })
);
-> 'a=2&b[0]=1&b[1]=2&b[2]=3&c[d]=1&e[0][f]=1&g[h][0]=1'
```

用 URLSearchParams 的话,set 进去的值是先调用 value 自身的 toString()方法, 再调用 encodeURIComponent

---

0x10 010 0b10
