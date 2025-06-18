# JavaScript ES6+ 新特性解析

JavaScript ES6（ECMAScript 2015）及其后续版本引入了许多强大的新特性，极大地改善了开发体验。本文将详细介绍这些重要特性。

## 变量声明：let 和 const

### let 声明

`let` 提供了块级作用域，解决了 `var` 的作用域问题：

```javascript
// var 的问题
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100); // 输出 3, 3, 3
}

// let 的解决方案
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100); // 输出 0, 1, 2
}
```

### const 声明

`const` 用于声明常量：

```javascript
const API_URL = 'https://api.example.com';
const config = {
  timeout: 5000,
  retries: 3
};

// config 对象本身不能重新赋值，但可以修改其属性
config.timeout = 10000; // ✅ 允许
// config = {}; // ❌ 错误
```

## 箭头函数

箭头函数提供了更简洁的函数语法，并且不绑定自己的 `this`：

```javascript
// 传统函数
const add = function(a, b) {
  return a + b;
};

// 箭头函数
const add = (a, b) => a + b;

// 在对象方法中的区别
const obj = {
  name: 'Alice',
  
  // 传统函数
  sayHello: function() {
    setTimeout(function() {
      console.log(`Hello, ${this.name}`); // this 指向 window
    }, 1000);
  },
  
  // 箭头函数
  sayHi: function() {
    setTimeout(() => {
      console.log(`Hi, ${this.name}`); // this 指向 obj
    }, 1000);
  }
};
```

## 模板字符串

使用反引号创建更灵活的字符串：

```javascript
const name = 'World';
const greeting = `Hello, ${name}!`;

// 多行字符串
const html = `
  <div class="container">
    <h1>${greeting}</h1>
    <p>Current time: ${new Date().toLocaleString()}</p>
  </div>
`;

// 标签模板
function highlight(strings, ...values) {
  return strings.reduce((result, string, i) => {
    const value = values[i] ? `<mark>${values[i]}</mark>` : '';
    return result + string + value;
  }, '');
}

const searchTerm = 'JavaScript';
const text = highlight`Learn ${searchTerm} programming`;
```

## 解构赋值

### 数组解构

```javascript
const numbers = [1, 2, 3, 4, 5];

// 基本解构
const [first, second] = numbers;

// 跳过元素
const [, , third] = numbers;

// 剩余元素
const [head, ...tail] = numbers;

// 默认值
const [a, b, c = 0] = [1, 2];
```

### 对象解构

```javascript
const user = {
  id: 1,
  name: 'Alice',
  email: 'alice@example.com',
  address: {
    city: 'New York',
    country: 'USA'
  }
};

// 基本解构
const { name, email } = user;

// 重命名
const { name: userName, email: userEmail } = user;

// 嵌套解构
const { address: { city } } = user;

// 函数参数解构
function createUser({ name, email, age = 18 }) {
  return {
    id: Date.now(),
    name,
    email,
    age
  };
}
```

## 扩展运算符

### 数组扩展

```javascript
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];

// 合并数组
const merged = [...arr1, ...arr2];

// 复制数组
const copy = [...arr1];

// 函数参数
function sum(...numbers) {
  return numbers.reduce((total, num) => total + num, 0);
}

sum(1, 2, 3, 4); // 10
```

### 对象扩展

```javascript
const defaults = {
  theme: 'light',
  language: 'en'
};

const userPrefs = {
  theme: 'dark',
  fontSize: 14
};

// 合并对象
const config = { ...defaults, ...userPrefs };

// 复制对象
const copy = { ...userPrefs };
```

## 类语法

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }
  
  speak() {
    console.log(`${this.name} makes a sound`);
  }
  
  // 静态方法
  static getSpecies() {
    return 'Unknown';
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name);
    this.breed = breed;
  }
  
  speak() {
    console.log(`${this.name} barks`);
  }
  
  // Getter
  get info() {
    return `${this.name} is a ${this.breed}`;
  }
  
  // Setter
  set nickname(value) {
    this.name = value;
  }
}

const dog = new Dog('Buddy', 'Golden Retriever');
dog.speak(); // Buddy barks
console.log(dog.info); // Buddy is a Golden Retriever
```

## Promise 和 async/await

### Promise

```javascript
function fetchData(url) {
  return new Promise((resolve, reject) => {
    fetch(url)
      .then(response => {
        if (!response.ok) {
          throw new Error('Network response was not ok');
        }
        return response.json();
      })
      .then(data => resolve(data))
      .catch(error => reject(error));
  });
}

// 使用 Promise
fetchData('/api/users')
  .then(users => console.log(users))
  .catch(error => console.error(error));
```

### async/await

```javascript
async function getUsers() {
  try {
    const response = await fetch('/api/users');
    
    if (!response.ok) {
      throw new Error('Failed to fetch users');
    }
    
    const users = await response.json();
    return users;
  } catch (error) {
    console.error('Error:', error);
    throw error;
  }
}

// 并行请求
async function getAllData() {
  try {
    const [users, posts, comments] = await Promise.all([
      fetch('/api/users').then(r => r.json()),
      fetch('/api/posts').then(r => r.json()),
      fetch('/api/comments').then(r => r.json())
    ]);
    
    return { users, posts, comments };
  } catch (error) {
    console.error('Failed to fetch data:', error);
  }
}
```

## 模块系统

### 导出

```javascript
// math.js
export const PI = 3.14159;

export function add(a, b) {
  return a + b;
}

export class Calculator {
  multiply(a, b) {
    return a * b;
  }
}

// 默认导出
export default function subtract(a, b) {
  return a - b;
}
```

### 导入

```javascript
// 命名导入
import { PI, add, Calculator } from './math.js';

// 默认导入
import subtract from './math.js';

// 全部导入
import * as math from './math.js';

// 重命名导入
import { add as sum } from './math.js';

// 动态导入
async function loadMath() {
  const math = await import('./math.js');
  return math;
}
```

## 新的数据结构

### Set

```javascript
const uniqueNumbers = new Set([1, 2, 3, 3, 4, 4, 5]);
console.log(uniqueNumbers); // Set { 1, 2, 3, 4, 5 }

// 方法
uniqueNumbers.add(6);
uniqueNumbers.has(3); // true
uniqueNumbers.delete(1);
uniqueNumbers.size; // 5

// 转换为数组
const array = [...uniqueNumbers];
```

### Map

```javascript
const userRoles = new Map();

// 设置值
userRoles.set('alice', 'admin');
userRoles.set('bob', 'user');
userRoles.set(123, 'guest');

// 获取值
console.log(userRoles.get('alice')); // admin

// 遍历
for (const [user, role] of userRoles) {
  console.log(`${user}: ${role}`);
}

// 使用对象作为键
const element = document.getElementById('myButton');
const metadata = new Map();
metadata.set(element, { clickCount: 0, lastClicked: null });
```

## 总结

ES6+ 的新特性极大地提升了 JavaScript 的开发体验：

- **let/const**: 更好的变量声明
- **箭头函数**: 简洁的函数语法
- **模板字符串**: 灵活的字符串处理
- **解构赋值**: 优雅的数据提取
- **扩展运算符**: 方便的数组/对象操作
- **类语法**: 面向对象编程
- **Promise/async-await**: 异步编程
- **模块系统**: 代码组织和复用
- **新数据结构**: Set/Map 等

这些特性不仅让代码更加简洁和易读，也提供了更强大的功能来构建现代 JavaScript 应用。

---

*继续探索 JavaScript 的魅力，让编程变得更加有趣！*
