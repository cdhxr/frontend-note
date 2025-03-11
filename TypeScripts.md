TypeScript 类型讲解


# TypeScript 类型概述

## 1. 基本类型
- **`boolean`**: 表示布尔值，值为 `true` 或 `false`。
- **`number`**: 表示数字类型，包括整数和浮点数。
- **`string`**: 表示字符串类型。

## 示例代码：
```typescript
let isActive: boolean = true;
let age: number = 25;
let name: string = "Alice";
```

---

## 2. 枚举类型 (`enum`)
用于定义一组命名的常量。

## 示例代码：
```typescript
enum Color {
  Red,
  Green,
  Blue,
}
let favoriteColor: Color = Color.Green;
```

---

## 3. 特殊类型
- **`any`**: 表示任意类型，跳过类型检查。
- **`unknown`**: 表示未知类型，但比 `any` 更安全，需要类型断言才能使用。
- **`void`**: 表示函数没有返回值。

## 示例代码：
```typescript
let data: any = 42; // 可以是任何类型
let input: unknown = "text"; // 必须进行检查
function logMessage(): void {
  console.log("No return value");
}
```

---

## 4. `never` 类型
表示永远不会有返回值的类型（如抛出错误的函数）。

## 示例代码：
```typescript
function throwError(message: string): never {
  throw new Error(message);
}
```

---

## 5. 数组类型
使用 `[]` 表示数组。

## 示例代码：
```typescript
let numbers: number[] = [1, 2, 3];
let strings: string[] = ["a", "b", "c"];
```

---

## 6. 元组类型 (`tuple`)
表示固定长度和类型的数组。

## 示例代码：
```typescript
let tuple: [string, number] = ["Alice", 25];
```




# TypeScript 基础 - Interface

## 定义

`interface` 用于定义对象的类型结构。

---

## 特点
1. **可选属性**：通过 `?` 标记属性为可选。
   ```typescript
   interface Person {
       name: string;
       age?: number; // 可选属性
   }

   const p1: Person = { name: "Lin" }; // age 可以省略
   ```

2. **只读属性**：通过 `readonly` 声明属性为只读。
   ```typescript
   interface Person {
       readonly id: number;
       name: string;
   }

   const p2: Person = { id: 1, name: "Lin" };
   // p2.id = 2; // 错误：只读属性不能修改
   ```

3. **描述函数类型**：可以定义函数的参数和返回值类型。
   ```typescript
   interface Add {
       (x: number, y: number): number; // 描述函数类型
   }

   const add: Add = (a, b) => a + b;
   ```

4. **描述自定义属性**：使用索引签名动态定义属性。
   ```typescript
   interface RandomKey {
       [propName: string]: string; // 键和值均为字符串类型
   }

   const obj: RandomKey = {
       a: "hello",
       b: "world",
       c: "typescript",
   };
   ```

---

## 总结
接口非常灵活，支持 **Duck Typing**（鸭子类型），即只关心对象的形状，而不在乎具体类型。

# TypeScript 类与抽象类

## 特点
1. **访问修饰符**：
   - **`public`**: 默认修饰符，属性或方法可以被自由访问。
   - **`private`**: 私有成员，只能在声明它的类中访问。
   - **`protected`**: 受保护成员，可以在声明它的类及其子类中访问。

   示例代码：
   ```typescript
   class Person {
       public name: string;
       private age: number;
       protected address: string;

       constructor(name: string, age: number, address: string) {
           this.name = name;
           this.age = age;
           this.address = address;
       }
   }

   class Employee extends Person {
       constructor(name: string, age: number, address: string) {
           super(name, age, address);
           console.log(this.address); // 可以访问
           // console.log(this.age); // 错误：age 是私有的
       }
   }
   ```

---

2. **抽象类**：
   - 抽象类只能被继承，不能被实例化。
   - 抽象方法没有具体实现，必须由子类实现。

   示例代码：
   ```typescript
   abstract class Animal {
       abstract makeSound(): void; // 抽象方法
       move(): void {
           console.log("Moving...");
       }
   }

   class Dog extends Animal {
       makeSound(): void {
           console.log("Bark");
       }
   }

   const dog = new Dog();
   dog.makeSound(); // 输出：Bark
   ```

---

3. **接口约束类**：
   - 使用 `implements` 关键字实现接口。
   - 一个类可以实现多个接口。

   示例代码：
   ```typescript
   interface Flyable {
       fly(): void;
   }

   interface Swimmable {
       swim(): void;
   }

   class Bird implements Flyable, Swimmable {
       fly(): void {
           console.log("Flying...");
       }
       swim(): void {
           console.log("Swimming...");
       }
   }
   ```

# TS高级类型

1. **联合类型 (`|`)**：
   - 联合类型用于定义一个值可以是多个类型中的任何一个。比如，`number | string` 表示该值既可以是数字，也可以是字符串。
   - 示例代码：
     ```typescript
     let num: number | string;
     num = 8;      // 合法
     num = 'eight'; // 也合法
     ```

2. **交叉类型 (`&`)**：
   - 交叉类型用于合并多个类型，表示该值必须同时符合多个类型的要求。
   - 示例代码：
     ```typescript
     interface Person {
         name: string;
         age: number;
     }

     interface Worker {
         company: string;
     }

     type Employee = Person & Worker;

     const worker: Employee = {
         name: 'John',
         age: 30,
         company: 'XYZ Ltd'
     };
     ```

3. **类型断言**：
   - 类型断言用于告诉 TypeScript 编译器某个值的类型。它的语法类似于强制类型转换，但 TypeScript 并不会进行类型检查。
   - 示例代码：
     ```typescript
     const strLength: any = 'hello';
     const length: number = (strLength as string).length; // 断言为string
     ```

