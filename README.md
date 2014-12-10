### JavaScript-oo

#### 类

类（Class）定义了一件事物的抽象特点。通常来说，类定义了事物的属性和它可以做到的（它的行为）。
举例来说，“狗”这个类会包含狗的一切基础特征，即所有“狗”都共有的特征或行为，例如它的孕育、毛皮颜色和吠叫的能力。
类可以为程序提供模版和结构。一个类的方法和属性被称为“成员”。

#### 对象

在面向对象编程中，类是对象的模板，对象就是类的实例，例如“狗”是一个类，而这只狗就是一个对象，这只叫“hello dog”的狗就是一个实例。

#### 如何定义类

##### 1. 构造函数法

这是经典方法，也是教科书必教的方法。它用构造函数模拟"类"，在其内部用this关键字指代实例对象。

```javascript

function Dog() {
	this.name = "大毛";
}
```
生成实例的时候，使用new关键字。

```javascript
var dog1 = new Dog();
alert(dog1.name); // 大毛

```

类的属性和方法，还可以定义在构造函数的prototype对象之上。

```javascript

Dog.prototype.makeSound = function(){
	alert("旺旺旺");
}

```

##### 2. Object.create()法

为了解决"构造函数法"的缺点，更方便地生成对象，Javascript的国际标准ECMAScript第五版（目前通行的是第三版），提出了一个新的方法Object.create()。

用这个方法，"类"就是一个对象，不是函数。

```javascript

var Dog = {
	name: "大毛",
	makeSound: function(){ alert("旺旺旺"); }
};

```

然后，直接用Object.create()生成实例，不需要用到new。

```javascript

var dog1 = Object.create(Dog);
alert(dog1.name); // 大毛
dog1.makeSound(); // 旺旺旺

```
目前，各大浏览器的最新版本（包括IE9）都部署了这个方法。如果遇到老式浏览器，可以用下面的代码自行部署。

```javascript
if (!Object.create) {
	Object.create = function (o) {
		function F() {}
			F.prototype = o;
			return new F();
		};
	}

```

这种方法比"构造函数法"简单，但是不能实现私有属性和私有方法，实例对象之间也不能共享数据，对"类"的模拟不够全面。

##### 3. 极简主义法

###### 3.1 封装

这种方法不使用this和prototype，代码部署起来非常简单，这大概也是它被叫做"极简主义法"的原因。

首先，它也是用一个对象模拟"类"。在这个类里面，定义一个构造函数createNew()，用来生成实例。

```javascript

var Dog = {
	createNew: function(){
		// some code here
	}
};

```
然后，在createNew()里面，定义一个实例对象，把这个实例对象作为返回值。

```javascript

var Dog = {
	createNew: function(){
		var dog = {};
			dog.name = "大毛";
			dog.makeSound = function(){ alert("旺旺旺"); };
			return dog;
		}
	};

```
使用的时候，调用createNew()方法，就可以得到实例对象。

```javascript

var dog1 = Dog.createNew();
dog1.makeSound(); // 旺旺旺

```

这种方法的好处是，容易理解，结构清晰优雅，符合传统的"面向对象编程"的构造，因此可以方便地部署下面的特性。

###### 3.2 继承

让一个类继承另一个类，实现起来很方便。只要在前者的createNew()方法中，调用后者的createNew()方法即可。

先定义一个Animal类。

```javascript
var Animal = {
	createNew: function(){
		var animal = {};
			animal.sleep = function(){ alert("睡懒觉"); };
			return animal;
		}
	};

```
然后，在Cat的createNew()方法中，调用Animal的createNew()方法。

```javascript

var Cat = {
	createNew: function(){
		var cat = Animal.createNew();
		cat.name = "大毛";
		cat.makeSound = function(){ alert("喵喵喵"); };
		return cat;
	}
};
```
这样得到的Cat实例，就会同时继承Cat类和Animal类。

```javascript
var cat1 = Cat.createNew();
cat1.sleep(); // 睡懒觉

```
###### 3.3 私有属性和私有方法

在createNew()方法中，只要不是定义在cat对象上的方法和属性，都是私有的。

