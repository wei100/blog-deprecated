---
title: 鲜为人知的JavaScript功能
date: 2019-09-29 10:56:15
thumbnail: /thumbnails/little-known-javascript.jpg
categories:
- 技术
	- JavaScript
tags:
- JavaScript
- IIFE
- ES6
---

JavaScript 常被人说道：“入门容易，精通难” 的一门语言，这个我深以为然。这是因为 JavaScript 是一门非常古老且非常灵活的语言，充满神秘的语法和一些过时的特性使得JavaScript 难以掌控。截止今天我虽已使用JavaScript多年了，但是在开发过程中偶尔会被隐藏的语法和巧妙的编码方式所迷惑不解。
下面我试图罗列出那些鲜为人知的 JavaScript 特性， 虽然有很多的特性在严格模式下已经无效，但是它在非严格模式下不失为完美的代码。但是请注意，我并不建议你使用所有的这些特性，即使它使用起来非常的酷。

所有的代码可以在我的[GitHub](https://gist.github.com/viral-sh/98813f83f4afe9dce5a74e176f88724f)仓库找到，祝你编码愉快。

> 注意：我这篇文章不会具体介绍声明提前`Hoisting`，闭包`Closures`，代理`Proxies`，原型继承`Prototypal inheritance`，异步`async-await`，生成器`generators`等等功能。虽然这些特性理解起来比较难，但是作为一名前端开发人员，你需要很好的掌握它。


### Void 操作符
JavaScript 有一个一元操作符：`void`，你也许曾经的某个时候见过它亦或是使用过它：`void(0)`，`void 0`等等。 `void` 操作符在javascript中功能非常单一，它只负责一件事情：执行右边表达式并返回`undefined`。使用 `0`仅仅是一种惯例，你可以使用任何有效的javascript表达式，比如：`void (1+1)`，但是它仍然返回`undefined`。
``` javascript
void 0                 // return undefined
void (0)               // return undefined
void 'abc'             // return undefined
void (1===1)           // return undefined
void {}                // return undefined
void function(){}      // return undefined
void NaN               // return undefined
```
> 看到这里你也许有点疑惑了，为什么要创建一个特殊的关键字来返回`undefined`而不是只返回`undefined`？这不是多此一举吗？
> 确实，以当前的角度来看确实是多此一举，但是在 `ES5` 之前我们可以`var undefined = 'abc'`这样来定义一个`undefined` 变量，这样会混淆自定义的`undefined`变量和 `undefined`关键字。所以在`ES5`之前使用`void`能够确保它总是返回原始的`undefined`。

### 构造函数的括号非必须
我们通常调用构造函数时会加上括号，比如`var user = new User('奇艺虎', '武汉');`。但是如果我们在调用无参构造函数的时候可以省略掉括号，比如：`var user = new User;`。
下面的代码样式都是有效的JS语法并且会返回完全相同的结果。
```javascript
// 有括号的构造函数
var date = new Date();
var date = new Date().getMonth();
var instance = new MyClass();

//没有括号的构造函数
var date = new Date;
var date = （new Date).getMonth();
var instance = new MyClass;
```

### IIFE 的括号非必须
`IIFE`（**立即调用函数表达式**）的语法对我来说总是有点奇怪。`()`只是为了告诉JavaScript解析器，即将到来的代码是函数表达而不是函数。基于这一点，有很多方法可以跳过这些额外的括号并且仍然可以制作有效的IIFE。
```javascript
// IIFE
// example 1：
(function(){
	console.log('Normal IIFE called');
})()
output: Normal IIFE called

// example 2：
void function(){
	console.log('Normal IIFE called');
}()
output: Normal IIFE called
```
通过对比上面的2个用例，用例2我们利用**void**操作符省去了括号并且仍然是有效的IIFE。其实我们不仅仅可以利用**void**操作符来跳过额外的括号，我们也可以利用其它的一元操作符来达到同样的目的，比如：`void, +, !, -.`等等
看到这里是不是感觉很酷，也会有所疑惑：**一元运算符不会影响IIFE返回的结果吗？**
答案是肯定的，但是利好的是如果你关心结果，可以将其存储在某个变量中，同时也不需要额外的括号。
```javascript
// example 1：
var result = (function(){
	return 'IIFE with a return';
})();

// example 2：
var result = function() {
	return 'IIFE with a return';
}();
```
> 想要更深入地了解 **IIFE**，请查看Chandra Gundamaraju的这篇[文章](https://medium.com/@vvkchandra/essential-javascript-mastering-immediately-invoked-function-expressions-67791338ddc6)

### With 语句
您知道吗，JavaScript有一个**with**语句块而且**with**实际上是JS中的关键字。**with**的用法一般如下面所示：
```javascript
with (object)
   statement 
// for multiple statements add a block
with (object) {
   statement
   statement
   ...
}
```
**with**语句可以方便地用来引用某个特定对象中已有的属性，但是请注意你不能用来给对象添加属性。要给对象创建新的属性，必须明确地引用该对象。
```javascript
// with block example
var person = {
	firstname: '奇艺',
	lastname: '虎',
	age: 29
};
with(person) {
	console.log(`${firstname} ${lastname} is ${age} years old`);
}
output: 奇艺 虎 is 29 years old
```
> 使用**with**语句是不是感觉很爽，甚至优于对象解构。
> 答案是否定的，通常情况下我们不鼓励使用**with**语句，因为它已被弃用而且在严格模式下完全禁止。 事实证明，使用**with**会增加语言中的一些性能和安全性问题，所有在以后的开发中请忽略它的存在。

### Function 构造函数
函数语句`function(){}`不是定义新函数的唯一方法，你可以使用`Function()`和new运算符动态定义函数。
```javascript
// function constructor
const multiply = new Function('x', 'y', 'return x * y;');
multiply(2, 3);
output: 6
```
最后一个参数是函数体，之前其它的参数是函数的形参。
> Function的构造函数是所有JavaScript对象的父构造函数。 甚至Object的构造函数也是Function构造函数。 而Function自己的构造函数也是Function本身。 因此，在JavaScript中调用`object.constructor.constructor ...`无论多少次最终还是返回Function构造函数。

### Function 属性
我们都知道函数`function`在JavaScript中是一等公民，因此我们可以向函数添加自定义属性，虽然这种做法比较少见了。
但是，我们什么时候需要这样做啦？
下面我试图列举一下比较好的用例以供飨食。

#### 可配置的函数
假设我们有一个名为`greet`函数。 我们希望我们的功能可以根据不同的区域设置打印不同的问候消息，而且区域设置是可配置的。我们可以在某处维护一个全局语言环境变量或者我们可以使用函数属性实现该功能，如下所示：
```javascript
// custom functional properties
function greet() {
	if(greet.locale === 'fr') {
		console.log('Bonjour!');
	} else if(greet.locale === 'es') {
		console.log('Hola!');
	} else {
		console.log('Hello!');
	}
}
greet();
// Hello!
greet.locale = 'fr';
greet();
// Bonjour!
```
#### 静态变量
另一个类似的例子，假设你想要实现一个生成器用以生成一系列有序数字的数字。 通常，你会使用`Class`或`IIFE`和静态计数器变量来跟踪最后一个值。这样做的好处是我们可以限制对计数器的访问，并避免使用额外的变量来污染全局空间。

但是，如果我们希望灵活地读取甚至修改计数器并且不污染全球空间呢？
好吧，我们仍然可以创建一个`Class`，带有一个计数器变量和一些额外的方法来读取它; 或者我们可以懒惰一点只需在函数上使用属性。

```javascript
// custom functional properties
function generateNumber() {
	if(!generateNumber.counter) {
		generateNumber.counter = 0;
	}
	return ++generateNumber.counter;
}
generateNumber();
// 1
generateNumber();
// 2
generateNumber.counter;
// 2
generateNumber.counter = 10;
generateNumber.counter;
// 10
generateNumber();
// 11
```
>  清注意，在工程实践中我们并不建议你这样做，就如我前面提到的有非常多的替代方案可供选择。

### Arguments 属性
我相信大多数人都知道函数内的参数对象`arguments`。 每一个函数都有一个`arguments`对象，它包括了函数所要调用的参数，通常我们把它当作数组使用，用它的`length`得到参数数量，但它并不是数组。它有一些其他有趣的属性：
- **arguments.callee**: 指的是函数自身
- **arguments.callee.caller**: 指的是函数调用者
```javascript
const myFunction = function() {
	console.log('Current function: ', arguments.callee.name);
	console.log('Invoked by function: ', arguments.callee.caller.name);
}
void function main() {
	myFunction();
} ();
// Current function:  myFunction
// vendors~main.93cb1e58.chunk.js:14 Invoked by function:  main
```
> 注意：虽然`ES5`禁止在严格模式下使用`callee` 和 `caller`，但在许多编译库中仍然常见。 所以你需要了解它。

### 标签模板
如果你熟悉es6一定听过或者用过字符串模板，字符串模板是ES6众多功能补充之一。 但是，你熟悉标签模板`(tagged template)`吗？
```javascript
// Normal template literal
`Hello ${username}`
// Tagged template literal
myTag`Hello ${username}`
```
标签模板允许你通过向模板添加自定义标记来更好地控制将模板解析为字符串。 `标签(Tag)`是一个解析器函数，它获取字符串模板解释的所有字符串和值的数组，标记函数应返回最终字符串。
在下面的示例中，我们的自定义标记 **highlight**，解释模板文字的值，并使用`<mark>`元素将结果字符串中的解释值包装起来以突出显示。
```javascript
function highlight(strings, ...values) {
	let result = '';
	strings.forEach((str, i) => {
		result += str;
		if(values[i]) {
			result += `<mark>${values[i]}</mark>`;
		}
	});
	return result;
}
const author = 'Henry Avery';
const statement = `I am a man of fortune & I must seek my fortune`;
const quote = highlight `${author} once said, ${statement}`;
// <mark>Henry Avery</mark> once said, <mark>I am a man of fortune & I must seek my fortune</mark>
```
>  许多第三方库利用这一特性来实现各种功能。 以下是一些很酷的例子：
>	- [styled-components](https://github.com/styled-components/styled-components) for React
>	- [es2015-i18n-tag](https://github.com/skolmer/es2015-i18n-tag) for translation & internationalization
>	- [chalk](https://github.com/chalk/chalk) for colorful logs

### Getters & Setters
对于大多数情况下，`JavaScript`对象很简单。 假设我们有一个用户(**user**)对象，并且我们尝试使用user.age访问它上面的age属性，如果它已定义,我们可以得到age属性的值，如果没有定义，我们得到`undefined`。
但是，深究下去并不简单。 `JavaScript`对象具有`Getters`和`Setter`的概念。 我们可以编写自定义的`Getter`函数来返回我们想要的任何内容，而不是直接返回对象上的值。 同理，通过`Setter`设置值也是一样的。

我们可以利用`Getter` 和 `Setter`  这一特性实现各种强大的功能，如虚拟字段(**virtual fields**)，字段验证(**field validations**))，副作用(**side-effects**)
```javascript
// Getters & Setters
const user = {
	firstName: 'Nathan',
	lastName: 'Drake',
	
	//fullname is a virtual field
	get fullName() {
		return this.firstName + ' ' + this.lastName;
	},

	// validate age before saving
	set age(value) {
		if(isNaN(value)) throw Error('Age has to be a number');
		this._age = Number(value);
	},
	get age() {
		return this._age;
	}
}

console.log(user.fullName); // Nathan Drake
user.firstName = 'Francis';
console.log(user.fullName); // Francis Drake
user.age = '29';
console.log(user.age); // 29
user.age = 'invalid text' // Error: Age has to be a number
```
`Getter` 和 `Setter` 并不是ES5的新增功能。 ES5只是为现有功能添加了方便的语法。 要了解有关`Getter` 和 `Setter` 的更多信息，请参阅此[文章](https://www.hongkiat.com/blog/getters-setters-javascript)
> [**Colors**](https://github.com/Marak/colors.js)是一个流行的`node.js`库，是利用`Getters`的一个很好的参考用例。
该库扩展了`String`类，并在其上添加了一堆`Getter`方法。 这允许我们通过简单地访问它的属性，将任何字符串添加各种颜色以便于日志记录。

### 逗号运算符

JavaScript有一个逗号运算符，它允许我们在一行中用逗号分隔多个表达式，并返回最后一个表达式的结果。
```javascript
// syntax
let result = expression1, expression2,... expressionN
```
上面示例，将执行所有表达式，并将expressionN执行的返回值赋予给**result**变量。

您可能已经在`for`循环中使用了逗号运算符：
```javascript
for (var a = 0, b = 10; a <= 10; a++, b--)
```
有时，在一行中编写多个语句时会非常方便：
```javascript
function getNextValue() {
    return counter++, console.log(counter), counter
}
```
或简洁的`lambda`函数:
```javascript
const getSquare = x => (console.log (x), x * x)
```
### + 运算符
曾经有想过如何快速将字符串转换为数字吗？

非常简单，只需在字符串前加上+运算符。
**+**运算符同样适用于**负数**，**八进制**，**十六进制**，**指数值**。 更重要的是它甚至可以将`Date`或`Moment.js`对象转换为时间戳！
```javascript
//Plus Operator
+'9.11'       // returns 9.11
+ '-4'        // returns -4
+'0xFF'       // returns 255
+true         // returns 1
+'123e-5'     // returns 0.00123
+false        // returns 0
+null         // returns 0
+'Infinity'   // returns Infinity
+'1,234'      // returns NaN
+new Date()   // returns 1568599238829
```
### !! 云算符
好吧，从技术上讲它并不是一个单独的`JavaScript`运算符。 它只是两次使用的`JavaScript`否定运算符。
但是 **!!** 听起来很酷，**!!** 可以将任何表达式巧妙的转换为布尔值。

如果表达式是真值，则返回**true**; 否则返回**false**。
```javascript
!!null         // returns false
!!undefined    // returns false
!!false        // returns false
!!true         // returns true
!!""           // returns false
!!"string"     // returns true
!!0            // returns false
!!1            // returns true
!!{}           // returns true
!![]           // return true
```
### ~ 运算符
不得说在日常的开发中我们很少会关心按位运算符。
~ 运算符查看表达式的二进制表示形式的值，并执行位非运算。
Javascript 按位取反运算符 (~) ，对一个表达式执行位非（求非）运算。如 ~1 = -2; ~2 = -3;
我们可以通过将〜放在indexOf（...）函数前面来进行布尔检查用以判断某个元素是否存在于`String`或`Array`中。
```javascript
// Tilde operator with indexOf
const username = 'Nathan Drake';
if(~username.indexOf("Drake")) {
	console.log('Access denied');
} else {
	console.log('Access granted');
}
```
> 注意：ES6和ES7分别在String和Array中添加了一个新的方法 `includes()`。当然，它比～运算符更清晰，用来检查某个元素是否存在于`Array`或`String`中。

### 标签声明
`JavaScript`具有标签声明这这样一个特性。 它允许我们在`JavaScript`中声明循环和块。 然后我们可以在使用`break`或`continue`时返回这些标签。

标签声明在嵌套循环中特别方便，我们也可以使用它们来简单地将代码组织成块或创建一个可破坏的块。
```javascript
declarationBlock: {
	// can be used to group logical code blocks together
	var i, j
}

forLoop1: // The first for statement is labeled "forLoop1"
for(i = 0; i < 3; i++){
	forLoop2: //The second for statement is labeled "forLoop2"
	for(j = 0; j < 3; j++) {
		if(i === 1 && j === 1) {
			continue forLoop1;
		}
		console.log('i = ' + i + ', j = ' + j);
	}
}

loopBlock4: {
	console.log('I will print');
	break loopBlock4;
	console.log('I will not print');
}
```
> 注意：与其他一些语言不同，JavaScript没有`goto`关键字。 因此，我们只能使用带有`break`和`continue`的标签。


如果您对此类`JavaScript`有所了解，或者已经找到了有趣的用例来利用这些功能，请在下面分享您的经验，我很乐意能够看到你的分享。
我喜欢`JavaScript`并喜欢写关于它的文章，写或者翻译一遍文章确实需要花费大量的时间和精力。
如果您喜欢这篇文章，请分享并推荐它。
祝你编码愉快...