4. **类型别名 (`type` vs `interface`)**：
   - **相同点**：
     - 都可以用于定义对象或函数类型。
     - 都允许继承其他类型。
   - **不同点**：
     - `interface` 用于定义对象类型，是 TypeScript 特有的关键字；而 `type` 用于给类型起别名，可以用于更多情况，比如联合类型、交叉类型等。
     - `type` 不能继承基本类型，`interface` 只能继承对象类型。
     - `interface` 可以进行重复声明合并，`type` 不支持。
   
   示例代码：
   ```typescript
   // 使用 interface
   interface Person {
       name: string;
       age: number;
   }

   // 使用 type
   type Person2 = {
       name: string;
       age: number;
   };
   ```

   总结：`interface` 和 `type` 都能定义对象的结构，但 `interface` 更侧重于用于描述类或对象，而 `type` 提供了更广泛的用途，支持更多复杂类型的组合。

# TS泛型

---

### **1. 泛型基本使用**

泛型允许定义可复用的、适用于多种类型的组件。

```typescript
function identity<T>(value: T): T {
    return value;
}

const num = identity<number>(42);  // T 为 number
const str = identity<string>("Hello");  // T 为 string
```
**解释**：  
`T` 是泛型变量，调用时可以明确指定类型，也可以让 TypeScript 推断。

---

### **2. `typeof`：获取类型**

`typeof` 获取变量的类型用于进一步操作。

```typescript
const example = { name: "Alice", age: 25 };
type ExampleType = typeof example; // { name: string; age: number }
```
**解释**：  
`typeof` 提取现有变量的类型（而非值）。

---

### **3. `keyof`：获取所有键**

`keyof` 提取对象类型的键，返回联合类型。

```typescript
type Person = { name: string; age: number };
type PersonKeys = keyof Person; // "name" | "age"
```
**解释**：  
`keyof` 返回类型的键名，用于动态处理类型。

---

### **4. `in`：遍历枚举类型**

`in` 用于构造类型时遍历枚举成员。

```typescript
type Options = { [K in "option1" | "option2"]: boolean };
// 等价于 { option1: boolean; option2: boolean }
```
**解释**：  
`in` 遍历联合类型或枚举，动态生成新类型。

---

### **5. `T[K]`：索引访问**

通过索引访问获取指定键的类型。

```typescript
type Person = { name: string; age: number };
type NameType = Person["name"]; // string
```
**解释**：  
`T[K]` 提取对象中指定键对应的值类型。

---

### **6. `extends`：泛型约束**

约束泛型变量必须满足某种类型。

```typescript
function getLength<T extends { length: number }>(value: T): number {
    return value.length;
}

getLength("Hello");  // 合法
getLength([1, 2, 3]);  // 合法
```
**解释**：  
`T extends U` 表示 `T` 必须具有 `U` 的特性。

---

### **7. `Partial<T>`：将类型属性变为可选**

```typescript
type Person = { name: string; age: number };
type PartialPerson = Partial<Person>; // { name?: string; age?: number }
```
**解释**：  
`Partial<T>` 将对象类型中所有属性设为可选。

---

### **8. `Required<T>`：将类型属性变为必选**

```typescript
type Person = { name?: string; age?: number };
type RequiredPerson = Required<Person>; // { name: string; age: number }
```
**解释**：  
`Required<T>` 将对象类型中所有属性设为必选。

---

### **9. `Readonly<T>`：将类型属性变为只读**

```typescript
type Person = { name: string; age: number };
type ReadonlyPerson = Readonly<Person>; // { readonly name: string; readonly age: number }
```
**解释**：  
`Readonly<T>` 将对象类型中所有属性设为只读，禁止修改。


# TypeScript 类型声明和配置相关功能讲解

---

#### **1. `declare`：声明全局变量或类型**

`declare` 用于在 TypeScript 中声明第三方库的变量或类型，避免类型检查报错。

```typescript
declare var $: (selector: string) => any; // 声明 jQuery 的全局变量
$("body").css("background", "blue"); // 使用时不会报错
```
**解释**：  
`declare` 告诉 TypeScript 有一个全局变量或函数存在，但不关心其具体实现。

---

#### **2. `.d.ts`：声明文件定义**

`.d.ts` 文件是专门用来声明类型的文件，通常伴随库一起发布，用于描述库的 API。

```typescript
// example.d.ts
declare module "myLibrary" {
    export function greet(name: string): string;
}
```
**解释**：  
`.d.ts` 文件只包含类型声明，没有具体实现，用于支持 IDE 自动补全和类型检查。

---

#### **3. `@types`：第三方库的类型包**

`@types` 是 TypeScript 的社区维护类型库集合，用于给没有类型定义的第三方库提供类型支持。

```bash
npm install @types/lodash
```
**解释**：  
安装 `@types` 类型包后，使用对应库时会有完整的类型支持。

---

#### **4. `tsconfig.json`：定义 TS 配置**

`tsconfig.json` 是 TypeScript 项目的配置文件，控制编译行为。

```json
{
  "compilerOptions": {
    "target": "ES6",         // 编译目标为 ES6
    "module": "commonjs",    // 使用 CommonJS 模块
    "strict": true           // 启用严格模式
  }
}
```
**解释**：  
`tsconfig.json` 文件帮助开发者配置编译器选项和文件包含范围。

--- 

总结：  
- `declare` 用于快速声明变量或类型。  
- `.d.ts` 文件专门用来写类型声明。  
- `@types` 提供现成的第三方库类型支持。  
- `tsconfig.json` 用于配置项目的编译行为。