```javascript
var Cat = {
	createNew: function(){
		var cat = {};
			var sound = "喵喵喵";
			cat.makeSound = function(){ alert(sound); };
			return cat;
		}
	};
```
上例的内部变量sound，外部无法读取，只有通过cat的公有方法makeSound()来读取。

```javascript
	var cat1 = Cat.createNew();
	alert(cat1.sound); // undefined

```

######	3.4 数据共享

有时候，我们需要所有实例对象，能够读写同一项内部数据。这个时候，只要把这个内部数据，封装在类对象的里面、createNew()方法的外面即可。

```javascript
	var Cat = {
		sound : "喵喵喵",
		createNew: function(){
			var cat = {};
			cat.makeSound = function(){ alert(Cat.sound); };
			cat.changeSound = function(x){ Cat.sound = x; };
				return cat;
			}
		};

```

然后，生成两个实例对象：

```javascript
		var cat1 = Cat.createNew();
		var cat2 = Cat.createNew();

		cat1.makeSound(); // 喵喵喵

```

这时，如果有一个实例对象，修改了共享的数据，另一个实例对象也会受到影响。

```javascript
		cat2.changeSound("啦啦啦");
		cat1.makeSound(); // 啦啦啦

```
#### 什么是继承

继承是面向对象编程的一种特性，其实开发一个东西跟造物主创造世界一样，那我就通过造物主创造世界来解释一下面向对象和继承。

造物主造了一些动物，造人的时候，因为每个人都不一样，所以他要一个一个的制造，也就是先做一个人类的模型，然后给这个人的模型，添加各种参数（身高、姓名、体重等），然后再添加各种行为（走路能力、手臂摆动、移动货物、跑多块、跳多高）。

由于要造的人太多，造物主造烦了，于是他观察之前造出来的人，好多工作都是重复的，比如有一大批人都有走路这个行为，也可以手臂摆动，好多人身高等也相同，于是他就用了一个工具（function）来创建人类，首先设置好工具的功能（创建一个人模型，然后添加各种参数和行为），然后向工具中传递一些个性的东西（例如姓名等）。这样造人的时候，就方便多了，只需要向这个工具中传递一些人与人不同的东西（例如姓名），就可以造出一个人来，省下了大量的重复工作。

造出的人就是一个对象，这个对象包含了一些属性和方法，分别就是姓名、身高和走动、吃饭等等。

造物主继续造其他动物，突然发现对动物的某个属性设置错了，于是不得已把所有动物又修改了一边，这时候发现，动物和人类也有共同之处，有一部分重复的工作，于是造物主就想出了继承这个解决方法。大体思路是这样的：

动物和人都有一些共同的属性和行为，包括名字、重量和吃东西、排泄行为等等。这里要进行更高层的抽象，所以显然不能用姓名和体重之类，因为动物没有姓名、体重这类名字，而需要用名字、重量。

造物主抽象出来了这一切，命名为了“动物”这个模版。然后造物主要创建“陆上动物”这个模版，陆上动物也是动物，具有动物所有的属性和行为，并且增加了奔跑等行为，造物主就让“陆上动物”模版继承了“动物”模版，然后对“陆上动物”模版增加了奔跑行为。同样的，“海上动物”这个模版，就没有奔跑行为了，但是具有了游泳行为。

人类属于陆上动物，所以造物主定义“人类”模版继承了“陆上动物”的所有内容，并添加了人类独有的站立行走、感情等等属性和行为。至于“蛇类”，造物主定义它们没有腿、有毒等等属性和行为。

然后开始创造人这个个体，这时候已经有了人类这个模版，只需要基于这个模版，生成一个实例，然后对这个实例添加每个人不同的属性和行为，这样就创建了一个个个体。

造物主突然发现，自己设置的陆上动物奔跑速度太低了，他没有挨个把所有陆上动物重新设置一遍，而是找到“陆上动物”这个模版，设置了一下“奔跑速度”这个属性，所有继承“陆上动物”的实例，奔跑速度全都被更新好了。

造物主对现在的工作太满意了，创建新个体和修改之前的 BUG 都特别方便。

所以说，上帝是个程序员。

这就是面向对象编程的大体思路以及继承的作用，先定义高度抽象的基类，然后再继承基类扩展出来更详细的类，然后不断的具体具体，直到可以创建具体个体的类。这时候，就可以直接通过这个类创建个体来实现相关功能。

