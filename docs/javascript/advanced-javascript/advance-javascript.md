<p style="text-align: center; font-size: 32px"><strong>主要介绍一些高级的, 难以理解的 javascript 语法</strong></p>
<p style="text-align: right"><strong>完结于：2020/06/10</strong></p>

## 面向对象的程序设计

对象的定义为: **无序属性的集合, 其属性可以包含基本值, 对象或者函数.**

### 特性

ECMA-262 第 5 版定义了只有内部使用的**特性**, 用来描述属性的各种特征. 这些特性是为了实现 JavaScript 引擎用的, 不能直接访问他们.

#### 特性

主要包括两种属性: **数据属性和访问器属性**.

- 数据属性

  数据属性是指值为数据值的属性, 可以在这个位置读取和写入数值. 有四个特性:

  - `[[Configurable]]`: 能否通过 delete 删除属性、能否再修改 Enumerable、Writable（特殊，把 Configurable 设置为 false 后，这个属性也能被修改）、Value（更特殊，看下面例子）、Configurable 属性这四个属性;
  - `[[Enumerable]]`: 能否迭代, 使用 `for - in`循环返回这个属性;
  - `[[Writable]]`: 能否修改值, 区别 `[[configurable]]`;
  - `[[Value]]`: 数据值的位置. 默认为 underfined. 读取值和设定值都是这个特性.

- 访问器属性

  不包含数据值, 主要是设定 `setter` 和 `getter` 函数.

  - `[[Configurable]]`: 一样;
  - `[[Enumerable]]`: 一样;
  - `[[Get]]`: 读取属性时调用的函数.
  - `[[Set]]`: 写入时调用的函数.

  可以利用这俩来像 python 的魔法方法一样.

  ```javascript
  var book = {
    _year: 2004,
    edition: 1
  };

  Object.defineProperty(book, "year", {
    get: function() {
      return this._year;
    },

    set: function(newValue) {
      this._year = newValue;
    }
  });

  console.log(book.year);
  //2004

  book.year = 2005;

  console.log(book.year);
  //2005
    })
  ```

  **注意:**

  1. 下划线是表示只能通过对象方法访问的属性, 就是 set 和 get.
  2. 注意只指定 getter 意味着属性不能写, 写入会被忽略;
  3. 只指定 setter 不能读;
  4. 单设置一个严格模式下报错.

#### 二者之间的差别

