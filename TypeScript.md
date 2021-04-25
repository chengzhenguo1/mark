## 基础类型

1. 布尔值
2. `数字` 支持二进制，八进制，十进制，十六进制
3. 字符串
4. 数组
   两种定义方式：
   `let list: number[] = [1, 2, 3];`
   `let list: Array<number> = [1, 2, 3];`
5. `元组Tuple` 元组与数组类似，但每一项数据类型可不同（数组需要使用any类型）
6. `枚举` 为一组数值赋予友好的名字
7. `Any` 声明为 any 的变量可以赋予任意类型的值
8. `Void` 表示没有任何类型，当一个函数没有返回值时，就可以使用void
9. `null` 表示对象值缺失
10. `undefined` 用于初始化变量为一个为定义的值
11. `never`表示的是那些永不存在的值的类型

#### 类型断言

- 可以用来手动指定一个值的类型
- 类型断言好比其它语言里的类型转换，但是不进行特殊的数据检查和解构。 它没有运行时的影响，只是在编译阶段起作用。

**使用形式：**

```javascript
let someValue: any = "this is a string";
let strLength: number = (<string>someValue).length;
 
let someValue: any = "this is a string";
let strLength: number = (someValue as string).length;

```

**注意：** TypeScript里使用JSX时，只有 as语法断言是被允许的。

## 变量声明

