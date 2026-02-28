# ES6
## let 代替 var
var 导致的问题：
1. 越狱： var会跳出函数作用域
2. 重复声明： var可以重复声明变量
3. 变量提升： var声明的变量会提升到函数顶部

## const 代替 var
const 声明一个常量，必须初始化，且不能重新赋值。适用于不需要修改的变量，如配置项、常量等。

## 解构
解构赋值允许从数组或对象中提取值，并将它们赋值给变量。可以简化代码，提高可读性。

### 数组解构
```js
// 数组解构
let arr = [1, 2, 3];
// 解构赋值
let [a, b, c] = arr; 
console.log(a); // 1
console.log(b); // 2
console.log(c); // 3
```

### 对象解构
```js
// 对象解构
let obj = {name: 'duan', age: 20};
// 解构赋值
let {name, age} = obj;
console.log(name); // duan
console.log(age); // 20

function getInfo(name, age) {
    console.log(name); // duan
    console.log(age); // 20
}
// 调用函数时，将 obj 作为参数传递, 可以直接解构赋值
getInfo(obj);
```

## 链判断（?.）
链判断（Optional Chaining）在访问对象属性时，如果左侧为 `null` 或 `undefined`，会短路并返回 `undefined`，避免抛出错误。

| 语法 | 作用 | 示例 |
|-----|------|-----|
| `obj?.prop` | 安全访问对象属性 | `user?.name` |
| `obj?.[expr]` | 安全访问动态属性/数组 | `list?.[0]` |
| `func?.()` | 安全调用方法 | `logger?.log()` |

```js
let user = {
    name: 'duan',
    address: {
        city: 'beijing'
    }
};

// 深层属性访问 - 避免 "Cannot read properties of undefined"
console.log(user.address?.city);      // beijing
console.log(user.address?.zipCode);   // undefined（安全）
console.log(user.profile?.age);       // undefined（profile 不存在）

// 等价于繁琐的写法：
console.log(user.address && user.address.zipCode); // undefined

// 方法调用
console.log(user.getAge?.());         // undefined（方法不存在时安全）

// 数组访问
console.log(user.items?.[0]);         // undefined（items 不存在时）
```

## 参数默认值
函数参数可设置默认值，未传递参数或传递 `undefined` 时使用默认值。

> **注意**：默认参数应放在参数列表末尾，否则必须显式传递 `undefined` 才能触发默认值

```js
// 基本用法
function greet(name = 'duan') {
    console.log(`hello, ${name}`);
}

greet();        // hello, duan（使用默认值）
greet('alice'); // hello, alice

// 多个参数默认值
function createUser(name = 'guest', age = 18, isAdmin = false) {
    return { name, age, isAdmin };
}

createUser();               // { name: 'guest', age: 18, isAdmin: false }
createUser('bob');          // { name: 'bob', age: 18, isAdmin: false }
createUser('bob', 25);      // { name: 'bob', age: 25, isAdmin: false }

// 与解构配合
function request({ url, method = 'GET', timeout = 3000 } = {}) {
    console.log(url, method, timeout);
}

request({ url: '/api' });           // /api GET 3000
request({ url: '/api', method: 'POST' }); // /api POST 3000

// 错误示范：默认参数不在末尾
function bad(name = 'guest', age) { }
bad(25);  // 期望 age=25，实际 name=25，age=undefined（混淆）

// 正确：默认参数在后
function good(age, name = 'guest') { }
good(25);  // age=25, name='guest'（清晰）
```

## 箭头函数
简洁的函数语法，核心特点：**语法简洁**、**this 继承外层**、**不适合作构造函数/方法**。

```js
// 基本语法
const add = (a, b) => a + b;          // 多参数，表达式返回
const square = x => x * x;            // 单参数，省略括号
const log = () => console.log('hi');  // 无参数

// 多行函数体（需要 return）
const calc = (x, y) => {
    const sum = x + y;
    return sum * 2;
};

// 返回对象字面量（需要括号）
const createUser = name => ({ name, id: Date.now() });

// this 绑定（核心优势）
const obj = {
    name: 'duan',
    // 传统函数需要 var self = this 或 .bind(this)
    greet: function() {
        setTimeout(function() {
            console.log('hello, ' + this.name); // undefined（this 指向 window）
        }, 100);
    },
    // 箭头函数自动继承外层 this
    greetArrow: function() {
        setTimeout(() => {
            console.log('hello, ' + this.name); // duan（this 继承自 obj）
        }, 100);
    }
};

// 不能作为构造函数
const Person = (name) => { this.name = name; };
// new Person('duan'); // TypeError: Person is not a constructor
```

