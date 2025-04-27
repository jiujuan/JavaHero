## TypeScript 学习快速入门

## 变量声明

TypeScript 支持 let、const 和 var 进行变量声明。
```TypeScript
// 使用 let 声明变量
let isDone: boolean = false;

// 使用 const 声明常量
const pi: number = 3.14;

// 类型推断
let name = "Alice"; // 推断为 string 类型
```

## 基础数据类型

TypeScript 提供了一些基本数据类型，如 `number`、`string`、`boolean`、`array`、`tuple` 等。
```TypeScript
let count: number = 42;
let userName: string = "John Doe";
let isActive: boolean = true;

// 数组
let numbers: number[] = [1, 2, 3, 4];
let strings: Array<string> = ["a", "b", "c"];

// 元组（Tuple）
let tuple: [string, number];
tuple = ["hello", 10]; // 正确
```

## 枚举 (Enum)
```TypeScript
enum Color {
  Red,
  Green,
  Blue,
}
let myColor: Color = Color.Green;
console.log(myColor); // 输出：1
```

## 联合枚举和枚举成员类型
```TypeScript
enum Direction {
  Up = "UP",
  Down = "DOWN",
}

type DirectionType = keyof typeof Direction; // "Up" | "Down"
```

## Any 和 Unknown 类型
```TypeScript
let notSure: any = 4;
notSure = "maybe a string instead"; // 允许赋值为其他类型

let unknownValue: unknown = "hello";
unknownValue = 42; // 允许赋值为其他类型，但需要类型断言后才能使用
```

## 函数
```TypeScript
// 带类型注解的函数
function add(a: number, b: number): number {
  return a + b;
}

// 可选参数和默认参数
function greet(name: string, age?: number): string {
  return `Hello, ${name}`;
}

// 箭头函数
const multiply = (x: number, y: number): number => x * y;
```

##  接口 (Interface)
```TypeScript
interface Person {
  name: string;
  age: number;
  isStudent?: boolean; // 可选属性
}

const john: Person = {
  name: "John",
  age: 25,
};

// 索引类型查询和访问 (Keyof, Indexed Access Types)
interface User {
  id: number;
  name: string;
  age: number;
}

type UserKey = keyof User; // "id" | "name" | "age"

type UserIdType = User["id"]; // number
```

## 类 (Class)
```TypeScript
class Animal {
  private name: string;

  constructor(name: string) {
    this.name = name;
  }

  public move(distance: number): void {
    console.log(`${this.name} moved ${distance}m.`);
  }
}

const dog = new Animal("Dog");
dog.move(10);
```

## 泛型 (Generics)
```TypeScript
function identity<T>(arg: T): T {
  return arg;
}

let output = identity<string>("Hello World"); // T 被推断为 string
let output1 = identity<number>(42);

// 泛型接口
interface GenericInterface<T> {
  value: T;
}

const genericObj: GenericInterface<number> = { value: 42 };

// 泛型类
class GenericNumber<T> {
  zeroValue: T;
  add: (x: T, y: T) => T;

  constructor(zeroValue: T, addFn: (x: T, y: T) => T) {
    this.zeroValue = zeroValue;
    this.add = addFn;
  }
}

let myGenericNumber = new GenericNumber<number>(0, (x, y) => x + y);
console.log(myGenericNumber.add(10, 20));
```

## 模块 (Modules)
```TypeScript
// 导出模块
export class Calculator {
  add(a: number, b: number): number {
    return a + b;
  }
}

// 导入模块
import { Calculator } from "./calculator";
const calc = new Calculator();
console.log(calc.add(1, 2));

//-----
// math.ts
export function add(a: number, b: number): number {
  return a + b;
}

// app.ts
import { add } from "./math";
console.log(add(10, 20));
```

## 类型断言 (Type Assertion)
手动告诉编译器变量的类型。
```TypeScript
let someValue: unknown = "this is a string";
let strLength: number = (someValue as string).length;

let anotherValue: unknown = "another string";
let anotherLength: number = (<string>anotherValue).length; // 或者使用尖括号语法（不推荐在 JSX 中使用）
```

## 联合类型 (Union Types)
联合类型允许一个变量可以是多种类型之一
```TypeScript
let id: number | string;
id = 123; // 正确
id = "abc"; // 也正确

function formatId(id: number | string): string {
  return `ID: ${id}`;
}
```

## 类型别名 (Type Alias)
类型别名允许为复杂的类型定义一个简短的名称。
```TypeScript
type UserId = number | string;

function getUser(id: UserId): void {
  console.log(`Fetching user with id: ${id}`);
}
```

## 类型守卫 (Type Guards)
通过类型检查缩小联合类型的范围
```TypeScript
function printId(id: number | string): void {
  if (typeof id === "string") {
    console.log(`ID is a string: ${id.toUpperCase()}`);
  } else {
    console.log(`ID is a number: ${id}`);
  }
}
```

## 字面量类型 (Literal Types)
可以限制变量的值为某些特定的字面量。
```TypeScript
type Direction = "up" | "down" | "left" | "right";

function move(dir: Direction): void {
  console.log(`Moving ${dir}`);
}

move("up"); // 正确
// move("forward"); // 错误
```

