# JavaScript 模块深入解析

模块化是现代 JavaScript 的一个核心概念，它使得代码更具结构性、可维护性，并且支持复用。以下将深入探讨 JavaScript 中的模块特性及其相关机制。

---
- ## 什么是模块？
  模块是独立的代码单元，每个模块都拥有自己的作用域，并且通过 `export` 和 `import` 来与其他模块交互。模块的目标是解决传统脚本文件容易造成的全局变量污染和依赖混乱问题。
- ### 模块的核心特性：
  1. **独立作用域**：模块内的变量默认是局部变量，避免了全局污染。
  2. **严格模式**：所有模块自动运行在严格模式下，无需显式声明 `'use strict'`。
  3. **延迟加载**：模块会按需加载，提高性能。
  4. **静态分析**：模块依赖在编译时已知，优化工具可以更高效地进行树摇（Tree-shaking）。
  
  ---
- ## 模块的使用方法
- ### 导出（Export）
  模块通过 `export` 将变量、函数或类暴露给外部使用。
- #### 命名导出（Named Export）
  
  ```javascript
  // math.js
  export const add = (a, b) => a + b;
  export const multiply = (a, b) => a * b;
  
  ```
- #### 默认导出（Default Export）
  
  ```javascript
  // logger.js
  export default function log(message) {
  console.log(message);
  }
  
  ```
  
  > 注意：一个模块只能有一个默认导出，但可以有多个命名导出。
- ### 导入（Import）
  通过 `import` 从其他模块中引入变量或函数。
- ### 导入命名导出
  
  ```javascript
  import { add, multiply } from './math.js';
  console.log(add(2, 3));
  
  ```
- ### 导入默认导出
  
  ```javascript
  import log from './logger.js';
  log('This is a default export');
  
  ```
- ### 导入重命名
  
  ```javascript
  import { add as sum } from './math.js';
  console.log(sum(2, 3));
  
  ```
- ### 导入所有内容
  
  ```javascript
  import * as math from './math.js';
  console.log(math.add(2, 3));
  console.log(math.multiply(4, 5));
  
  ```
  
  ---
- #### 动态导入（Dynamic Import）
  动态导入允许在运行时加载模块。这种方式返回一个 Promise，可以用于按需加载或条件加载。
  
  ```javascript
  if (condition) {
  import('./module.js')
    .then(module => {
      module.someFunction();
    })
    .catch(err => console.error(err));
  }
  ```
  
  动态导入的典型应用场景：
- 延迟加载资源
- 实现代码分割
- 根据用户行为加载特定功能
  
  ---
- ## 浏览器中的模块
  
  在浏览器环境中，模块化通过 `<script type="module">` 实现。
  
  ```html
  <script type="module">
  import { add } from './math.js';
  console.log(add(2, 3));
  </script>
  ```
- ### 浏览器模块的特点
  1. **默认延迟加载**：模块脚本是延迟加载的，类似于给 `<script>` 添加了 `defer` 属性。
  2. **跨域限制**：模块文件必须与 HTML 位于相同的源（或提供适当的 CORS 头）。
  
  ---
- ## Node.js 中的模块
  
  Node.js 提供两种模块系统：
  1. **CommonJS**（`require`/`module.exports`）
  2. **ES Modules**（`import`/`export`）
  
  从 Node.js 13 开始，默认支持 ES Modules，但需满足以下条件之一：
- 文件扩展名为 `.mjs`
- `package.json` 中指定 `"type": "module"`
- ###### CommonJS 示例
  
  ```javascript
  // math.js
  module.exports = {
  add: (a, b) => a + b,
  multiply: (a, b) => a * b,
  };
  ```
  
  ```javascript
  const math = require('./math.js');
  console.log(math.add(2, 3));
  ```
- ###### ES Modules 示例
  
  ```javascript
  // math.mjs
  export const add = (a, b) => a + b;
  ```
  
  ```javascript
  import { add } from './math.mjs';
  console.log(add(2, 3));
  ```
  
  ---
- ## Tree-Shaking
  Tree-Shaking 是一种通过静态分析移除未使用代码的技术。
- ##### 示例：
  
  ```javascript
  // utils.js
  export const used = () => console.log('Used function');
  export const unused = () => console.log('Unused function');
  ```
  
  在只使用 `used` 时，现代构建工具（如 Webpack 或 Rollup）会移除未引用的 `unused`。构建结果如下：
  
  ```javascript
  export const used = () => console.log('Used function');
  ```
  
  ---
- #### 常见的面试题
  
  1. **JavaScript 模块的优势是什么？**
	- 代码复用性高，维护方便。
	- 避免全局作用域污染。
	- 提升性能（如延迟加载和 Tree-Shaking）。
	  
	  2. **如何区分 CommonJS 和 ES Modules？**
	- CommonJS 使用 `require` 和 `module.exports`，同步加载。
	- ES Modules 使用 `import` 和 `export`，支持异步加载。
	  
	  3. **什么是 Tree-Shaking？如何实现？**
	- Tree-Shaking 是一种移除未使用代码的优化技术，通过静态分析实现。需要使用支持模块化的构建工具（如 Webpack）。
	  
	  4. **动态导入的作用和应用场景？**
	- 动态导入可以实现按需加载、代码分割，常用于加载大型库或根据用户操作加载特定功能。
	  
	  5. **浏览器模块和 Node.js 模块的主要区别是什么？**
	- 浏览器模块需要跨域许可（CORS），Node.js 模块支持文件系统模块加载。
	  
	  6. **如何解决模块跨域问题？**
	- 配置服务器响应 CORS 头部，例如 `Access-Control-Allow-Origin`。
	  
	  7. **模块中默认导出与命名导出的区别？**
	- 默认导出每个模块只能有一个，而命名导出可以有多个。