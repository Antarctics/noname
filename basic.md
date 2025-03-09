# 第一章：基础知识

## 1. 开发环境准备

### 1.1 必要工具
- 文本编辑器(推荐使用VS Code)
- 无名杀游戏本体

### 1.2 开发环境设置
1. 下载并安装VS Code
2. 安装必要插件:
   - Prettier（用于JavaScript格式化）

### 1.3 新建扩展文件
##### 方法一
 - 游戏内依次点击 `选项 -> 扩展 -> 制作扩展 -> 输入扩展名 -> 确定 -> 保存` 即可新建扩展
#### 方法二
创建你的开发目录:
```
├── extension.js    # 扩展主文件
├── info.json       # 扩展信息
├── character.js    # 角色文件(可选)
├── card.js         # 卡牌文件(可选)
├── skill.js        # 技能文件(可选)
├── image/          # 图片文件夹(可选)
└── audio/          # 音频文件夹(可选)
```
将上述文件压缩为zip格式，随后游戏内依次点击 `选项 -> 扩展 -> 获取扩展 -> 导入扩展 -> 选择压缩包 -> 确定` 即可导入扩展

## 2. JavaScript基础
### 2.1 变量
#### 说明
变量是所有编程语言的基础之一，可以用来存储数据，例如字符串、数字、布尔值、数组等，并在需要时设置、更新或者读取变量中的内容。
在 JavaScript 中，变量名称并不能随便定义，需要遵循标识符的命名规则，如下所示：
- 变量名中可以包含数字、字母、下划线_、美元符号$；
- 变量名中不能出现汉字；
- 变量名中不能包含空格；
- 变量名不能是 JavaScript 中的关键字、保留字；
- 变量名不能以数字开头，即第一个字符不能为数字。
- 推荐使用驼峰命名法（大驼峰：每个单词首字母大写，例如 FileType、DataArr；小驼峰：第一个单词首字母小写后面的单词首字母大写，例如 fileType、dataArr）。
#### 定义变量
在 JavaScript 中，通常使用`var`、`let`、`const`关键字定义变量：
```javascript
// 声明变量
var name;        // 创建全局变量 name
let sex;         // 创建局部变量 message
const hp;        // 创建常量 hp

// 赋值变量
var name = "Zhang",
    sex = "male"                                  // 创建并赋值全局变量 name、sex
let user = 'John', age = 25, message = 'Hello';   // 创建并赋值局部变量 user、age、message
```
需要注意的是，在大多数编程语言中，`=`的意义均为赋值，而非`等于`！

若想判断两个数据是否相等，需要使用`==`。

#### 变量的区别
##### 全局变量（var）
使用`var`定义的变量生效于`函数作用域`或`全局作用域`：
```javascript
// 全局作用域
if (true) {
  var test = true;
}
alert(test)  // true，在 if 外依旧可以读取到var变量

// 函数作用域
function hello() {
  if (true) {
    var test = "Hello";
  }
  alert(test); // Hello 
}
hello();
alert(test); // ReferenceError: test is not defined（test未定义）
```
使用`var`定义的变量允许重新声明和赋值：
```javascript
// 重新声明
var user = "Pete";
var user = "John";
alert(user); // John

// 重新赋值
var user = "Pete";
user = "John";
alert(user); // John
```

##### 局部变量（let）
使用`let`定义的变量仅生效于`块级作用域`：
```javascript
if (true) {
  let test = true;
  alert(test) // true
}
alert(test) // ReferenceError: test is not defined（test未定义）
```
使用`let`定义的变量允许重新赋值，但不允许重新声明：
```javascript
// 重新声明
let test = true;
let test = false
alert(test); // SyntaxError: Identifier 'test' has already been declared （test已被定义）

// 重新赋值
let user = "Pete";
user = "John";
alert(user); // John
```
##### 常量（const）
使用`const`定义的变量仅生效于`块级作用域`：
```javascript
if (true) {
  const test = true;
  alert(test) // true
}
alert(test) // ReferenceError: test is not defined（test未定义）
```
使用`const`定义的变量不允许重新声明和重新赋值：
```javascript
// 重新声明
const test = true;
const test = false
alert(test); // SyntaxError: Identifier 'test' has already been declared （test已被定义）

// 重新赋值
const user = "Pete";
user = "John";
alert(user); // TypeError: Assignment to constant variable.（不能对常量重新赋值）
```

