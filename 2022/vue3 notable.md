---
order: 23
tags: vue
---

# vue 3 notable

## reactivity fundamentals

- Use reactive many times in origin or proxyed object always return the same proxy object, but ref always return a new proxy object
- > If an object is assigned as a ref's value, the object is made deeply reactive with reactive(). This also means if the object contains nested refs, they will be deeply unwrapped.
- Ref unwrappping **is not** reactive unwrappping, example:

```js
import { ref, reactive, toRaw } from "vue";

const proxy = reactive({});

proxy.ref = ref("");
proxy.reactive = {};

console.log(proxy.ref === toRaw(proxy.ref)); // true
console.log(proxy.reactive === toRaw(proxy.reactive)); // false
```

- Set noRefValue / Ref to a reactive object

```js
const count = ref(0);
const state = reactive({ count });

console.log(state.count); // 0

state.count = 1; // inner equal to state.count.value = noRefValue
console.log(count.value); // 1

const otherCount = ref(2);

state.count = otherCount; // inner equal to state.count = Ref
console.log(state.count); // 2
// original ref is now disconnected from state.count
console.log(count.value); // 1
```

## fallthrough attributes

- Parent event which is not declared in child emits will be binded to child root (if any), and can be transfered by $attrs as well as be triggered by child $emit. If which has been declared in child, it can only be triggered by child $emit.
- class、style and v-on event will be merged, while other attributes parent overrides child in single root mode

## pinia

### [`storeToRefs`](https://pinia.vuejs.org/api/modules/pinia.html#storetorefs)

Store export a reactive object, destructure items from this store will loss reactivity (even if the item is a ref, cause ref unwrappping), you could use `storeToRefs`, it like toRefs, but will ignore methods and non reactive properties

### replaceing the state

> ```js
> // this doesn't actually replace `$state`
> store.$state = { count: 24 };
> // it internally calls `$patch()`:
> store.$patch({ count: 24 });
> ```

If you want to replace the state, use `$reset` instead of assigning directly. By default this api only works on options type store, if you choose composition type, you need implement this function by a pinia plugin, there is an [example](https://github.com/anson09/crystal/blob/dev/src/store/plugins/reset.js)