## 模版字符串
使用反引号（``）定义字符串，支持多行和内嵌表达式。

```js
// 基本用法
let name = 'duan';
let age = 20;

let message = `hello, ${name} you are ${age} years old `;

console.log(message); // hello, duan you are 20 years old 
```  

## Promise
异步编程解决方案，代表一个未来可能完成的操作。有三种状态：**pending**（进行中）、**fulfilled**（已成功）、**rejected**（已失败）。

```js
// 自定义 Promise 示例
// 模拟 API 请求
function request(success) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if (success) {
                resolve('数据获取成功');
            } else {
                reject(new Error('网络错误'));
            }
        }, 1000);
    });
}

// 成功场景
request(true)
    .then(result => console.log(result))  // 数据获取成功
    .catch(error => console.error(error))
    .finally(() => console.log('请求结束'));

// 失败场景
request(false)
    .then(result => console.log(result))  // 跳过
    .catch(error => console.error(error)) // Error: 网络错误
    .finally(() => console.log('请求结束')); // 仍会执行

// Promise.all - 所有成功才成功，一个失败则失败
Promise.all([
    Promise.resolve('user'),
    Promise.resolve('posts')
]).then(results => {
    console.log(results); // ['user', 'posts']
});

// Promise.race - 谁快用谁
Promise.race([
    new Promise(r => setTimeout(() => r('fast'), 100)),
    new Promise(r => setTimeout(() => r('slow'), 200))
]).then(result => {
    console.log(result); // fast
});
```

## 异步函数（async/await）
基于 Promise 的语法糖，使异步代码看起来更像同步代码。

```js
// 模拟 API
const getUser = () => Promise.resolve({ id: 1, name: 'duan' });
const getPosts = (userId) => Promise.resolve(['post1', 'post2']);

// 基本用法
async function fetchData() {
    try {
        const response = await request(true);
        console.log(response); // 数据获取成功
    } catch (error) {
        console.error(error); // 处理错误
    }
}

// 顺序执行（串行）
async function serial() {
    const user = await getUser();     // 等待 100ms
    const posts = await getPosts();   // 等待 100ms
    // 总耗时：200ms
}

// 并行执行（推荐）
async function parallel() {
    const [user, posts] = await Promise.all([
        getUser(),
        getPosts()
    ]);
    // 总耗时：100ms（同时执行）
}

// async 函数返回 Promise
async function getValue() {
    return 42;
}
getValue().then(v => console.log(v)); // 42

// await 只能在 async 函数中使用
// 顶层 await 需要模块支持（type="module"）
```

## 模块化
原生模块系统，使用 `export` 导出，`import` 导入。需要在 `<script type="module">` 或 `.mjs` 文件中使用。

```js
// ========== utils.js ==========
// 命名导出
export const PI = 3.14;
export function add(a, b) { return a + b; }
export const user = { name: 'duan' };

// 默认导出（一个文件只能有一个）
export default function greet(name) {
    console.log(`hello, ${name}`);
}

// ========== main.js ==========
// 默认导入（名称可自定义）
import greet from './utils.js';
greet('alice'); // hello, alice

// 命名导入（名称必须匹配）
import { add, PI, user } from './utils.js';
console.log(add(1, 2)); // 3
console.log(PI);        // 3.14

// 全部导入到对象
import * as utils from './utils.js';
utils.add(1, 2);
utils.greet('bob');

// 混合导入
import greetDefault, { add, PI } from './utils.js';

// 动态导入（返回 Promise）
const utils = await import('./utils.js');
utils.add(1, 2);

// 只导入副作用（不使用导出内容）
import './style.css';
```

**注意**：ES6 模块是静态分析，import 必须在文件顶部（动态 import() 除外）