### 2.2 数据类型
JavaScript中的基本数据类型：

- 字符串（string）: ```"Hello,World！"```
  - 使用半角单引号`'Hello'`、半角双引号`"Hello"`、反引号`` `Hello` ``包含的文本即为字符串，不可使用全角符号（`“ ”`或`‘ ’`）。
  - 反引号是 __功能扩展__ 引号，后续会进行说明。
  - 你可以在字符串中使用引号，只要字符串中的引号与包围引号不匹配即可：
    - 如 `"张角闻知事露，星夜举兵，自称“天公将军”，张宝称“地公将军”，张梁称“人公将军”。"`
  
- 数字（number）：```3.1415926```
  - 直接使用的数字即为数字类型，可带小数，也可不带，支持科学计数法。
  - 数字支持使用乘法 `*`、除法 `/`、加法 `+`、减法 `-` 等等进行运算。
  - 这些特殊值也属于数字：
    - `Infinity`、`-Infinity` 和 `NaN`，它们分别代表无穷、负无穷、无法定义
  
- 布尔（boolean）：```true```、```false```
  - 布尔只有两个值，以下值在使用布尔转换时会赋值为```false```：
    - `0`、`-0`、`null`、`false`、`undefined`、`NaN`
    - 注意：数组`[]`、字符串`"false"`均会赋值为`true`

- 对象（object）: ```{name: "张飞",hp: 4}```
  - 用于储存数据集合和更复杂的实体的类型。
  
- 数组（array）：```[1, "hello", { key: "value" }, true]```
  - 一种有序集合数据类型，用于存储任意类型的值，是对象的一种特殊类型。

- 空（null）：`null`
  - 一个代表`空白`的特殊值。
  - 创建但赋值为空：`let hp = null`

- 无（undefined）：`undefined`
  - 代表`未被赋值`的特殊值
  - 创建但未赋值：`let hp`

当我们想要分别处理不同类型值的时候，或者想快速进行数据类型检验时，可以使用`typeof`运算符。
对 typeof x 的调用会以字符串的形式返回数据类型：
```javascript
typeof undefined // "undefined"
typeof 0 // "number"
typeof true // "boolean"
typeof "foo" // "string"
typeof alert // "function"
```

### 2.3 函数
函数是JavaScript中最重要的概念之一：
```javascript
// 基本函数定义
function say(text = "hello") {
  alert(text);
}
say()       // Hello
say("你好") // 你好

/**
 * 箭头函数
 * 一种比传统的函数表达式更简洁的表达方式，但是具有一些限制
 */
const say = text => alert(text);
const isDamaged = (player) => {
    return player.hp < player.maxHp
};
say("你好")        // 你好
isDmaged("张角")   // true（此处仅用作教学，无名杀中的实际判断方式并非此例）


// 异步函数
async function drawDiscard(player) {
    await player.draw(2);
    await player.chooseToDiscard(1);
}
drawDiscard("张角") // 等待张角摸2牌的动作完成后后再执行弃置1牌。
```
### 2.4 运算符
运算符通常用于执行各种操作，如赋值、比较、算术计算等。
```javascript
let x = 5;              // 赋值：将 X 赋值为 5
let sum = 1 + 2;        // 加法：3
let product = sum * 2;  // 乘法：结果为 6
1 == 1                  // 等于：结果为 true
1 != 1                  // 不等于：结果为 false
x === "5"               // 全等：结果为 false
x !== 5                 // 不全等：结果为 false
x > 5                   // 大于：结果为 false
x < 5                   // 小于：结果为 false
x >= 5                  // 大于等于：结果为 true
// 注意：=> 为箭头函数标记符，而非运算符
x <= 5                  // 小于等于：结果为 true

x += 1                  // 加法赋值：等价于 x = x + 1 ，结果为 6
x++                     // 自增：等价于 x += 1 ，结果为6
x--                     // 自减：等价于 x -= 1 ，结果为4
12 % 5                  // 求余：结果为 2
-x                      // 求负：结果为 -5
+"5"                    // 求正：若为非数字，会试图转化为数字，结果为 5
2 ** 3                  // 指数运算，结果为 8

x > 6 || x == 5         // 或（||）：任意一边满足即为true，结果为 true
x > 6 && x == 5         // 和（&&）：任意一边不满足即为false，结果为 false
```

