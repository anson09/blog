---
order: 28
tags: js
---

# This Refer

_code in module and class enable strict mode by default_

## browser

- `globalThis` **always** refer to `window`
- `this` in global within **strict or non-strict** mode refer to **`window`**
- `this` in non-arrow function refer to **`window`** within non-strict mode, refer to **`undefined`** within strict mode
- `this` in arrow function refer to it **parent domain** `this`
- variable declared with `var` in global is seted to `window`
- variable defined without declaring is seted to `window` within non-strict mode, and will **throw error** within strict mode

## nodejs

- `globalThis` **always** refer to `global`
- `this` in global within **strict or non-strict** mode refer to **plain object {}**, **notice: not global object**
- `this` in non-arrow function refer to **`global`** within non-strict mode, refer to **`undefined`** within strict mode
- `this` in arrow function refer to it **parent domain** `this`
- variable declared with `var` in global is **not** seted to global
- variable defined without declaring is seted to `global` within non-strict mode, and will **throw error** within strict mode