[深入理解这数据属性和访问器属性](https://www.zhihu.com/question/295168343)

```javascript
// 声明一个对象
const obj = {};

// 数据属性
Object.defineProperty(obj, "data", {
	value: 0,
	writable: true,
	configurable: true,
	enumerable: true
});
// 等价于
obj.data = 0;

// 访问器属性
Object.defineProperty(obj, "accessor", {
	set(val) {
		this.data = val;
	},
	get() {
		return this.data;
	},
	configurable: true,
	enumerable: true
});
```

这两个属性都是定义对象属性的方法, 二者不同但是大体上是一样的. 二者之间可以相互转换, 只需要设定相应的特性就可以了, 会自动放弃另一个.

#### 访问特性和修改特性

修改属性的默认特性, 只能通过 `Object.defineProperty()` 和 `Object.defineProperties()`

需要三个参数: 属性所在的对象, 属性的名字和一个描述符对象. 描述符对象的属性是一个特性.

可以同时设置多个属性：

```javascript
var book = {
	_year: 2004,
	edition: 1
};

Object.defineProperties(book, {
	year: {
		value: 2005
	},
	edition: {
		value: 3
	}
});

console.log(book.year);

console.log(book.edition);
```

注意:

1. 调用`Object.defineProperty()` 不指定的特性默认都为 false;
2. 如果设置了 `configurable` 为 false, 则无法该回来了. 也不能设置其他属性的值了, 除了下面的特例;
3. `writable` 仍可以自由设置. `value` 只有当 `writable`和`configurable` 都为 false 时才不可以设置.

读取属性的特性可以使用 `Object.getOwnPropertyDescriptor()`, 需要两个参数, 对象名和属性名.

### 创建对象

1. 字面量;
2. 工厂模式;

   ```javascript
   function createPerson(name, age, job) {
   	var o = new Object();
   	o.name = name;
   	o.age = age;
   	o.job = job;
   	o.sayName = function () {
   		alert(this.name);
   	};
   	return o;
   }
   var person1 = createPerson("Nicholas", 29, "Software Engineer");
   var person2 = createPerson("Greg", 27, "Doctor");
   ```

   用函数来封装数据接口

   解决了重复创建对象的问题, 但是无法判断实例的类型, 就是不知道具体是"人" 还是 "猫".

3. 构造函数

   解决了工厂模式的问题, 可以使用 `instanceOf` 来判断实例的类型.

   new 创建对象的过程:

   1. 创建新对象;
   2. this 指向这个新对象;
   3. 执行构造函数的代码, 创建属性;
   4. 返回新对象.

   此外, 构造函数也是函数, 可以调用. 注意 this 的指向问题. 任何函数都可以用 new 来调用, 创建对象.

   不可以不要 `new` 单独使用构造函数来创建对象.

   缺点是: 多个实例彼此独立, 有些方法没必要定义多次, 浪费内存.

4. 原型模式

   我们创建的每个函数都有一个 prototype（原型）属性，这个属性是一个指针，指向一个对象，而这个对象的用途是包含可以由特定类型的所有实例共享的属性和方法, 就是它里面的所有属性和方法都可以被实例调用, 并且只定义一次, 所有的实例用的都是同一个.

   ```javascript
   function Book() {}

   Book.prototype.name = "Nicholas";

   var book1 = new Book();

   console.log(book1.name);
   // Nicholas
   ```

5. 组合使用原型模式和构造函数

	可以将共享的属性放到原型中定义, 把实例自己单独的属性放到构造函数中定义. 组合形式是注意使用的方式.

6. 动态原型模式

	```javascript
	const BookTwo = function (name, edition) {
	this.name = name;
	this.edition = edition;
	this.getEdition = function () {
		return this.edition;
	};

	if (typeof this.getName !== "function") {
		BookTwo.proptotype.getName = function () {
			return this.name;
		};
	}
	};
	```

	不是重写原型函数, 之前定义的还在. 可以在第一次 new 时, **初始化**一个原型属性.

	这么写, 会报错, 因为 `this.getName` 并没有声明, 却已经用来判断类型了

7. 寄生函数构造函数形式
   ````javascript
   function Book(name, edition){
	   var myObg=new Object();
	   myObg.name = name;
	   myObg.getName=function(){
		   return this.name
	   }
	   return myObg
	}
   ````

	寄生构造函数, 和工厂函数几乎一样, 除了使用 `new` 来创建对象. 它的作用是: **利用工厂模式的函数来封装对象, 用 new 来改变 this 的指向.**

	缺点依旧是: 人不人, 猫不猫
8. 稳妥构造函数模式

	没有公共属性, 不使用 `this` 对象, 也不使用 `new` 来指定 this 对象.

	目的: 防止外界更改自己的属性. 如下, 外界永远都不可能改变 `name` 和 `edition`

	```javascript
	function Book(name, edition){
		let o = new Array();
		let name = name;
		let edition = edition;
		o.getEdition= function(){
			return edition;
		}
		return o
	}
	Const book1 = Book("Dom", "2005")
	```

**总结** 

- 如果需要创建一个单例对象, 使用 **字面量** 来定义;

- 如果需要多个实例对象, 并且没有共享属性, 那么就 **构造函数**; 

- 如果有属性共享, 就把属性定义到 **原型对象** 上去.

### 理解原型对象

#### 函数, 原型对象和实例

无论什么时候, 创建一个函数, 就会自动创建一个原型对象, 并且为这个函数创建 `prototype` 属性, 指向这个原型对象; 这个函数就只是函数对象, 原型对象才是创建的对象.

```javascript
console.log(Object.getOwnPropertyNames(Book.prototype));
// [ 'constructor', 'name', 'plus' ]
console.log(Object.getOwnPropertyNames(Book));
// [ 'length', 'name', 'arguments', 'caller', 'prototype' ]
```

这个原型对象自动创建一个 `constructor` 属性, 指向最开始创建的函数.

此后, 原型对象会继承它父类(如果有)的方法和属性.

创建一个实例后, 它会有一个指针 `__proto__` 指向原型对象. 没有标准方式访问, 浏览器有一个 `Object.getPrototypeOf()` 可以访问. 可以用 `isPrototypeOf()` (方法, 不是关键字)来看它和一个原型对象的关系.

#### 解释器查找属性的顺序

解释器按照名字查找, 先找**实例对象**, 后找**原型对象**.

所以实例对象的同名属性会屏蔽原型对象, 不能访问但没改变它们.

可以用 `delete()` 恢复访问.

可以用 `hasOwnProperty()` 来确定有没有实例属性.

用 `in` 操作符可以判断实例和原型对象中是否存在这个属性, 可以判断是否属于原型对象.

#### 得到属性

`for (let property in Object)` 来得到可以迭代的所有属性. `[[Enumerable]]` 为 true 的.

还可以使用 `Object.key()` 返回一个迭代属性的数组. 实例只返回自己的, for-in 返回全部的.

`Object.getOwnPropertyNames()` 返回所有的包括不可迭代的属性的数组.

#### 原型对象的动态性

重写原型对象会打破与函数的 `constructor` 关系.

注意什么叫做 **重写**.

```javascript
function Book() {}

Book.prototype.name = "Tom";

book1 = new Book();

Book.prototype = {
	constructor: Book,
	name: "Wang",
	sayName: function () {
		return this.name;
	}
};

book2 = new Book();

// book1 没有 sayname
console.log(book2.sayName(), book1.sayName);

// book1 no chage
console.log(book2.name, book1.name);

for (let property in book2) {
	console.log(property);
}
console.log("-------");
for (let property in book1) {
	console.log(property);
}

// book1 no longer is belong to Book, (Ture, false)
console.log(book2 instanceof Book, book1 instanceof Book);

// ture false
console.log(
	Book.prototype.isPrototypeOf(book2),
	Book.prototype.isPrototypeOf(book1)
);

console.log(Book.constructor);
```

重写 prototype:

1. 写不写 constructor 没有影响, 它已经是一个历史的产物了, 没什么作用了. 他不会影响 `instanceof` 的结果; 
2. 重写之前的实例: 不会继承, 不改变, `instanceof` `isPrototypeOf` 均改变;
3. 之后的实例, 一切正常

只影响重写之前的属性.

### 继承

大多数语言都支持两种继承方式：接口继承和实现继承。接口继承只继承方法签名，而实现继承则继承实际的方法。

- [ ] 两种继承方式的了解

ECMAScript 只支持实现继承, 并且实现继承主要依靠原型链来实现.

#### 原型链

主要思想: 让子对象的原型对象等于父对象的实例

```javascript
	Book.prototype = new SuperBook()
```

继承完之后(要明白它们这些属性什么时候进入实例, 在哪里存储):

1. 父对象的构造函数的属性, 在 `new` 的过程中进入到了子类的原型对象中;
2. 子对象的构造函数定义的属性在使用 `new` 时在实例中创建相应的属性;
3. 父对象的原型属性由子对象的原型对象中的 `__proto__` 指向;
3. 子对象的原型被重写, 所以继承之前定义的原型属性不存在了;
4. 实例 `instanceof`, `isPrototypeOf` 均为 true;
5. 继承完之后, 不能再用字面量的方法更改原型对象了, 因为这会重写原型对象, 打破原型链.

这张图很重要, 可以清楚的描述原型链的组成:
![](https://cdn.jsdelivr.net/gh/zzan1/markdownPicture/normal/20200607122052.png)

#### 原型链存在的问题

包含引用类型值的原型属性会被所有实例共享, 一个实例改变了值, 则全部改变. 基本类型的值不会改变. 注意这和实例属性覆盖原型属性不一样. **因为两个实例的属性指向了同一个内存地址**.

```javascript
let Book = function () {};
Book.prototype.name = "DOM";
Book.prototype.list = [12, 12, 31, 42];
let book1 = new Book();
let book2 = new Book();
book1.name.split().reverse().join();

console.log(book1.name, book2.name);
// DOM DOM
//
book1.list.pop();

console.log(book1.list, book2.list);

//[ 12, 12, 31 ] [ 12, 12, 31 ]
```

原型链的第二个问题是：**在创建子类型的实例时，不能向父类的构造函数中传递参数**. 因为`new SuperType()` 是直接在子类的原型上的, 如果传递一个参数, 则子类的所有的实例这部分属性是一样的.

#### 借用构造函数

```javascript
function SuperType() {
	this.list = [1, 2, 3, 4];
}
function SubType() {
	SuperType.call(this);
}

let book1 = new SubType();
book1.list.pop();
let book2 = new SubType();
console.log(book1.list, book2.list);
console.log(book1.list === book2.list);
// [ 1, 2, 3 ] [ 1, 2, 3, 4 ]
// false
```

这种技术的思想很简单: **在子类型构造函数中调用父类的构造函数**. 当创建实例时, 会绑定不同的 `this` 的对象, 然后就避免了继承时引用类型使用相同的内存地址的为题.

但问题是: 父类只能是一个函数, 并且父类的原型属性无法通过 `this` 来绑定, 仍然不能使用引用类型的值.

**为什么只能是函数呢?** 函数是**在特定执行环境中执行代码的对象**, 因此可以通过 `call` `apply` 来通过绑定不同的执行环境来实现创建不同的对象.

要学习这种思想的使用.

#### 组合继承

```javascript
function SuperType() {
	this.list = [1, 2, 3, 4];
}
SuperType.prototype.name = "yi";
function SubType() {
	SuperType.call(this);
}

SubType.prototype = new SuperType();

let book1 = new SubType();
book1.list.pop();
let book2 = new SubType();
console.log(book1.list, book2.list);
console.log(book1.list === book2.list);
// [ 1, 2, 3 ] [ 1, 2, 3, 4 ]
// false
console.log(book1.name, book2.name);
console.log(book1 instanceof SubType, book1 instanceof SuperType);
console.log(
	SuperType.prototype.isPrototypeOf(book1),
	SubType.prototype.isPrototypeOf(book1)
);
// yi yi
// true true
// true true
```

把原型链和构造函数继承结合起来. 使用 `SuperType.call(this)` 解决原型链的两个问题.

#### 不是继承的对象复制: 原型式继承

```javascript
function obj(base) {
	function NewObj() {}
	NewObj.prototype = base;
	return new NewObj();
}
SubType.prototype = obj(SuperType.prototype)
```

思想是: 既然 `SubType.prototype = new SuperType()` 中创建 `SuperType.call(this)` 同样的属性, 而且前面只是复制一下原型链, 副作用是 `new` 引起的. 那么我就在中间插入一个没有构造函数属性的对象, 只是单纯的复制一下原型链.

现在可以使用 `Object.create()` 来实现上面这个函数. 第一个参数就是 `baseObj`, 传递一个想要复制的对象. 第二个参数可以新增加属性, 格式像 `Object.defineProperty()` 第二个参数一样, 需要指定特性;

#### 寄生式继承

和原型模式 `obj()` `Object.create()` 没什么区别, 在重写原型对象后, 又为原型对象添加属性, 实现复制, 加强了基底对象.

#### 寄生组合式继承

**组合式继承的缺点**: 在第一次调用 SuperType 构造函数时，SubType.prototype 会得到两个属性：name 和 colors；它们都是 SuperType 的实例属性，只不过现在位于 SubType 的原型中。当调用 SubType 构造函数时，又会调用一次 SuperType 构造函数，这一次又在新对象上创建了实例属性 name 和 colors。于是，这两个属性就屏蔽了原型中的两个同名属性. 有两组 name 和 colors 属性：一组在实例上，一组在 SubType 原型中。

一个完整的寄生式组合继承

```javascript
function inheritPrototype(SubType, SuperType) {
	let MediaObj = function () {};
	// 取消掉 SuperType 的构造函数, 只复制原型
	MediaObj.prototype = SuperType.prototype;
	SubType.prototype = new MediaObj();
}

function SuperType(name, list) {
	this.name = name;
	this.list = list;
}
SuperType.prototype.sayName = function () {
	console.log(this.name);
};
function SubType(age, name, list) {
	this.age = age;
	// 实现传参 实现 不同的引用类型u值内存地址
	SuperType.call(this, name, list);
}
inheritPrototype(SubType, SuperType);
```

寄生组合式继承改变原型链的缺点了吗?

解决了. **传递参数:** 通过在子类中调用 `call` 可以解决向父类构造函数中传递参数. **引用类型的值**, `call` 函数已经为不同的引用类型的值绑定了不同的对象. **重复定义属性**, 因为现在不再执行 `new SuperType()` 而是直接将 `SuperType.prototype` , 复制给了中间对象的原型对象. 没用 `new` 再次执行构造函数的过程了.

问题:

1. `instanceOf` 和 `isPrototypeOf` 的工作原理是什么;
	
	都是依托原型链.

## 函数表达式

函数表达式和函数声明的区别在于: **表达式不会提升**.

函数声明的提升意味着:

```javascript
if (condition) {
	function alert1() {
		console.log("true");
	}
} else {
	function alert1() {
		console.log("false");
	}
}
```

永远不要这么做, 现在 `nodeJs` 没什么问题. 可能会引起问题, 因为执行代码之前, 就开始声明函数了, 没有循环的效果, 有些情况下会出错.

同样, 提升变量的`var`也应该注意;

### 递归会出现的问题
把递归函数赋给另外一个变量, 然后取消掉这个原本的函数指向. 就会出错, 因为递归函数内找不见这个函数的指针了.

原来使用 `argument.callee` 来指向永远指向函数, 但是现在严格模式不让用这个了, 所以不推荐使用了.

替代方法:

```javascript
var factorial = (function f(num){
	if(num <=1){
		return 1;
	}else{
		return num*f(num - 1 );
	}
})
```

使用的是*命名函数表达式*, 函数名只作为函数内部的一个变量.

即使把 `factorial` 赋给别的变量, `f()` 永远有效. 推荐这么用, 思想就是把会删除的变量作为一个中间件, 确保底层的安全, 也是确保退路的一种思想.

### 闭包
#### 理解闭包
首先, 闭包是: **有权访问另一个函数作用域的变量的函数**.

来理解闭包实现的**原理**: 首先明白执行环境, 变量对象和作用域链, 这里又比之前多了一些内容.

1. **创建** 函数时, 会创建一个预先包含这个函数之外所有变量对象的作用链,最先的变量对象是这个函数最近邻的执行环境, 最末尾的是全局环境. 这个作用链保存在函数对象的 `[[Scope]]` 中.

2. 当 **调用** 函数的时候, 创建这个函数的变量对象, 然后复制 `[[Scope]]` 中的作用链域, 并把新建的变量(称为活动对象, 实际这个说法有的累赘了)对象推入到作用链域的最前端. 作用域链本质上是一个指向变量对象的指针列表，它只引用但不实际包含变量对象。

3. 当函数**调用完毕之后**, 它的作用链域销毁, 它的变量对象不一定, 取决于别的作用链域是否还指向它. 

所以, 这里就是闭包的原理了. 把它**定义在一个函数内部**, 这个函数即使不再调用, 它的变量对象因为被内部的闭包使用, 不能被标记清除. 因此, 闭包可以访问这个函数作用域中的变量. 

#### 闭包的缺陷
因为闭包引用了其包裹函数的变量对象, 这个变量对象不能及时销毁, 所以闭包会占用更多的内存. 

因此, 只在绝对必要的情况下使用闭包. 使用完毕之后应及时解除对闭包的引用. 下面是一个闭包使用的例子:

```javascript
function creatComparisonFunction(propertyName) {
	return function(object1, object2) {
		let value1 = object1[propertyName];
		// 闭包函数使用包裹函数的变量
		let value2 = object2[propertyName];
		console.log(value2, value1);
	};
}
let consoleName = creatComparisonFunction("name");
// 这里把闭包引出来使用
let Book = function(name) {
	this.name = name;
};
book1 = new Book('Dom');
book2 = new Book('Bom');
consoleName(book1, book2);
// Bom Dom
// 解除对闭包函数的引用, 标记清除
consoleName = null;
```

需要记住的图
![](https://cdn.jsdelivr.net/gh/zzan1/markdownPicture/normal/20200609195324.png)

另外, 闭包只能访问包裹函数变量的最后一个值. 因为它包含的变量对象里面只有一个变量, 这个变量不可能平白的被复制多个并且有不同的值.

```javascript
function createFunction() {
	let result = new Array();

	for (var i = 0; i < 10; i++) {
		result[i] = function() {
			return i;
		};
	}
	return result;
}
console.log(createFunction()[2]())
```

这个每个闭包函数的执行结果都是 10, 因为访问的是包裹函数变量对象的同一个 i 变量.

```javascript
function createFunction() {
	let result = new Array();

	for (let i = 0; i < 10; i++) {
		result[i] = function() {
			return i;
		};
	}
	return result;
}
console.log(createFunction()[2]())
```

这个结果就是[0, 1, 2, ...] 正常结果了. 原因是: 使用 `let` 定义的属性不在函数的变量对象中, 所以这个 `let` 定义的属性可以不只是一个. 这个 `let` 定义的变量在这里函数变量对象的中间. 它们十个函数引用的是不同的 `i` 变量对象, 从包裹函数开始分开了. `var` 定义的变量处于包裹函数的变量对象内. 

另外一种方法是一样的道理, **从中截掉这个作用链域**. 

```javascript
function createFunction() {
	let result = new Array();

	for (var i = 0; i < 10; i++) {
		result[i] = function (num){
			return function(){
				return num;
			}
		}(i)
	}
	return result;
}
console.log(createFunction()[8]())
```

闭包的第三个问题: this 的指向问题


```javascript
var name = 'The'

var object = {
	name: "Obj",
	getNameFunc: function(){
		// var that = this 这个this 就指调用 function 的对象
		return function(){
			return this.name;
		}
	}
}

console.log(object.getNameFunc()())
```

这段代码在 `nodeJs` 输出为 underfined, 在浏览器中输出 the, 意思指向了 windows 对象. 

直接说原理: this 的值由谁调用这个函数来决定.

只找一层, 比如, 第一个执行函数时, obj 调用的第二个 func 需要找 this, 但调用它的是第一个 func, 他不是一个this需要的对象, 即使它的 argumets 或者 变量对象中有一个 name 变量. 如果是第一个 func 需要找 this, 那么就去 obj 去找了. 

引入this的初衷就是**想在原型继承的情况下，拿到函数的调用者。如果存在原型链, 那么他就找原型链上的对象.如果会找不到，那就让this指向全局对象吧**。
 
如果要使用包裹函数的变量, 就把它的 this 提前赋给 that, 手动的指明 this 的指向. 

还有一个比较有意思的事情:
```javascript
var name = "The Windows";
var object = {
	name: "my Object",

	getName: function() {
		return this.name;
	}
};

(object.getName)() //My
(object.getName = object.getName)() // global
```

### 模仿块级作用域
利用匿名函数可以用来模仿块级作用域.

```javascript
{
	(function() {
		// 块级作用区域
		for (var i = 0; i<10;i++){
			console.log(i);
		}
	})();
}

console.log(i)
```

创建一个立即执行的函数, `i` 变量是它的局部变量, 外部访问不到.

函数表达式的后面可以跟圆括号, 立即执行. 但是, 必须用**小括号把函数体包裹起来**. 如果不包裹, 会出错, 因为认为 `function` 是一个函数声明的开始, 必须有一个名字.

这种技术很有用, 经常在全局作用域中被用在函数外部，从而**限制向全局作用域中添加过多的变量和函数**. 一般来说，我们都应该尽量少向全局作用域中添加变量和函数。在一个由很多开发人员共同参与的大型应用程序中，过多的全局变量和函数很容易导致命名冲突.

### 私有变量
首先, javascript里所有的对象属性都是公有的. 函数有私有变量, **在内部定义的变量, 函数, 和它的参数** 都是私有变量. 

一般来说, 私有变量都不能被外部访问. 但是, 利用闭包, 可以在外部访问私有变量, 称为**特权方法**.

所以, 可以利用函数的这种特性, 给**对象添加私有变量和特权方法**.

#### 构造函数模式

```javascript
function Person(name){
	this.getName = function(){
		return name;
	}
	this.setName =function(value){
		name = value;
	}
}

person1 = new Person('Dom');
```

显而易见, 这个 `name` 变量不可能被外部直接访问, 而是使用两个闭包函数来访问和设置它的值. 

那么缺点是: 构造函数的缺点, 人不人, 猫不猫.

#### 私有作用域
```javascript
{
	(function() {
		// 私有变量
		var privateVariable = 10;
		// 私有方法
		function privateFunction(){
			return false;
		}
		// 构造函数
		MyObject = function(){

		}
		// publicMethod
		MyObject.prototype.publicMethod = function(){
			privateVariable++;
			privateFunction();
		}
	})();
}
```

一个私有作用域, 里面定义了私有变量和方法. 注意在定义构造函数时, 没有使用函数声明的形式, 而是使用匿名函数的形式. 这是因为: **函数声明只能创建一个局部函数, 这里的函数表达式没有使用 `var`, 而是直接定义了一个全局的变量**. 这样在严格模式下是错的.

这样的缺点很明显: 所有的实例都是同一个私有变量和私有方法, 实例之间相互影响. 每个实例都没有自己的私有变量.

#### 模块模式
由于上面的缺点, 不能有多个实例. 实际上它有一个特殊的用处, 可以只用在 **单例** 中.

javascript 一般的单例是由字面量来创建的.它的这个对象只有它自己一个实例.

结合一下, 就有:

```javascript
var singleInstance = (function() {
	// 私有变量和函数
	var privateVariable = "10";
	function privateFunction() {
		return false;
	}

	// 公共接口
	return {
		publicVariable: true,

		publicMethod: function() {
			privateVariable++;
			return privateFunction();
		}
	};
})();
```

理解一下这个过程: 首先利用立即执行的匿名函数来创建一个私有区域, 然后这个函数返回一个字面量定义的单例对象给外部的变量. 私有区域里的变量和方法只有这个字面量可以访问到, 实现了一个对外的单独接口.

**增强的单例模式**, 即在返回对象之前加入对其增强的代码。这种增强的模块模式适合那些单例必须是某种类型的实例，同时还必须添加某些属性和（或）方法对其加以增强的情况.

```javascript
var singleInstance = (function() {
	// 私有变量和函数
	var privateVariable = "10";
	function privateFunction() {
		return false;
	}

	// 公共接口
	var instance = new Array();

	instance.publicVariable = true;

	instance.publicVariable = function(){
		privateVariable++;
		return privateFunction()
	}

	return instance

})();
```

直接返回一个实例.

## 附上一个思维导图

![](https://cdn.jsdelivr.net/gh/zzan1/markdownPicture/normal/面向对象的程序设计.png)