### 2.5 条件语句
```javascript
// if条件
if (player.hp <= 2) { // 体力值小于等于2执行
    player.draw(1); 
} else if (player.hp == 3) { // 体力值大于2且等于3时执行
    player.draw(2);
} else { // 均不满足时执行
    player.draw(3);
}

// ? 语句
let result = isDamaged() ? player.draw() : player.discard(); // 若isDamaged()返回值为true，则result赋值为player.draw()，否则赋值为player.diascard()

// switch语句
switch(event.name) {
    case 'damage': // 若event.name == damage 时执行
        player.draw(1);
        break; // 结束本轮，等价于return
    case 'lose': 
        player.recover();
        break;
    default: // 均不满足的默认情况
        return "无满足" // 返回指定内容
}
```

### 2.6 循环语句
```javascript
// if条件
if (player.hp <= 2) { // 体力值小于等于2执行
    player.draw(1); 
} else if (player.hp == 3) { // 体力值大于2且等于3时执行
    player.draw(2);
} else { // 均不满足时执行
    player.draw(3);
}

// switch语句
switch(event.name) {
    case 'damage': // 若event.name == damage 时执行
        player.draw(1);
        break; // 结束本轮，等价于return
    case 'lose': 
        player.recover();
        break;
    default: // 均不满足的默认情况
        return "无满足" // 返回指定内容
}
```


## 练习题
1. 下面的脚本会输出什么？
```javascript
let name = "张飞";

alert( `hello ${1}` ); // ?

alert( `hello ${"name"}` ); // ?

alert( `hello ${name}` ); // ?
```

<details>
<summary>参考答案 | 🟩 Easy</summary>

```javascript
// 表达式为数字 1
alert( `hello ${1}` ); // hello 1

// 表达式是一个字符串 "name"
alert( `hello ${"name"}` ); // hello name

// 表达式是一个变量。
alert( `hello ${name}` ); // hello 张飞
```

</details>

2. 下面的脚本会输出什么？
```javascript
let x = "5"
x += 5
+x
```

<details>
<summary>参考答案 | 🟩 Easy</summary>

```javascript
x += 5    // 字符串"55"
+x        // 数字5
```

</details>

3. 下面的脚本会输出什么？
```javascript
let x = 5
let name = ((x > 5 || x == 4) && x == 5) ? "张角" : "张飞"
switch(name) {
    case '张角':
        return "大贤良师"
    case '张宝': 
        return "地公将军"
    default:
        return "人公将军？"
}
```

<details>
<summary>参考答案 | 🟩 Easy</summary>

```javascript
let x = 5
let name = ((x > 5 || x == 4) && x == 5) ? "张角" : "张飞"
switch(name) {
    case '张角':
        return "大贤良师"
    case '张宝': 
        return "地公将军"
    default:
        return "人公将军？"
}
// 人公将军？
```

</details>

</br>
下一章我们将正式学习制作无名杀扩展。