##### 继承的实现方法

JavaScript 中的继承比较奇葩，无法实现接口继承，只能依靠原型继承。

###### 原型链

原型就是一个对象，通过构造函数创建出来的实例会有指针指向原型得到原型的属性和方法。这样，实例对象就带有构造函数的属性方法和原型的属性方法，然后将需要继承的构造函数的原型指向这个实例，即可拥有这个实例的所有属性方法实现继承。

看下面演示代码：
```javascript

//声明超类，通过构造函数和原型添加有关属性和方法
function Super(){
	this.property = true;
}
Super.prototype.getSuperValue = function() {
	return this.property;
};
//声明子类的构造函数
function SubType() {
	this.subproperty = false;
}
//将子类的原型指向超类的实例，得到超类的一切
SubType.prototype = new Super();
SubType.prototype.constructor = SubType;
SubType.prototype.getSubValue = function(){
	return this.subproperty;
};
//由子类创建对象，测试是否继承超类方法和属性
var instance = new SubType();
console.log(instance.getSuperValue());
```

所有函数的默认原型都是 Object 的实例，因此默认原型都会包含一个内部指针，指向 Object.prototype。

使用 instanceof 和 isPrototypeOf 可以确定原型和实例的关系：
```javascript
instance instanceof Object;
Object.prototype.isPrototypeOf(instance);
```
使用原型链的时候，需要谨慎的定义方法。子类需要重写超类型的某个方法或者扩充，一定要放在替换原型的语句后面，这样才能生效。此外，通过原型链实现继承时，不能使用对象字面量创建原型方法，这样会重写原型链：
```javascript
......
SubType.prototype = new Super();
SubType.prototype = {
	....
};
```
#### 什么是封装
Javascript是一种基于对象（object-based）的语言，你遇到的所有东西几乎都是对象。但是，它又不是一种真正的面向对象编程（OOP）语言，因为它的语法中没有class（类）。

那么，如果我们要把"属性"（property）和"方法"（method），封装成一个对象，甚至要从原型对象生成一个实例对象，我们应该怎么做呢？

##### 1. 生成对象的原始模式

假定我们把猫看成一个对象，它有"名字"和"颜色"两个属性。
```javascript
var Cat = {
	name : '',
	color : ''
}
```
现在，我们需要根据这个原型对象的规格（schema），生成两个实例对象。
```javascript
var cat1 = {}; // 创建一个空对象
	cat1.name = "大毛"; // 按照原型对象的属性赋值
	cat1.color = "黄色";
	var cat2 = {};
		cat2.name = "二毛";
		cat2.color = "黑色";

```
好了，这就是最简单的封装了，把两个属性封装在一个对象里面。但是，这样的写法有两个缺点，一是如果多生成几个实例，写起来就非常麻烦；二是实例与原型之间，没有任何办法，可以看出有什么联系。


##### 2. 原始模式的改进

我们可以写一个函数，解决代码重复的问题。
```javascript

function Cat(name,color){
	return {
		name:name,
		color:color
	}
}
```
然后生成实例对象，就等于是在调用函数：
```javascript
var cat1 = Cat("大毛","黄色");
var cat2 = Cat("二毛","黑色");
```
这种方法的问题依然是，cat1和cat2之间没有内在的联系，不能反映出它们是同一个原型对象的实例

##### 3. 构造函数模式

为了解决从原型对象生成实例的问题，Javascript提供了一个构造函数（Constructor）模式。

所谓"构造函数"，其实就是一个普通函数，但是内部使用了this变量。对构造函数使用new运算符，就能生成实例，并且this变量会绑定在实例对象上。

比如，猫的原型对象现在可以这样写，
```javascript
function Cat(name,color){
	this.name=name;
	this.color=color;
}
```
我们现在就可以生成实例对象了。
```javascript
var cat1 = new Cat("大毛","黄色");
var cat2 = new Cat("二毛","黑色");
alert(cat1.name); // 大毛
alert(cat1.color); // 黄色
```
这时cat1和cat2会自动含有一个constructor属性，指向它们的构造函数。
```javascript
alert(cat1.constructor == Cat); //true
alert(cat2.constructor == Cat); //true
```
Javascript还提供了一个instanceof运算符，验证原型对象与实例对象之间的关系。
```javascript
alert(cat1 instanceof Cat); //true
alert(cat2 instanceof Cat); //true
```
##### 4. 构造函数模式的问题

