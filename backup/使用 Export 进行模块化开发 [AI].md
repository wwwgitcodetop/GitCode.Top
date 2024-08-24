在现代 JavaScript 开发中，模块化编程是非常重要的一部分；它可以帮助我们将代码分成独立的模块，使得代码更加清晰、可维护、可重用；在这个教程中，我们将介绍如何使用 `export` 关键字将代码从一个模块导出，并在其他模块中使用 `import` 关键字导入这些代码。

## 1. 什么是 `export` 和 `import`

在 JavaScript 中，`export` 关键字用于将变量、函数、类或对象从一个模块中导出。导出的内容可以在其他模块中通过 `import` 关键字导入并使用。

### 1.1 命名导出（Named Export）

命名导出允许你从一个模块中导出多个内容。在导入时，需要使用导出的名称进行引用。

**示例**:

```javascript
// utils.js

export function add(a, b) {
  return a + b;
}

export const PI = 3.14159;
```

在上面的示例中，我们在 `utils.js` 文件中导出了一个函数 `add` 和一个常量 `PI`。

**在其他文件中使用**:

```javascript
// main.js

import { add, PI } from './utils.js';

console.log(add(2, 3)); // 输出: 5
console.log(PI); // 输出: 3.14159
```

### 1.2 默认导出（Default Export）

默认导出允许你从模块中导出一个默认值。一个模块中只能有一个默认导出。在导入时，可以为这个默认导出内容指定任意名称。

**示例**:

```javascript
// utils.js

export default function subtract(a, b) {
  return a - b;
}
```

在上面的示例中，我们将 `subtract` 函数作为默认导出。

**在其他文件中使用**:

```javascript
// main.js

import subtract from './utils.js';

console.log(subtract(5, 3)); // 输出: 2
```

### 1.3 混合使用命名导出和默认导出

你可以在一个模块中同时使用命名导出和默认导出。

**示例**:

```javascript
// utils.js

export const multiply = (a, b) => a * b;

export default function divide(a, b) {
  return a / b;
}
```

**在其他文件中使用**:

```javascript
// main.js

import divide, { multiply } from './utils.js';

console.log(multiply(2, 3)); // 输出: 6
console.log(divide(6, 2)); // 输出: 3
```

## 2. 创建 `utils.js` 文件

让我们创建一个 `utils.js` 文件，其中包含常用的实用函数，并将它们导出以便在其他模块中使用。

```javascript
// utils.js

export function add(a, b) {
  return a + b;
}

export function subtract(a, b) {
  return a - b;
}

export const PI = 3.14159;

export default function multiply(a, b) {
  return a * b;
}

// export { add, subtract, PI, multiply };
```

在这个文件中，我们导出了三个命名导出：`add`、`subtract` 和 `PI`，以及一个默认导出 `multiply` 函数。

## 3. 在其他文件中使用 `utils.js`

现在，我们可以在其他文件中导入 `utils.js` 中的函数和常量，并使用它们。

```javascript
// main.js

import multiply, { add, subtract, PI } from './utils.js';

console.log(add(10, 5)); // 输出: 15
console.log(subtract(10, 5)); // 输出: 5
console.log(multiply(10, 5)); // 输出: 50
console.log(PI); // 输出: 3.14159
```

## 4. 总结

使用 `export` 和 `import` 进行模块化编程可以极大地提高代码的组织性和可维护性。你可以将常用的功能封装在一个模块中，通过 `export` 导出它们，在需要的地方通过 `import` 导入并使用。

- 使用 **命名导出** (`export`) 你可以导出多个内容，导入时需要使用相同的名称引用它们。
- 使用 **默认导出** (`export default`) 你可以导出一个默认值，导入时可以使用任意名称引用它。
- 你可以在同一个模块中混合使用命名导出和默认导出。

---

Title: 使用 export 进行模块化开发教程
Date: 2024-08-24
Author: ChatGPT
