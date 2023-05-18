---
order: 2
tags: browser
---

# How Defer Influences Rendering Process

`index.html`

```html
<!DOCTYPE html>
<html>
  <head>
    <link
      rel="stylesheet"
      href="https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css"
      integrity="sha384-HSMxcRTRxnN+Bdg0JdbxYKrThecOKuH5zCYotlSAcp1+c8xmyTe9GYg1l9a69psu"
      crossorigin="anonymous"
    />
  </head>

  <body>
    <script src="./main.js" defer></script>
    <div id="app">hello</div>
  </body>

  <style media="screen">
    #app {
      width: 100px;
      height: 100px;
      border: red 1px solid;
    }
  </style>
</html>
```

`main.js`

```js
console.log(1);
```

add network throttle setting

![image](../assets/images/2018-2-1.png)

without defer

![image](../assets/images/2018-2-2.png)

with defer

![image](../assets/images/2018-2-3.png)

normally, dom and css parse parallel，but when mixin js：

- without defer case，dom parse wait script, script wait css parse
- with defer case, dom parse don't wait js excute, and js excute don't wait css parse
- the defer attribute is ignored if the `<script>` tag has no `src`
- all script will start downloading before dom is parsed, because a preanalysis. Specific start time of preanalysis resources download is influenced by the priority of resource
- use preload or fetchpriority is anothor topic

event flow: readyState:interactive-> deferjs -> DOMContentLoaded -> load img/iframe... -> dreadyState:complete -> onLoad

by the way,
async scripts don't wait anything else, and nothing wait async script, except it's excuting may block dom parse.
dynamically inserted script is async by default, if we need certain order, set `script.async = false`.
module script is defer by default
