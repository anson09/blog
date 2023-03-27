---
order: 23
tags: vue
---

# Vue 3 Notable

## reactivity fundamentals

- Using reactive many times in origin or proxy object always return the same proxy object, while ref return a new proxy object when arguments is origin object each time, return the same proxy object when arguments is proxy object.
- > If an object is assigned as a ref's value, the object is made deeply reactive with reactive(). This also means if the object contains nested refs, they will be deeply unwrapped.

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

- Parent event not declared in child emits will be

1. binded to child root, acting as navite event (if any, `inheritAttrs: false` can disable this)
2. accessed by $attrs in child
3. triggered by child $emit

So, emit event may be trigger duplicated in item 1 and item 3. If which has been declared in child, only item 3 will happen

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
