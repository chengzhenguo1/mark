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
12
let someValue: any = "this is a string";
let strLength: number = (someValue as string).length;
12
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
1
```

等价于

```javascript
let newName1 = o.a;
let newName2 = o.b;
12
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
12345
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
12345678910
```

上述代码，向`createSquare`传入了一个对象字面量，就会对这个对象字面量进行检查，发现目标类型里不包含`colour`这个属性，就会报错。

**解决方法：**
一：使用类型断言

```javascript
let mySquare = createSquare({ colour: "red", width: 100 } as SquareConfig);
1
```

二：添加一个字符串索引签名（最佳）
如果 `SquareConfig`带有上面定义的类型的`color`和`width`属性，并且还会带有任意数量的其它属性，那么我们可以这样定义它：

```javascript
interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any; // 表示可以有任意数量和类型的属性
}
12345
```

三：将对象赋值给一个变量，再传入，跳过属性检查

```javascript
let squareOptions = { colour: "red", width: 100 };
let mySquare = createSquare(squareOptions);
12
```

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
12345
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
123456789101112
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
123456789101112131415161718192021
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