构造函数方法很好用，但是存在一个浪费内存的问题。

请看，我们现在为Cat对象添加一个不变的属性"type"（种类），再添加一个方法eat（吃老鼠）。那么，原型对象Cat就变成了下面这样：
```javascript
function Cat(name,color){
	this.name = name;
	this.color = color;
	this.type = "猫科动物";
	this.eat = function(){alert("吃老鼠");};
}
```
还是采用同样的方法，生成实例：
```javascript
var cat1 = new Cat("大毛","黄色");
var cat2 = new Cat ("二毛","黑色");
alert(cat1.type); // 猫科动物
cat1.eat(); // 吃老鼠
```
表面上好像没什么问题，但是实际上这样做，有一个很大的弊端。那就是对于每一个实例对象，type属性和eat()方法都是一模一样的内容，每一次生成一个实例，都必须为重复的内容，多占用一些内存。这样既不环保，也缺乏效率。
```javascript
alert(cat1.eat == cat2.eat); //false
```
能不能让type属性和eat()方法在内存中只生成一次，然后所有实例都指向那个内存地址呢？回答是可以的。

##### 5. Prototype模式

Javascript规定，每一个构造函数都有一个prototype属性，指向另一个对象。这个对象的所有属性和方法，都会被构造函数的实例继承。

这意味着，我们可以把那些不变的属性和方法，直接定义在prototype对象上。
```javascript
function Cat(name,color){
	this.name = name;
	this.color = color;
}
Cat.prototype.type = "猫科动物";
Cat.prototype.eat = function(){alert("吃老鼠")};
```
然后，生成实例。
```javascript
var cat1 = new Cat("大毛","黄色");
var cat2 = new Cat("二毛","黑色");
alert(cat1.type); // 猫科动物
cat1.eat(); // 吃老鼠
```
这时所有实例的type属性和eat()方法，其实都是同一个内存地址，指向prototype对象，因此就提高了运行效率。
```javascript
alert(cat1.eat == cat2.eat); //true
```

##### Prototype模式的验证方法

为了配合prototype属性，Javascript定义了一些辅助方法，帮助我们使用它。，

###### 6.1 isPrototypeOf()

这个方法用来判断，某个proptotype对象和某个实例之间的关系。
```javascript
alert(Cat.prototype.isPrototypeOf(cat1)); //true
alert(Cat.prototype.isPrototypeOf(cat2)); //true
```
###### 6.2 hasOwnProperty()

每个实例对象都有一个hasOwnProperty()方法，用来判断某一个属性到底是本地属性，还是继承自prototype对象的属性。
```javascript
alert(cat1.hasOwnProperty("name")); // true
alert(cat1.hasOwnProperty("type")); // false
```
###### 6.3 in运算符

in运算符可以用来判断，某个实例是否含有某个属性，不管是不是本地属性。
```javascript
alert("name" in cat1); // true
alert("type" in cat1); // true
```
in运算符还可以用来遍历某个对象的所有属性。
```javascript
for(var prop in cat1) { alert("cat1["+prop+"]="+cat1[prop]); }
```

#### 什么是多态

##### 重载和覆盖

先来说明一下重载和覆盖的区别。重载的英文是 overload，覆盖的英文是 override。发现网上大多数人把 override 当成了重载，这个是不对的。重载和覆盖是有区别的。

重载的意思是，同一个名字的函数（注意这里包括函数）或方法可以有多个实现，他们依靠参数的类型和（或）参数的个数来区分识别。

而覆盖的意思是，子类中可以定义与父类中同名，并且参数类型和个数也相同的方法，这些方法的定义后，在子类的实例化对象中，父类中继承的这些同名方法将被隐藏。

##### 重载

javascript 中函数的参数是没有类型的，并且参数个数也是任意的，例如，尽管你可以定义一个：

```javascript
function add(a, b) {
	return a + b;
}
```
这样的函数，但是你仍然可以再调用它是带入任意多个参数，当然，参数类型也是任意的。至于是否出错，那是这个函数中所执行的内容来决定的，javascript 并不根据你指定的参数个数和参数类型来判断你调用的是哪个函数。