## 模板字面量类型 (Template Literal Types)

可以基于字符串模板动态生成类型。
```TypeScript
type Prefix = "user_" | "admin_";
type Suffix = "id" | "name";

type UserKeys = `${Prefix}${Suffix}`;
// "user_id" | "user_name" | "admin_id" | "admin_name"
```

## 交叉类型 (Intersection Types)
将多个类型合并为一个更复杂的类型。
```TypeScript
interface Person {
  name: string;
}

interface Employee {
  id: number;
}

type Staff = Person & Employee;

const staffMember: Staff = {
  name: "Alice",
  id: 101,
};
```

## 可选链 (Optional Chaining)
用于安全地访问嵌套对象的属性。
```TypeScript
let user: { name?: string; address?: { city?: string } } = {};

// 安全地访问
console.log(user?.address?.city); // undefined

//--------
let user = { address: { city: "New York" } };

// 可选链
console.log(user?.address?.city); // "New York"
```

## 空值合并运算符 (Nullish Coalescing)
提供默认值，防止 null 或 undefined。
```TypeScript
let input: string | undefined;
let defaultValue: string = input ?? "Default Value";

console.log(defaultValue); // "Default Value"

//------
let input2: string | null = null;
let defaultValue2 = input2 ?? "Default Value";
console.log(defaultValue2); // "Default Value"
```

## 接口扩展 (Interface Inheritance)
一个接口可以扩展另一个接口。
```TypeScript
interface Animal {
  name: string;
}

interface Dog extends Animal {
  breed: string;
}

const myDog: Dog = {
  name: "Buddy",
  breed: "Golden Retriever",
};
```

## 抽象类 (Abstract Class)
抽象类用于定义不能直接实例化的基类。
```TypeScript
abstract class Shape {
  abstract getArea(): number;

  printArea(): void {
    console.log(`Area: ${this.getArea()}`);
  }
}

class Circle extends Shape {
  constructor(public radius: number) {
    super();
  }

  getArea(): number {
    return Math.PI * this.radius ** 2;
  }
}

const circle = new Circle(5);
circle.printArea(); // 输出面积
```

## 装饰器 (Decorators)
装饰器是一种特殊的语法，用于修饰类或类的成员（需要在 tsconfig.json 中启用 experimentalDecorators）。
```TypeScript
function Logger(target: any, propertyKey: string): void {
  console.log(`Property ${propertyKey} is being accessed`);
}

class User {
  @Logger
  name: string;

  constructor(name: string) {
    this.name = name;
  }
}

const user = new User("Alice");
```

## 控制流分析 (Control Flow Analysis)
TypeScript 会根据代码的控制流来推断类型。
```TypeScript
function example(value: string | number | null): void {
  if (value === null) {
    console.log("Value is null");
  } else if (typeof value === "string") {
    console.log(`String value: ${value}`);
  } else {
    console.log(`Number value: ${value}`);
  }
}
```

## 映射类型 (Mapped Types)
通过映射类型可以批量修改类型的属性。

```TypeScript
type ReadonlyPerson = {
  readonly [K in keyof Person]: Person[K];
};

interface Person {
  name: string;
  age: number;
}

const readonlyPerson: ReadonlyPerson = { name: "Alice", age: 30 };
// readonlyPerson.age = 31; // 错误，属性是只读的

//------------------------------
type Readonly<T> = {
  readonly [P in keyof T]: T[P];
};

type Optional<T> = {
  [P in keyof T]?: T[P];
};

interface User {
  name: string;
  age: number;
}

type ReadonlyUser = Readonly<User>;
type OptionalUser = Optional<User>;

const user: ReadonlyUser = { name: "John", age: 30 };
// user.age = 31; // 错误，属性是只读的
```

## 条件类型 (Conditional Types)
根据条件产生不同的类型。
```TypeScript
type IsString<T> = T extends string ? true : false;

type Test1 = IsString<string>; // true
type Test2 = IsString<number>; // false
```

## 类型推导 (Type Inference)
TypeScript 可以根据上下文自动推断类型。
```TypeScript
let message = "Hello!"; // 推断为 string
let numbers = [1, 2, 3]; // 推断为 number[]

const num = 42; // 推断为 number
const str = "Hello"; // 推断为 string

const add = (a: number, b: number) => a + b; // 返回值类型推断为 number
```

## 非空断言操作符 (Non-null Assertion Operator)

用于告诉编译器某个值不可能为 null 或 undefined。
```TypeScript
let value: string | null = "Hello";
console.log(value!.toUpperCase());
```

## 类型工具 (Type Utility)
TypeScript 提供了一些内置的类型工具，如 `Partial`、`Readonly`、`Pick`、`Omit` 等。
```TypeScript
interface User {
  id: number;
  name: string;
  age: number;
}

type PartialUser = Partial<User>; // 所有属性变为可选
type ReadonlyUser = Readonly<User>; // 所有属性变为只读
type PickName = Pick<User, "name">; // 只选取 name 属性
type OmitAge = Omit<User, "age">; // 移除 age 属性
```

## 模块声明 (Ambient Declarations)
当使用第三方库时，可以为其添加类型声明。
```TypeScript
declare module "my-library" {
  export function doSomething(): void;
}
```


