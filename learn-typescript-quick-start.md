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

// 泛型接口
interface GenericInterface<T> {
  value: T;
}

const genericObj: GenericInterface<number> = { value: 42 };
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
```

## 类型断言 (Type Assertion)
```TypeScript
let someValue: unknown = "this is a string";
let strLength: number = (someValue as string).length;

let anotherValue: unknown = "another string";
let anotherLength: number = (<string>anotherValue).length;
```