使用`let` 声明变量
使用`const`声明常量
[详情请看我另一篇博客：js中的const,var,let](https://blog.csdn.net/weixin_40693643/article/details/101263463)

#### 解构

[JS解构和 … 运算符](https://blog.csdn.net/weixin_40693643/article/details/104657008)

属性重命名

```javascript
let { a: newName1, b: newName2 } = o;

```

等价于

```javascript
let newName1 = o.a;
let newName2 = o.b;

```

## 接口

接口是一系列抽象方法的声明，是一些方法特征的集合，这些方法都应该是抽象的，需要由具体的类去实现，然后第三方就可以通过这组抽象方法调用，让具体的类执行具体的方法。

接口定义实例：

```javascript
interface LabelledValue {
  label: string;
  color?: string;  // 可选属性，带？符号，表示不是必须的
  readonly x: number; // 只读属性，用readonly指定
}
```

#### `readonly` vs `const`

两者都表示只读，不可修改

- 变量使用`const`
- 属性使用`readonly`

**注意：**
TypeScript具有`ReadonlyArray<T>`类型，它与`Array<T>`相似。
不同的是，数组创建后不能修改。

#### 额外的属性检查

额外的属性检查只会应用于对象字面量场景
如果一个对象字面量存在任何“目标类型”不包含的属性时，你会得到一个错误。

实例代码：

```javascript
interface SquareConfig {
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
    // ...
}

let mySquare = createSquare({ colour: "red", width: 100 });
```

上述代码，向`createSquare`传入了一个对象字面量，就会对这个对象字面量进行检查，发现目标类型里不包含`colour`这个属性，就会报错。

**解决方法：**
一：使用类型断言

```javascript
let mySquare = createSquare({ colour: "red", width: 100 } as SquareConfig);
```

二：添加一个字符串索引签名（最佳）
如果 `SquareConfig`带有上面定义的类型的`color`和`width`属性，并且还会带有任意数量的其它属性，那么我们可以这样定义它：

```javascript
interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any; // 表示可以有任意数量和类型的属性
}
```

三：将对象赋值给一个变量，再传入，跳过属性检查

```javascript
let squareOptions = { colour: "red", width: 100 };
let mySquare = createSquare(squareOptions);
```

#### 接口使用自身的类型

```` typescript
interface UserParams {
	user: User[]
	param: {
		name: string
		age: string
	}
	// 使用自身param类型
	setParam: (param: UserParams['parsm']) => void
}
````

#### 函数类型

接口也可以用来描述函数类型

**函数类型接口示例代码：**

```javascript
interface SearchFunc {
  (source: string, subString: string): boolean;
}
```

**使用方式：**
创建一个函数类型的变量，就可以向这个变量赋值一个同类型的函数。
函数的参数名不需要与接口定义的名字匹配
如果没有指定类型，`typescript`类型系统会推断出参数类型，返回值类型则通过返回值推断出。

```javascript
let mySearch: SearchFunc;
mySearch = function(src: string, sub: string) {
  let result = source.search(subString);
  return result > -1;
}
```

#### 可索引的类型

- `TypeScript`支持两种索引类型：字符串和数字。
- 可以同时使用两种类型的索引，但是数字索引的返回值必须是字符串索引返回值类型的子类型。

索引也可以设置为只读：

```javascript
interface ReadonlyStringArray {
    readonly [index: number]: string;
}
let myArray: ReadonlyStringArray = ["Alice", "Bob"];
myArray[2] = "Mallory"; // error!
```

#### 类类型

代码示例：该接口描述了类的公共部分，它不会帮你检查类是否具有某些私有成员。

```javascript
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date);
}

class Clock implements ClockInterface {
    currentTime: Date;
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}
```

**类静态部分与实例部分的区别**
当一个类实现了一个接口时，只对其实例部分进行类型检查。 constructor存在于类的静态部分，所以不在检查的范围内。

#### 继承接口

- 接口可以相互继承
- 一个接口可以继承多个接口，创建出多个接口的合成接口

#### 混合类型

一个对象可以同时具备多种类型

#### 接口继承类

当接口继承了一个类类型时，它会继承类的成员但不包括其实现



## 类

类的概念同java
**注意：** 派生类包含了一个构造函数，它就必须调用 super()，它会执行基类的构造函数

#### 公共，私有与受保护的修饰符

同java

- `public` (默认)
- `private` 表示不能在声明它的类的外部访问
- `protected`
  protected修饰符与 private修饰符的行为很相似，但有一点不同， protected成员在派生类中仍然可以访问

**注意：** 构造函数也可以被标记成 protected。 这意味着这个类不能在包含它的类外被实例化，但是能被继承

**`readonly`修饰符**
可以使用 `readonly`关键字将属性设置为只读的。 只读属性必须在声明时或构造函数里被初始化。

```javascript
class Octopus {
    readonly name: string;
    readonly numberOfLegs: number = 8;
    constructor (theName: string) {
        this.name = theName;
    }
}
let dad = new Octopus("Man with the 8 strong legs");
dad.name = "Man with the 3-piece suit"; // 错误! name 是只读的.
123456789
```

**参数属性：**
代码功能同上，更加简洁

```javascript
class Octopus {
    readonly numberOfLegs: number = 8;
    constructor(readonly name: string) {
    }
}
12345
```

参数属性通过给构造函数参数前面添加一个访问限定符来声明。 使用 private限定一个参数属性会声明并初始化一个私有成员；对于 public和 protected来说也是一样。

#### 存取器

通过`getters/setters`来截取对对象成员的访问
示例代码：

```javascript
let passcode = "secret passcode";
class Employee {
    private _fullName: string;
    get fullName(): string {
        return this._fullName;
    }
    set fullName(newName: string) {
        if (passcode && passcode == "secret passcode") {
            this._fullName = newName;
        }
        else {
            console.log("Error: Unauthorized update of employee!");
        }
    }
}

let employee = new Employee();
employee.fullName = "Bob Smith";
if (employee.fullName) {
    alert(employee.fullName);
}
```

**注意：** 只带有`get`不带有 `set`的存取器自动被推断为 `readonly`

#### 静态属性`static`

存在于类的本身上，而不是实例上

#### 抽象类`abstract`

抽象类做为其它派生类的基类使用。 它们一般不会直接被实例化。 不同于接口，抽象类可以包含成员的实现细节。 abstract关键字是用于定义抽象类和在抽象类内部定义抽象方法。

#### 类型推论

如果没有明确指定类型，typescript会根据类型推论的规则推断出一个类型。
**注意：**
如果定义的时候没有赋值，不管之后有没有赋值，都会被推断为`any`类型而完全不被类型检查

#### 联合类型

联合类型表示取值可以为多种类型中的一种。
使用`|` 来分隔类型
示例代码：

```javascript
let myFavoriteNumber: string | number;
1
```

**注意：**
当不确定一个联合类型的变量到底是哪个类型的时候，只能访问此联合类型的所有类型共有的属性和方法。

#### 接口

- 赋值的时候，变量的形状必须和接口的形状保持一致
- 可选属性使用`?`
- 任意属性：使用 `[propName: string]`定义了任意属性取 string 类型的值。

```javascript
interface Person {
    name: string;
    age?: number;
    [propName: string]: any;
}
12345
```

**注意：**

1. 定义来任意属性后，确定属性和可选属性的类型必须是任意属性类型的子集
2. 如果同时存在任意属性、可选属性，那么任意属性的数据类型要带undefined

- 只读属性`readonly`
  **注意:** 只读的约束存在于第一次给对象赋值的时候，而不是第一次给只读属性赋值的时候：

#### 数组的类型

- `类型[]`表示法

```javascript
let fibonacci: number[] = [1, 1, 2, 3, 5];
1
```

- 可以使用数组泛型来表示数组

```javascript
let fibonacci: Array<number> = [1, 1, 2, 3, 5];
1
```

- 用接口表示数组(过于复杂，一般不用)

```javascript
interface NumberArray {
    [index: number]: number;
}
let fibonacci: NumberArray = [1, 1, 2, 3, 5];
1234
```

`NumberArray`表示：只要索引的类型是数字时，那么值的类型必须是数字

可以用接口来描述类数组：

```javascript
function sum() {
    let args: {
        [index: number]: number;
        length: number;
        callee: Function;
    } = arguments;
}
1234567
```

**数组里`any`的使用**
表示数组中允许出现任意类型

```javascript
let list: any[] = ['xcatliu', 25, { website: 'http://xcatliu.com' }];
1
```

#### 函数的类型

两种常见的定义函数的方式：

- 函数声明

```javascript
function sum(x: number, y: number): number {
    return x + y;
}
123
```

- 函数表达式

```javascript
let mySum: (x: number, y: number) => number = function (x: number, y: number): number {
    return x + y;
};
123
```

**注意：**
在 `TypeScript`的类型定义中，`=>`用来表示函数的定义，左边是输入类型，需要用括号括起来，右边是输出类型。
在 ES6 中，`=>`叫做箭头函数.

用接口定义函数的形状

```javascript
interface SearchFunc {
    (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
    return source.search(subString) !== -1;
}
12345678
```

关于函数的参数：

- 可选参数后面不允许再出现必需参数
- 添加了默认值的参数会被识别为可选参数，不受前面☝️第一条限制

示例代码：

```javascript
function buildName(firstName: string = 'Tom', lastName: string, nickName?: string) {
    return firstName + ' ' + lastName;
}
let tomcat = buildName('Tom', 'Cat');
let cat = buildName(undefined, 'Cat', 'mimi');
12345
```

**重载：**

```javascript
function reverse(x: number): number;
function reverse(x: string): string;
function reverse(x: number | string): number | string {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}
123456789
```

**注意：**
`TypeScript`会优先从最前面的函数定义开始匹配，所以多个函数定义如果有包含关系，需要优先把精确的定义写在前面。

#### 类型断言

类型断言可以用来手动指定一个值
使用语法：
`值 as 类型` （建议使用as语法，不容易混淆）或 `<类型>值`

**注意：** 类型断言只能够「欺骗」`TypeScript`编译器，无法避免运行时的错误。小心使用！

**使用场景：**
判断传入的参数是不是`ApiError`类型

```javascript
interface ApiError extends Error {
    code: number;
}
interface HttpError extends Error {
    statusCode: number;
}

function isApiError(error: Error) {
    if (typeof (error as ApiError).code === 'number') {
        return true;
    }
    return false;
}
12345678910111213
```

如上，因为接口是一个类型，不是一个真正的值，它在编译结果中会被删除，就无法使用 `instanceof`来做运行时判断，此时就只能使用类型断言来判断。

**将类型断言为`any`**

```javascript
window.foo = 1;
1
```

上述代码，在TS编译时会报错，显示window上不存在foo属性
将window断言为any类型，则可以运行：

```javascript
(window as any).foo = 1;
1
```

在`any`类型的变量上，访问任何属性都是允许的。

**注意：** 将类型断言为`any`，是我们解决问题的最后一个手段。（在类型的严格性和开发的便利性之间掌握平衡）

类型断言总结：

1. 联合类型可以被断言为其中一个类型
2. 父类可以被断言为子类
3. 任何类型都可以被断言为any
4. any可以被断言为任何类型

由第3，4点，产生了双重断言，形式如`as any as Foo`, 了解了解，不要使用

**注意：**

- 类型断言只会影响TS编译时的类型，类型断言语句在编译结果中会被删除。它不会影响到变量的类型，若要进行类型转换，直接使用类型转换方法。
- 类型声明比类型断言更加严格（优先使用类型声明）

```javascript
const tom = getCacheData('tom') as Cat;  // 类型断言
const tom: Cat = getCacheData('tom');    // 类型声明
12
```

- 可以使用泛型来更加规范的实现对类型的约束

#### 类型保护

类型保护允许你使用更小范围下的对象类型。

##### [](https://jkchao.github.io/typescript-book-chinese/typings/typeGuard.html#typeof)typeof

TypeScript 熟知 JavaScript 中 `instanceof` 和 `typeof` 运算符的用法。如果你在一个条件块中使用这些，TypeScript 将会推导出在条件块中的的变量类型。如下例所示，TypeScript 将会辨别 `string` 上是否存在特定的函数，以及是否发生了拼写错误：

```ts
function doSome(x: number | string) {
  if (typeof x === 'string') {
    // 在这个块中，TypeScript 知道 `x` 的类型必须是 `string`
    console.log(x.subtr(1)); // Error: 'subtr' 方法并没有存在于 `string` 上
    console.log(x.substr(1)); // ok
  }

  x.substr(1); // Error: 无法保证 `x` 是 `string` 类型
}
```

##### [#](https://jkchao.github.io/typescript-book-chinese/typings/typeGuard.html#instanceof)instanceof

这有一个关于 `class` 和 `instanceof` 的例子：

```ts
class Foo {
  foo = 123;
  common = '123';
}

class Bar {
  bar = 123;
  common = '123';
}

function doStuff(arg: Foo | Bar) {
  if (arg instanceof Foo) {
    console.log(arg.foo); // ok
    console.log(arg.bar); // Error
  }
  if (arg instanceof Bar) {
    console.log(arg.foo); // Error
    console.log(arg.bar); // ok
  }
}

doStuff(new Foo());
doStuff(new Bar());
```

TypeScript 甚至能够理解 `else`。当你使用 `if` 来缩小类型时，TypeScript 知道在其他块中的类型并不是 `if` 中的类型：

```ts
class Foo {
  foo = 123;
}

class Bar {
  bar = 123;
}

function doStuff(arg: Foo | Bar) {
  if (arg instanceof Foo) {
    console.log(arg.foo); // ok
    console.log(arg.bar); // Error
  } else {
    // 这个块中，一定是 'Bar'
    console.log(arg.foo); // Error
    console.log(arg.bar); // ok
  }
}

doStuff(new Foo());
doStuff(new Bar());
```

##### [#](https://jkchao.github.io/typescript-book-chinese/typings/typeGuard.html#in)in

`in` 操作符可以安全的检查一个对象上是否存在一个属性，它通常也被作为类型保护使用：

```ts
interface A {
  x: number;
}

interface B {
  y: string;
}

function doStuff(q: A | B) {
  if ('x' in q) {
    // q: A
  } else {
    // q: B
  }
}
```

##### [#](https://jkchao.github.io/typescript-book-chinese/typings/typeGuard.html#字面量类型保护)字面量类型保护

当你在联合类型里使用字面量类型时，你可以检查它们是否有区别：

```ts
type Foo = {
  kind: 'foo'; // 字面量类型
  foo: number;
};

type Bar = {
  kind: 'bar'; // 字面量类型
  bar: number;
};

function doStuff(arg: Foo | Bar) {
  if (arg.kind === 'foo') {
    console.log(arg.foo); // ok
    console.log(arg.bar); // Error
  } else {
    // 一定是 Bar
    console.log(arg.foo); // Error
    console.log(arg.bar); // ok
  }
}
```

##### [#](https://jkchao.github.io/typescript-book-chinese/typings/typeGuard.html#使用定义的类型保护)使用定义的类型保护

JavaScript 并没有内置非常丰富的、运行时的自我检查机制。当你在使用普通的 JavaScript 对象时（使用结构类型，更有益处），你甚至无法访问 `instanceof` 和 `typeof`。在这种情景下，你可以创建*用户自定义的类型保护函数*，这仅仅是一个返回值为类似于`someArgumentName is SomeType` 的函数，如下：

```ts
// 仅仅是一个 interface
interface Foo {
  foo: number;
  common: string;
}

interface Bar {
  bar: number;
  common: string;
}

// 用户自己定义的类型保护！
function isFoo(arg: Foo | Bar): arg is Foo {
  return (arg as Foo).foo !== undefined;
}

// 用户自己定义的类型保护使用用例：
function doStuff(arg: Foo | Bar) {
  if (isFoo(arg)) {
    console.log(arg.foo); // ok
    console.log(arg.bar); // Error
  } else {
    console.log(arg.foo); // Error
    console.log(arg.bar); // ok
  }
}

doStuff({ foo: 123, common: '123' });
doStuff({ bar: 123, common: '123' });
```

#### 声明文件

- `declare var`声明全局变量
- `declare function` 声明全局方法
- `declare class` 声明全局类
- `declare enum` 声明全局枚举类型
- `declare namespace` 声明（含有子属性的）全局对象
- `interface` 和`type` 声明全局类型
- `export` 导出变量
- `export namespace` 导出（含有子属性的）对象
- `export default` ES6 默认导出
- `export =`commonjs 导出模块
- `export as namespace` UMD 库声明全局变量
- `declare global` 扩展全局变量
- `declare module` 扩展模块
- `/// <reference />` 三斜线指令

#### 内置对象

- ECMAScript标准提供的内置对象有：`Boolean` `Error` `Date` `RegExp`等
- DOM和BOM提供的内置对象有：`Document`, `HTMLElement` `Event` `NodeList`等
- [TypeScript 核心库的定义文件](https://github.com/Microsoft/TypeScript/tree/master/src/lib)中定义了所有浏览器环境需要用到的类型，并且是预置在 TypeScript 中的。

**注意：** TypeScript 核心库的定义中不包含 Node.js 部分。想要使用，需要引入第三方声明文件

```
npm install @types/node --save-dev
1
```

#### 捕获键的名称 keyof

`keyof` 操作符能让你捕获一个类型的键。例如，你可以使用它来捕获变量的键名称，在通过使用 `typeof` 来获取类型之后：

```ts
const colors = {
  red: 'red',
  blue: 'blue'
};

type Colors = keyof typeof colors;

let color: Colors; // color 的类型是 'red' | 'blue'
color = 'red'; // ok
color = 'blue'; // ok
color = 'anythingElse'; // Error
```

这允许你很容易地拥有像字符串枚举+常量这样的类型，如上例所示。

#### ThisType （this）

通过 `ThisType` 我们可以在对象字面量中键入 `this`，并提供通过上下文类型控制 `this` 类型的便捷方式。它只有在 `--noImplicitThis` 的选项下才有效。

现在，在对象字面量方法中的 `this` 类型，将由以下决定：

- 如果这个方法显式指定了 `this` 参数，那么 `this` 具有该参数的类型。（下例子中 `bar`）
- 否则，如果方法由带 `this` 参数的签名进行上下文键入，那么 `this` 具有该参数的类型。（下例子中 `foo`）
- 否则，如果 `--noImplicitThis` 选项已经启用，并且对象字面量中包含由 `ThisType<T>` 键入的上下文类型，那么 `this` 的类型为 `T`。
- 否则，如果 `--noImplicitThis` 选项已经启用，并且对象字面量中不包含由 `ThisType<T>` 键入的上下文类型，那么 `this` 的类型为该上下文类型。
- 否则，如果 `--noImplicitThis` 选项已经启用，`this` 具有该对象字面量的类型。
- 否则，`this` 的类型为 `any`。

一些例子：

```ts
// Compile with --noImplicitThis

type Point = {
  x: number;
  y: number;
  moveBy(dx: number, dy: number): void;
};

let p: Point = {
  x: 10,
  y: 20,
  moveBy(dx, dy) {
    this.x += dx; // this has type Point
    this.y += dy; // this has type Point
  }
};

let foo = {
  x: 'hello',
  f(n: number) {
    this; // { x: string, f(n: number): void }
  }
};

let bar = {
  x: 'hello',
  f(this: { message: string }) {
    this; // { message: string }
  }
};
```

类似的方式，当使用 `--noImplicitThis` 时，函数表达式赋值给 `obj.xxx` 或者 `obj[xxx]` 的目标时，在函数中 `this` 的类型将会是 `obj`：

```ts
// Compile with --noImplicitThis

obj.f = function(n) {
  return this.x - n; // 'this' has same type as 'obj'
};

obj['f'] = function(n) {
  return this.x - n; // 'this' has same type as 'obj'
};
```

通过 API 转换参数的形式来生成 `this` 的值的情景下，可以通过创建一个新的 `ThisType<T>` 标记接口，可用于在上下文中表明转换后的类型。尤其是当字面量中的上下文类型为 `ThisType<T>` 或者是包含 `ThisType<T>` 的交集时，显得尤为有效，对象字面量方法中 `this` 的类型即为 `T`。

```ts
// Compile with --noImplicitThis

type ObjectDescriptor<D, M> = {
  data?: D;
  methods?: M & ThisType<D & M>; // Type of 'this' in methods is D & M
};

function makeObject<D, M>(desc: ObjectDescriptor<D, M>): D & M {
  let data: object = desc.data || {};
  let methods: object = desc.methods || {};
  return { ...data, ...methods } as D & M;
}

let obj = makeObject({
  data: { x: 0, y: 0 },
  methods: {
    moveBy(dx: number, dy: number) {
      this.x += dx; // Strongly typed this
      this.y += dy; // Strongly typed this
    }
  }
});

obj.x = 10;
obj.y = 20;
obj.moveBy(5, 5);
```

在上面的例子中，`makeObject` 参数中的对象属性 `methods` 具有包含 `ThisType<D & M>` 的上下文类型，因此对象中 `methods` 属性下的方法的 `this` 类型为 `{ x: number, y: number } & { moveBy(dx: number, dy: number): number }`。

`ThisType<T>` 的接口，在 `lib.d.ts` 只是被声明为空的接口，除了可以在对象字面量上下文中可以被识别以外，该接口的作用等同于任意空接口。

#### 类型别名

类型别名用来给一个类型起个新名字，常用于联合类型。

```javascript
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;
function getName(n: NameOrResolver): Name {
    if (typeof n === 'string') {
        return n;
    } else {
        return n();
    }
}
12345678910
```

#### 字符串字面量类型

字符串字面量类型用来约束取值只能是某几个字符串中的一个

```javascript
type EventNames = 'click' | 'scroll' | 'mousemove';
function handleEvent(ele: Element, event: EventNames) {
    // do something
}

handleEvent(document.getElementById('hello'), 'scroll');  // 没问题
handleEvent(document.getElementById('world'), 'dblclick'); // 报错，event 不能为 'dblclick'

// index.ts(7,47): error TS2345: Argument of type '"dblclick"' is not assignable to parameter of type 'EventNames'.
123456789
```

**注意：** 类型别名与字符串字面量类型都是使用 type 进行定义。

## 高级类型

lib 库中的五个高级类型
**以下所有例子皆以 person 为例**

```typescript
interface Person {
    name: string;
    age?: number;
}
```

### Partial(可选)

源码:

```typescript
type Partial<T> = {
    [P in keyof T]?: T[P];
};
```

实例:

```typescript
type person2 = Partial<Person>;
// 将参数都变为可选
// person2 === {name?: string; age?: number}
```

### Required(必选)

源码:

```typescript
type Required<T> = {
    [P in keyof T]-?: T[P];
};
```

实例:

```typescript
type person3 = Required<Person>;
// 将参数都变为必选
// person3 === {name: string; age: number}
```

### Readonly（只读）

源码:

```typescript
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};
```

实例:

```typescript
type person4 = Readonly<Person>;
// 将参数变为只读
// person4 === {
//        readonly name: string;
//        readonly age?: number;
//  }
```

### Pick（选择）

源码:

```typescript
type Pick<T, K extends keyof T> = {
    [P in K]: T[P];
};
```

实例:

```typescript
type person5 = Pick<Person, "name">;
// 只要name属性
// person5 === {name: string}
```

### Record

源码:

```typescript
type Record<K extends keyof any, T> = {
    [P in K]: T;
};
```

实例:

```typescript
type person6 = Record<'name' | 'age', string>
// 以 typeof 格式快速创建一个类型，此类型包含一组指定的属性且都是必填。
// person6 === {name: string; age: string}
```

## 如果对象实现了某个接口，我怎么在运行时检查？

> 我写下了像下面的一段代码

```ts
interface SomeInterface {
  name: string;
  length: number;
}
interface SomeOtherInterface {
  questions: string[];
}

function f(x: SomeInterface | SomeOtherInterface) {
  // Can't use instanceof on interface, help?
  if (x instanceof SomeInterface) {
    // ...
  }
}
```

在编译时期， TypeScript 的类型被删除。这意味着没有用于执行运行时类型检查的内置机制。这完全取决与你如何鉴别对象。一个比较广泛的用法是检查某个对象里的属性。你可以使用用户定义的类型保护来实现它：

```ts
function isSomeInterface(x: any): x is SomeInterface {
  return typeof x.name === 'string' && typeof x.length === 'number';

function f(x: SomeInterface|SomeOtherInterface) {
  if (isSomeInterface(x)) {
    console.log(x.name); // Cool!
  }
}
```

## 仅仅导入/导出声明 export TYPE

为了能让我们导入类型，TypeScript 重用了 JavaScript 导入语法。例如在下面的这个例子中，我们确保 JavaScript 的值 `doThing` 以及 TypeScript 类型 `Options` 一同被导入

```ts
// ./foo.ts
interface Options {
  // ...
}

export function doThing(options: Options) {
  // ...
}

// ./bar.ts
import { doThing, Options } from './foo.js';

function doThingBetter(options: Options) {
  // do something twice as good
  doThing(options);
  doThing(options);
}
```

这很方便的，因为在大多数的情况下，我们不必担心导入了什么 —— 仅仅是我们想导入的内容。

遗憾的是，这仅是因为一个被称之为「导入省略」的功能而起作用。当 TypeScript 输出一个 JavaScript 文件时，TypeScript 会识别出 `Options` 仅仅是当作了一个类型来使用，它将会删除 `Options`

```ts
// ./foo.js
export function doThing(options: Options) {
  // ...
}

// ./bar.js
import { doThing } from './foo.js';

function doThingBetter(options: Options) {
  // do something twice as good
  doThing(options);
  doThing(options);
}
```

在通常情况下，这种行为都是比较好的。但是它会导致一些其他问题。

首先，在一些场景下，TypeScript 会混淆导出的究竟是一个类型还是一个值。比如在下面的例子中， `MyThing` 究竟是一个值还是一个类型？

```ts
import { MyThing } from './some-module.js';

export { MyThing };
```

如果单从这个文件来看，我们无从得知答案。如果 `Mything` 仅仅是一个类型，Babel 和 TypeScript 使用的 `transpileModule` API 编译出的代码将无法正确工作，并且 TypeScript 的 `isolatedModules` 编译选项将会提示我们，这种写法将会抛出错误。问题的关键在于，没有一种方式能识别它仅仅是个类型，以及是否应该删除它，因此「导入省略」并不够好。

同时，这也存在另外一个问题，TypeScript 导入省略将会去除只包含用于类型声明的导入语句。对于含有副作用的模块，这造成了明显的不同行为。于是，使用者将会不得不添加一条额外的声明语句，来确保有副作用。

```ts
// This statement will get erased because of import elision.
import { SomeTypeFoo, SomeOtherTypeBar } from './module-with-side-effects';

// This statement always sticks around.
import './module-with-side-effects';
```

一个我们看到的具体例子是出现在 Angularjs（1.x）中， `services` 需要在全局在注册（它是一个副作用），但是导入的 `services` 仅仅用于类型声明中。

```ts
// ./service.ts
export class Service {
  // ...
}
register('globalServiceId', Service);

// ./consumer.ts
import { Service } from './service.js';

inject('globalServiceId', function(service: Service) {
  // do stuff with Service
});
```

结果 `./service.js` 中的代码不会被执行，导致在运行时会被中断。

为了避免这类行为，我们意识到在什么该被导入/删除方面，需要给使用者提供更细粒度的控制。

在 TypeScript 3.8 版本中，我们添加了一个仅仅导入/导出声明语法来作为解决方式。

```ts
import type { SomeThing } from "./some-module.js";

export type { SomeThing };
```

`import type` 仅仅导入被用于类型注解或声明的声明语句，它总是会被完全删除，因此在运行时将不会留下任何代码。与此相似，`export type` 仅仅提供一个用于类型的导出，在 TypeScript 输出文件中，它也将会被删除。

值得注意的是，类在运行时具有值，在设计时具有类型。它的使用与上下文有关。当使用 `import type` 导入一个类时，你不能做类似于从它继承的操作。

```ts
import type { Component } from "react";

interface ButtonProps {
    // ...
}

class Button extends Component<ButtonProps> {
    //               ~~~~~~~~~
    // error! 'Component' only refers to a type, but is being used as a value here.

    // ...
}
```

如果在之前你使用过 Flow，它们的语法是相似的。一个不同的地方是我们添加了一个新的限制条件，来避免可能混淆的代码。

```ts
// Is only 'Foo' a type? Or every declaration in the import?
// We just give an error because it's not clear.

import type Foo, { Bar, Baz } from "some-module";
//     ~~~~~~~~~~~~~~~~~~~~~~
// error! A type-only import can specify a default import or named bindings, but not both.
```

与 `import type` 相关联，我们提供来一个新的编译选项：`importsNotUsedAsValues`，通过它可以来控制没被使用的导入语句将会被如何处理，它的名字是暂定的，但是它提供来三个不同的选项。

- `remove`，这是现在的行为 —— 丢弃这些导入语句。这仍然是默认行为，没有破坏性的更改
- `preserve`，它将会保留所有的语句，即使是从来没有被使用。它可以保留副作用
- `error`，它将会保留所有的导入（与 `preserve` 选项相同）语句，但是当一个值的导入仅仅用于类型时将会抛出错误。如果你想确保没有意外导入任何值，这会是有用的，但是对于副作用，你仍然需要添加额外的导入语法。

## react中使用ts

### props

```tsx
interface AppProps = {
  message: string;
};

// 使用React.FC会带一个chidren字段
const App: React.FC<AppProps> = ({ message,children }) => <div>{message}</div>;
```

### Hooks

#### useState

```tsx
// 使用初始值会默认判断该参数的类型
const [val, toggle] = React.useState(false);

// 然而，许多state都是用空的默认值初始化的，您可能想知道如何提供类型。显式声明类型，并使用联合类型
const [user, setUser] = React.useState<IUser | null>(null);

```

#### useReducer

```tsx
// 初始化数据
const initialState = { count: 0 };
// 需要声明payload的数据类型
type ACTIONTYPE =
  | { type: "increment"; payload: number }
  | { type: "decrement"; payload: string };

function reducer(state: typeof initialState, action: ACTIONTYPE) {
  switch (action.type) {
    case "increment":
      return { count: state.count + action.payload };
    case "decrement":
      return { count: state.count - Number(action.payload) };
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = React.useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({ type: "decrement", payload: "5" })}>
        -
      </button>tsx
      <button onClick={() => dispatch({ type: "increment", payload: 5 })}>
        +
      </button>
    </>
  );
}
```

##### **Reducer**在redux中使用

```` tsx
import { Reducer } from 'redux';

export function reducer: Reducer<AppState, Action>() {}
````

#### useEffect

当使用useEffect时，注意不要返回函数或未定义以外的任何东西，否则TypeScript和React都会对你报错。当使用箭头函数时，这可能会很微妙:

```tsx
function DelayedEffect(props: { timerMs: number }) {
  const { timerMs } = props;

  useEffect(
    () =>
      setTimeout(() => {
        /* do stuff */
      }, timerMs),
    [timerMs]
  );
  // 坏例子!setTimeout隐式返回一个数字
  // 因为arrow函数体没有被花括号括起来
  return null;
}


function DelayedEffect(props: { timerMs: number }) {
  const { timerMs } = props;

  useEffect(() => {
    setTimeout(() => {
      /* do stuff */
    }, timerMs);
  }, [timerMs]);
  // 更好;使用void关键字确保返回undefined
  return null;
}

```

#### UseRef

当使用useRef创建没有初始值的ref容器时，有两个选项:

```tsx
const ref1 = useRef<HTMLElement>(null!);
const ref2 = useRef<HTMLElement>(null);
const ref3 = useRef<HTMLElement | null>(null);
```

```tsx
function TextInputWithFocusButton() {
  // 用null初始化，但是告诉TypeScript我们正在寻找一个HTMLInputElement
  const inputEl = React.useRef<HTMLInputElement>(null);
  const onButtonClick = () => {
    // 严格的空值检查需要我们检查输入值和当前值是否存在.
    // 一旦current存在，它的类型是HTMLInputElement
    // 可以使用focus方法! ✅
    if (inputEl && inputEl.current) {
      inputEl.current.focus();
    }
  };
  return (
    <>
      {/* 此外，inputEl只能用于输入元素. Yay! */}
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```

#### useImperativeHandle

```tsx
type ListProps<ItemType> = {
  items: ItemType[];
  innerRef?: React.Ref<{ scrollToItem(item: ItemType): void }>;
};

function List<ItemType>(props: ListProps<ItemType>) {
  useImperativeHandle(props.innerRef, () => ({
    scrollToItem() {},
  }));
  return null;
}
```

#### 自定义Hooks

如果你在自定义钩子中返回一个数组，你需要避免类型推断，因为TypeScript会推断出一个联合类型(当你在数组的每个位置都需要不同类型时)。相反，使用TS 3.4 const断言:

```tsx
export function useLoading() {
  const [isLoading, setState] = React.useState(false);
  const load = (aPromise: Promise<any>) => {
    setState(true);
    return aPromise.finally(() => setState(false));
  };
  return [isLoading, load] as const; // 推断出 [boolean, typeof load] 而不是 (boolean | typeof load)[]
}
```

这样，当你进行解构时，你实际上会根据解构位置得到正确的类型。



如果你在使用const断言时遇到麻烦，你也可以断言或定义函数的返回类型:

```tsx
export function useLoading() {
  const [isLoading, setState] = React.useState(false);
  const load = (aPromise: Promise<any>) => {
    setState(true);
    return aPromise.finally(() => setState(false));
  };
  return [isLoading, load] as [
    boolean,
    (aPromise: Promise<any>) => Promise<any>
  ];
}
```

如果你写了很多自定义钩子，那么一个自动为元组类型的辅助函数也会很有帮助:

```tsx
function tuplify<T extends any[]>(...elements: T) {
  return elements;
}

function useArray() {
  const numberValue = useRef(3).current;
  const functionValue = useRef(() => {}).current;
  return [numberValue, functionValue]; // type is (number | (() => void))[]
}

function useTuple() {
  const numberValue = useRef(3).current;
  const functionValue = useRef(() => {}).current;
  return tuplify(numberValue, functionValue); // type is [number, () => void]
}
```

但是，请注意，React团队建议，返回两个以上值的自定义钩子应该使用适当的对象，而不是元组。

### 类组件

在typescript中，`React.Component`是一种通用类型，因此您希望为它提供（可选）prop和state参数

```tsx

type MyProps = {
  // 用 `interface` 也是可以的
  message: string;
};

type MyState = {
  count: number; // 像这样
};

class App extends React.Component<MyProps, MyState> {
  state: MyState = {
    // 可选的第二个注释，用于更好的类型推断
    count: 0,
  };
  render() {
    return (
      <div>
        {this.props.message} {this.state.count}
      </div>
    );
  }
}
```

不要忘记您可以导出/导入/扩展这些类型/接口以便重用。

#### **类方法:**

**像普通方法那样做，但是记住函数的任何参数也需要typed:**

```tsx
class App extends React.Component<{ message: string }, { count: number }> {
  state = { count: 0 };
  render() {
    return (
      <div onClick={() => this.increment(1)}>
        {this.props.message} {this.state.count}
      </div>
    );
  }
  increment = (amt: number) => {
    // 像这样
    this.setState((state) => ({
      count: state.count + amt,
    }));
  };
}
```

#### **类属性:**

**如果你需要声明类属性供以后使用，只需要像state一样声明它，但不需要赋值:**

```tsx
class App extends React.Component<{
  message: string;
}> {
  pointer: number; // like this
  componentDidMount() {
    this.pointer = 3;
  }
  render() {
    return (
      <div>
        {this.props.message} and {this.pointer}
      </div>
    );
  }
}
```

#### **您可能不需要defaultProps**（默认值）

函数组件:

```tsx
type GreetProps = { age?: number };

const Greet = ({ age = 21 }: GreetProps) => // etc
```

类组件:

```tsx
type GreetProps = {
  age?: number;
};

class Greet extends React.Component<GreetProps> {
  render() {
    const { age = 21 } = this.props;
  }
}

let el = <Greet age={3} />;
```

#### Typing `defaultProps`

```tsx
type GreetProps = { age: number } & typeof defaultProps;
const defaultProps = {
  age: 21,
};

const Greet = (props: GreetProps) => {
  // etc
};
Greet.defaultProps = defaultProps;

//对于类组件，有两种方法可以做到这一点(包括使用Pick实用工具类型)，但建议“反转”props的定义:
type GreetProps = typeof Greet.defaultProps & {
  age: number;
};

class Greet extends React.Component<GreetProps> {
  static defaultProps = {
    age: 21,
  };
  /*...*/
}

let el = <Greet age={3} />;
```

### 基本prop类型示例

你可能会在React+TypeScript应用中使用的TypeScript类型列表:

```typescript
type AppProps = {
  message: string;
  count: number;
  disabled: boolean;
  /** 类型数组! */
  names: string[];
  /** 字符串字面值指定精确的字符串值，并使用union类型将它们连接在一起 */
  status: "waiting" | "success";
  /** 任何对象，只要你不使用它的属性(不常见但作为占位符很有用)) */
  obj: object;
  obj2: {}; // 和object几乎一样
  /** 具有任意数量属性的对象(首选) */
  obj3: {
    id: string;
    title: string;
  };
  /** 数组的对象!(普通) */
  objArr: {
    id: string;
    title: string;
  }[];
  /** 具有相同类型的任意数量属性的dict对象 */
  dict1: {
    [key: string]: MyTypeHere;
  };
  dict2: Record<string, MyTypeHere>; // 相当于dict1
  /** 任何函数，只要你不调用它(不推荐)) */
  onSomething: Function;
  /** 不接受或不返回任何东西的函数(非常常见) */
  onClick: () => void;
  /** 带有命名的函数(非常常见) */
  onChange: (id: number) => void;
  /** 接受事件的替代函数类型语法(非常常见)*/
  onClick(event: React.MouseEvent<HTMLButtonElement>): void;
  /** 可选的(非常常见!) */
  optional?: OptionalType;
};
```

### React 相关类型

```` typescript
export declare interface AppProps {
  children1: JSX.Element; // ❌ 不推荐 没有考虑数组
  children2: JSX.Element | JSX.Element[]; // ❌ 不推荐 没有考虑字符串 children
  children4: React.ReactChild[]; // 稍微好点 但是没考虑 null
  children: React.ReactNode; // ✅ 包含所有 children 情况
  functionChildren: (name: string) => React.ReactNode; // ✅ 返回 React 节点的函数
  style?: React.CSSProperties; // ✅ 推荐 在内联 style 时使用
  // ✅ 推荐原生 button 标签自带的所有 props 类型
  // 也可以在泛型的位置传入组件 提取组件的 Props 类型
  props: React.ComponentProps<"button">;
  // ✅ 推荐 利用上一步的做法 再进一步的提取出原生的 onClick 函数类型 
  // 此时函数的第一个参数会自动推断为 React 的点击事件类型
  onClickButton：React.ComponentProps<"button">["onClick"]
}

````

### Forms and Events

如果性能不是问题(通常不是!)，内联是最简单的，因为你可以使用类型推断和上下文类型:

```tsx
const el = (
  <button
    onClick={(event) => {
      /* event will be correctly typed automatically! */
    }}
  />
);
```

但是，如果需要单独定义事件处理程序，IDE工具在这里真的很有用，因为@type定义附带了丰富的类型。键入你想要的，通常自动完成会帮助你。下面是一个表单事件的onChange:

```tsx
type State = {
  text: string;
};
class App extends React.Component<Props, State> {
  state = {
    text: "",
  };

  // typing on RIGHT hand side of =
  onChange = (e: React.FormEvent<HTMLInputElement>): void => {
    this.setState({ text: e.currentTarget.value });
  };
  render() {
    return (
      <div>
        <input type="text" value={this.state.text} onChange={this.onChange} />
      </div>
    );
  }
}

```

```tsx
<form
  ref={formRef}
  onSubmit={(e: React.SyntheticEvent) => {
    e.preventDefault();
    const target = e.target as typeof e.target & {
      email: { value: string };
      password: { value: string };
    };
    const email = target.email.value; // typechecks!
    const password = target.password.value; // typechecks!
    // etc...
  }}
>
  <div>
    <label>
      Email:
      <input type="email" name="email" />
    </label>
  </div>
  <div>
    <label>
      Password:
      <input type="password" name="password" />
    </label>
  </div>
  <div>
    <input type="submit" value="Log in" />
  </div>
</form>
```

## Tip

### 如何读取一个接口的属性的类型

```` typescript
# 我们要取Person的person属性
interface Person {
  person: {
    name: string
    age: number
  }
}
# 使用Person['person'] 读取即可
interface Student {
  person: Person['person']
}
````



Union类型很容易解决这些类型问题:

```tsx
class App extends React.Component<
  {},
  {
    count: number | null; // like this
  }
> {
  state = {
    count: null,
  };
  render() {
    return <div onClick={() => this.increment(1)}>{this.state.count}</div>;
  }
  increment = (amt: number) => {
    this.setState((state) => ({
      count: (state.count || 0) + amt,
    }));
  };
}
```

### admin或者user共存如何声明

```` typescript
('admin' | 'user')[]  
结果可为['admin'] ['user'] ['admin','user']
````



### 类型保护:（属于User|Person）

有时Union 类型解决了一个区域的问题，但却创建了另一个下游区域。如果A和B都是对象类型，那么| B就不是“要么A要么B”，而是“A或B，或者同时两者”，如果你认为它是前者，这就会引起一些混淆。学习如何编写检查、保护和断言(也请参阅下面的条件呈现一节)。例如:

```tsx
interface Admin {
  role: string;
}
interface User {
  email: string;
}

// Method 1: 用 `in` 该值
function redirect(user: Admin | User) {
  if ("role" in user) {
    routeToAdminPage(user.role);
  } else {
    routeToHomePage(user.email);
  }
}

// 方法2:自定义类型守卫，在较老的TS版本或“在”不够的地方做同样的事情
function isAdmin(user: Admin | User): user is Admin {
  return (user as any).role !== undefined;
}
```

方法2也被称为用户定义的类型保护，对于可读的代码来说非常方便。这就是TS本身如何使用typeof和instanceof来改进类型。

### 可选的类型

如果一个组件有一个可选的道具，添加一个问号并在解构过程中进行赋值(或者使用defaultProps)。

```tsx
class MyComponent extends React.Component<{
  message?: string; // like this
}> {
  render() {
    const { message = "default" } = this.props;
    return <div>{message}</div>;
  }
}
```

你也可以用a !去断言某些东西是没有定义的，但这是不被鼓励的。

### 枚举类型

我们建议尽量避免使用枚举。

枚举有一些文档化的问题(TS团队同意)。枚举的另一种更简单的替代方法是声明一个字符串字面值的union类型:

```typescript
export declare type Position = "left" | "right" | "top" | "bottom";
```

如果你必须使用枚举，请记住，TypeScript中的枚举默认为numbers。你通常会想把它们作为字符串来使用:

```tsx
export enum ButtonSizes {
  default = "default",
  small = "small",
  large = "large",
}

// usage
export const PrimaryButton = (
  props: Props & React.HTMLProps<HTMLButtonElement>
) => <Button size={ButtonSizes.default} {...props} />;
```

### 类型的断言

有时候，你比TypeScript更清楚，你使用的类型比它想象的要懂，或者union类型需要断言到一个更特定的类型，以便与其他api一起工作，所以assert要使用as关键字。这告诉编译器您比它更了解。

```tsx
class MyComponent extends React.Component<{
  message: string;
}> {
  render() {
    const { message } = this.props;
    return (
      <Component2 message={message as SpecialMessageType}>{message}</Component2>
    );
  }
}
```

### 使用的类型

依靠TypeScript的类型推断是非常棒的…直到您意识到您需要一个被推断的类型，并且必须返回并显式声明类型/接口，以便您可以导出它们以供重用。

幸运的是，有了typeof，您就不必这样做了。只要在任何值上使用它:

```` tsx
const [state, setState] = React.useState({
  foo: 1,
  bar: 2,
}); //state的类型被推断为{foo: number, bar: number}

const someMethod = (obj: typeof state) => {
  // 获取状态类型，即使它是推断的
  // 一些使用obj的代码
  setState(obj); // this works
};
````

### 使用部分类型

```tsx
const [state, setState] = React.useState({
  foo: 1,
  bar: 2,
});

// 注意:在React.useState中不鼓励stale state的合并
// 我们正在演示如何使用Partial
const partialStateUpdate = (obj: Partial<typeof state>) =>
  setState({ ...state, ...obj });

// later on...
partialStateUpdate({ foo: 2 }); // this works
```

### 我需要的类型没有导出!

- 这可能会很烦人，但这里有一些方法来获取类型!

  -抓取组件的道具类型:使用“React”。ComponentProps '和' typeof '，并且可以省略任何重叠类型

```tsx
import { Button } from "library"; // 但不输出ButtonProps!
type ButtonProps = React.ComponentProps<typeof Button>; // 没问题!用自己的!
type AlertButtonProps = Omit<ButtonProps, "onClick">; // 修改
const AlertButton: React.FC<AlertButtonProps> = (props) => (
  <Button onClick={() => alert("hello")} {...props} />
);
```

你也可以使用ComponentPropsWithoutRef(而不是ComponentProps)和ComponentPropsWithRef(如果你的组件专门转发refs)

- 获取函数的返回类型:使用' ReturnType ':

```tsx
// 在某个库中，返回类型{baz: number}是推断的，但不是导出的
function foo(bar: string) {
  return { baz: 1 };
}

//  在你的应用程序中，如果你需要{baz: number}
type FooReturn = ReturnType<typeof foo>; // { baz: number }
```

事实上，你几乎可以获取任何公开的信息:

```tsx
function foo() {
  return {
    a: 1,
    b: 2,
    subInstArr: [
      {
        c: 3,
        d: 4,
      },
    ],
  };
}

// 获取函数的返回值类型
type InstType = ReturnType<typeof foo>;
type SubInstArr = InstType["subInstArr"];
type SubIsntType = SubInstArr[0];

let baz: SubIsntType = {
  c: 5,
  d: 6, // type checks ok!
};

//你可以只写一行代码，
//但请确保它是可向前读的
//你可以通过从左到右的阅读来理解它。
type SubIsntType2 = ReturnType<typeof foo>["subInstArr"][0];
let baz2: SubIsntType2 = {
  c: 5,
  d: 6, // type checks ok!
};
```

### 对象进行结构赋值

```` typescript
export const cleanObject = (object: {[key: string]: unknown}=>{
    ...{object}
})
````

### 箭头函数使用泛型

```` typescript
const test = <V>(value: V) => {
    ....
}

const GlobalTable = <T>(props: GlobalTableProp<T>) => {
  return (
    <div>
      <Table columns={columns} dataSource={datasource} />}
    </div>
  )
}
````

