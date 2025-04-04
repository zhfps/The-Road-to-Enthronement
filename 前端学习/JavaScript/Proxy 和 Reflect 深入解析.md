### Proxy 和 Reflect 深入解析
- #### 什么是 Proxy？
  
  `Proxy` 是 ES6 引入的一种对象，用于定义基本操作的自定义行为，例如属性查找、赋值、枚举、函数调用等。
  
  ```javascript
  const target = {
  message: "Hello, world!",
  };
  
  const handler = {
  get: function (target, property, receiver) {
    console.log(`Getting property: ${property}`);
    return target[property];
  },
  };
  
  const proxy = new Proxy(target, handler);
  console.log(proxy.message); // 输出: Getting property: message \n Hello, world!
  ```
- ##### 核心概念
  
  1. **目标对象 (Target)**: 被代理的对象。
  2. **处理程序 (Handler)**: 包含拦截操作的对象。
  3. **拦截器 (Traps)**: 定义自定义行为的函数。
- #### 常见的拦截器（Traps）
  
  
  | Trap                       | 描述                                         |
  | -------------------------- | -------------------------------------------- |
  | `get`                      | 拦截属性访问，如 `proxy.prop`                |
  | `set`                      | 拦截属性赋值，如 `proxy.prop = value`        |
  | `has`                      | 拦截 `in` 操作符                             |
  | `deleteProperty`           | 拦截 `delete` 操作符                         |
  | `apply`                    | 拦截函数调用，如 `proxy()`                   |
  | `construct`                | 拦截 `new` 操作符                            |
  | `defineProperty`           | 拦截 `Object.defineProperty`                 |
  | `getOwnPropertyDescriptor` | 拦截 `Object.getOwnPropertyDescriptor`       |
  | `ownKeys`                  | 拦截对象键操作，如 `Object.keys`、`for...in` |
- #### 示例：动态验证
  
  利用 `set` 实现属性赋值验证。
  
  ```javascript
  const user = {};
  
  const validator = {
  set: function (target, property, value) {
    if (property === "age") {
      if (!Number.isInteger(value) || value <= 0) {
        throw new Error("Age must be a positive integer");
      }
    }
    target[property] = value;
    return true;
  },
  };
  
  const proxyUser = new Proxy(user, validator);
  proxyUser.age = 25; // 正常
  console.log(proxyUser.age); // 25
  proxyUser.age = -5; // 抛出错误: Age must be a positive integer
  ```
  
  ------
- #### 什么是 Reflect？
  
  `Reflect` 是一个内置对象，提供拦截 JavaScript 操作的方法，与 Proxy 拦截器一一对应。它的设计目的是将语言级的操作函数化，使得这些操作更加一致。
  
  ```javascript
  const obj = { a: 1 };
  console.log(Reflect.get(obj, "a")); // 输出: 1
  Reflect.set(obj, "b", 2);
  console.log(obj.b); // 输出: 2
  ```
- ##### 常见方法
  
  | 方法                     | 描述                                           |
  | ------------------------ | ---------------------------------------------- |
  | `Reflect.get`            | 类似于 `obj[prop]`                             |
  | `Reflect.set`            | 类似于 `obj[prop] = value`                     |
  | `Reflect.has`            | 类似于 `prop in obj`                           |
  | `Reflect.deleteProperty` | 类似于 `delete obj[prop]`                      |
  | `Reflect.ownKeys`        | 类似于 `Object.keys(obj)`                      |
  | `Reflect.defineProperty` | 类似于 `Object.defineProperty`                 |
  | `Reflect.getPrototypeOf` | 类似于 `Object.getPrototypeOf(obj)`            |
  | `Reflect.setPrototypeOf` | 类似于 `Object.setPrototypeOf(obj, prototype)` |
  
  ------
- #### Proxy 与 Reflect 的结合
  
  `Proxy` 和 `Reflect` 通常结合使用，在代理中调用 `Reflect` 可以使代码更加清晰且具有一致性。
  
  
  ```javascript
  const target = {
  message: "Hello",
  };
  
  const handler = {
  get: function (target, property, receiver) {
    console.log(`Intercepted get: ${property}`);
    return Reflect.get(target, property, receiver);
  },
  set: function (target, property, value, receiver) {
    console.log(`Intercepted set: ${property} = ${value}`);
    return Reflect.set(target, property, value, receiver);
  },
  };
  
  const proxy = new Proxy(target, handler);
  proxy.message = "Hi"; // Intercepted set: message = Hi
  console.log(proxy.message); // Intercepted get: message \n Hi
  ```
- #### 应用场景
  
  1. **数据验证与安全性检查**: 在访问或修改数据时进行权限校验。
  
  
   ```javascript
   const bankAccount = {
     balance: 1000,
   };
   
   const handler = {
     get(target, property) {
       if (property === "balance") {
         throw new Error("Access denied");
       }
       return Reflect.get(target, property);
     },
   };
   
   const proxyAccount = new Proxy(bankAccount, handler);
   console.log(proxyAccount.balance); // 抛出错误: Access denied
   ```
  
  
  2. **日志与调试**: 通过拦截器记录对象操作日志。
  
  3. **虚拟化与模拟**: 用于创建模拟对象，拦截未定义属性。
  
  ------
- #### 注意事项
  
  1. **性能影响**: 使用 `Proxy` 会带来一些性能开销，特别是对于频繁操作的对象。
  2. **不可代理内置对象**: 某些内置对象（如 `Map` 和 `Set` 的原生方法）不支持完全代理。
  
  ------
- #### 常见面试题
  
  1. **什么是 Proxy？它的核心作用是什么？**
	- Proxy 是 ES6 提供的一种机制，用于拦截对象的基本操作并定义自定义行为。
	  2. **Proxy 的常见使用场景有哪些？**
	- 数据验证、安全性检查、调试与日志记录、虚拟化对象等。
	  3. **Reflect 与 Proxy 有什么关系？**
	- Reflect 提供了一组方法，与 Proxy 拦截器一一对应，通常用于在 Proxy 中调用默认行为。
	  4. **如何使用 Proxy 实现属性验证功能？**
	- 可以通过 `set` 拦截器检查赋值逻辑，例如验证某属性是否为正整数。
	  5. **列举几个 Proxy 的常见拦截器并描述其作用。**
	- `get`: 拦截属性读取。
	- `set`: 拦截属性赋值。
	- `has`: 拦截 `in` 操作。
	- `apply`: 拦截函数调用。
	  6. **Reflect.get 和直接访问对象属性有何不同？**
	- `Reflect.get` 是函数化的，能够更好地配合 Proxy 和自定义上下文。
	  7. **如何用 Proxy 实现对函数调用的拦截？**
	- 使用 `apply` 拦截器，可以在函数调用时注入自定义逻辑。
	  8. **Proxy 的性能是否会有影响？为什么？**
	- 是的，Proxy 会增加一定的性能开销，因为每次操作都需要通过拦截器处理。