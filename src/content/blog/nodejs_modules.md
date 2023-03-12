---
author: Patrik Kaura
pubDatetime: 2023-03-14T21:15:00Z
title: 🧱 7. Node.js modules - From commonJS to ES6 modules
postSlug: node-modules
featured: false
draft: false
tags:
  - nodejs
  - web-development
ogImage: ""
description: From commonJS to ES6 modules
---

## Description

Node.js modules are the building blocks of any Node.js application. They are the basic units of code that are reusable throughout the application. Modules are the way to encapsulate the code and share it between different parts of the application.

## CommonJS modules

CommonJS modules are the first modules that were introduced in Node.js. They are the default modules in Node.js and are used in most of the applications. CommonJS modules are synchronous and are loaded into the memory when the application starts. They are loaded into the memory only once and are cached for the next time they are required.

```js
// module.js
module.exports.add = function (a, b) {
  return a + b;
};

module.exports.subtract = function (a, b) {
  return a - b;
};
```

```js
// app.js
const { add, subtract } = require("./util");

console.log(add(5, 5)); // 10
console.log(subtract(10, 5)); // 5
```

## ES6 modules

ES6 modules were introduced in Node.js 12.17.0 and are the new modules that are replacing the CommonJS modules. They are asynchronous and are loaded into the memory when they are required. They are loaded into the memory every time they are required.

```js
// module.mjs
export function add(a, b) {
  return a + b;
}

export function subtract(a, b) {
  return a - b;
}
```

```js
// app.mjs
import { add, subtract } from "./util";

console.log(add(5, 5)); // 10
console.log(subtract(10, 5)); // 5
```

To use ES6 modules in Node.js, you need to add the `--experimental-modules` flag to the `node` command or
set the `type` field to `module` in the `package.json` file. Other solution is to use the `.mjs` extension for the ES6 modules. Last alternative which is used in project with react is to use Babel to transpile the ES6 modules to CommonJS modules.

```
{
  "name": "my-library",
  "version": "1.0.0",
  "type": "module",

   ...
}
```

### ES6 vs CommonJS

![es6-vs-common-js](/assets/nodejs/commonjs-vs-esm.png)

| CommonJS                                           | ES6                                                 |
| -------------------------------------------------- | --------------------------------------------------- |
| Synchronous                                        | Asynchronous                                        |
| Loaded into the memory when the application starts | Loaded into the memory when they are required       |
| Loaded into the memory only once                   | Loaded into the memory every time they are required |
| Used in most of the applications                   | Used in new applications                            |
| Default modules in Node.js                         | New modules in Node.js                              |
| Used with `require`                                | Used with `import`                                  |
| Used with `.js` extension                          | Used with `.mjs` extension                          |

### Sources

- CommonJS vs. ES modules in Node.js - https://blog.logrocket.com/commonjs-vs-es-modules-node-js/
- CommonJS vs ESM - https://roadmap.sh/nodejs