因此，要定义重载方法，就不能像强类型语言中那样做了。但是你仍然可以实现重载。就是通过函数的 arguments 属性。例如：

```javascript
function add() {
	var sum = 0;
	for (var i = 0; i < arguments.length; i++) {
		sum += arguments[i];
	}
	return sum;
}
```
这样你就实现了任意多个参数加法函数的重载了。

当然，你还可以在函数中通过 instanceof 或者 constructor 来判断每个参数的类型，来决定后面执行什么操作，实现更为复杂的函数或方法重载。总之，javascript 的重载，是在函数中由用户自己通过操作 arguments 这个属性来实现的。

##### 覆盖

实现覆盖也很容易，例如：

```javascript
function parentClass() {
	this.method = function() {
		alert("parentClass method");
	}
}
function subClass() {
	this.method = function() {
		alert("subClass method");
	}
}
subClass.prototype = new parentClass();
subClass.prototype.constructor = subClass;

var o = new subClass();
o.method();
```
这样，子类中定义的 method 就覆盖了从父类中继承来的 method 方法了。

你可能会说，这样子覆盖是不错，但 java 中，覆盖的方法里面可以调用被覆盖的方法（父类的方法），在这里怎么实现呢？也很容易，而且比 java 中还要灵活，java 中限制，你只能在覆盖被覆盖方法的方法中才能使用 super 来调用次被覆盖的方法。我们不但可以实现这点，而且还可以让子类中所有的方法中都可以调用父类中被覆盖的方法。看下面的例子：

```javascript
function parentClass() {
	this.method = function() {
		alert("parentClass method");
	}
}
function subClass() {
	var method = this.method;
	this.method = function() {
		method.call(this);
		alert("subClass method");
	}
}
subClass.prototype = new parentClass();
subClass.prototype.constructor = subClass;

var o = new subClass();
o.method();
```
你会发现，原来这么简单，只要在定义覆盖方法前，定义一个私有变量，然后把父类中定义的将要被覆盖的方法赋给它，然后我们就可以在后面继续调用它了，而且这个是这个方法是私有的，对于子类的对象是不可见的。这样跟其它高级语言实现的覆盖就一致了。

最后需要注意，我们在覆盖方法中调用这个方法时，需要用 call 方法来改变执行上下文为 this（虽然在这个例子中没有必要），如果直接调用这个方法，执行上下文就会变成全局对象了。

### node.js

Node.js是一个事件驱动I/O服务端JavaScript环境，基于Google的V8引擎。目的是为了提供撰写可扩充网络程序，如Web服务。与一般JavaScript不同的地方，Node.js并不是在Web浏览器上运行，而是一种在服务器上运行的Javascript服务端JavaScript。Node.js实作了部份CommonJS规格（Spec）。[4]Node.js包含了一个交互测试REPL环境

#### node.js debugger;

Nodejs提供了一个内建调试器来帮助开发者调试应用程序。想要开启调试器我们需要在代码中加入debugger标签，当Nodejs执行到debugger标签时会自动暂停（debugger标签相当于在代码中开启一个断点）。代码如下：
```javascript
function Person(name, age){
	this.name = name;
	this.age = age;
}
Person.prototype.walk = function(){
	console.log(this.name, 'walking');
};

function Students(name, age, id){
	Person.call(this,name,age);
	this.id = id;

}
debugger;
Students.prototype = Object.create(Person.prototype);
Students.prototype.constructor = Students;
var stu = new Students("杨明昆");
stu.walk();

```
执行后效果图如下：

```shell
yangmingkun@yangmingkun ~/Desktop $ node debug person.js
< debugger listening on port 5858
connecting... ok
break in person.js:5
3   this.age = age;
4 }
5 Person.prototype.walk = function(){
	6   console.log(this.name, 'walking');
	7 };
	debug> help
	Commands: run (r), cont (c), next (n), step (s), out (o), backtrace (bt), setBreakpoint (sb), clearBreakpoint (cb),
	watch, unwatch, watchers, repl, restart, kill, list, scripts, breakOnException, breakpoints, version
	debug>
```
输入help指令可以查看继续进行的操作。
