---
name: arkts-language-fundamentals
description: 专门用于指导和审查符合鸿蒙 ArkTS 语言规范（严格模式）的代码。当用户要求编写或审查ArkTS纯逻辑代码，或者从TypeScript/JavaScript迁移代码到ArkTS时，务必触发此SKILL，以避免生成包含 any、动态属性、解构赋值、globalThis、隐式类型转换等非法语法的代码。 Use when this capability is needed.
metadata:
  author: Kenshin2438
---

# ArkTS 语言基础与严格规范

本SKILL旨在确保 AI 在编写、重构或审查鸿蒙应用代码时，严格遵守 **ArkTS 的静态强类型和运行时限制**。ArkTS 不是普通的 TypeScript，它为了在鸿蒙系统上实现极致性能（方舟字节码），移除了大量动态和模糊的语法。

## 核心原则 (Core Principles)

在生成任何 ArkTS 代码时，**必须绝对禁止**以下 TypeScript/JavaScript 的常见动态特性：

### 1. 严格的静态类型 (Strict Typing & Null Safety)
- **禁用 `any`、`unknown` 和 `ESObject`**：所有的变量、参数、返回值必须有明确的类型（如 `string`, `number`, 自定义 `class` 或 `interface`）。如果实在未知，使用 `Object` 或泛型。
- **严格属性初始化**：在class中，属性必须在声明时或构造函数中初始化。若允许为空，请使用可选属性 `prop?: Type` 或联合类型 `prop: Type | null = null`。
- **严格空值检查**：访问可能为 `null` 或 `undefined` 的属性/方法前，必须进行显式判空（如 `if (val)`）或使用非空断言 `!`。
- **禁止绕过类型检查**：绝不允许使用 `// @ts-ignore` 或 `// @ts-nocheck`。
- **异常类型断言**：`catch (e)` 语句中必须写成无类型 `catch (error)`，然后再通过 `let e = error as Error` 或 `as BusinessError` 进行断言。抛出异常时也必须 `throw error as Error`。

### 2. 禁止动态对象布局 (No Dynamic Objects)
- **禁止动态添加/删除属性**：对象在实例化后，其内存布局被锁定。不能使用 `obj.newProp = value` 动态添加属性，也不能使用 `delete obj.prop`。
- **禁止字符串索引访问**：不能使用 `obj['propertyName']` 访问或修改对象的属性，必须使用点语法 `obj.propertyName`。
- **禁止索引签名 (Index Signatures)**：不支持 `[key: string]: type` 的接口定义。
  - **替代方案**：如果需要动态的键值对集合，**必须**使用 `Map<K, V>` 或 `Record<K, V>`。
- **禁用结构化类型（鸭子类型）**：不能将一个对象字面量直接赋值给带有构造函数参数或带有方法的类类型的变量。必须使用 `new ClassName()` 来实例化对象。
- **禁用 `in` 操作符**：不支持 `'key' in obj`，请使用 `Object.keys(obj)` 配合 `for...of` 遍历判断。
- **禁止展开运算符 (Spread Operator)**：ArkTS不支持对对象使用展开运算符 `...obj`。请通过逐个属性赋值语句或 `Record` 遍历完成。

### 3. 语法缩减与限制 (Syntax Restrictions)
- **禁用解构赋值**：ArkTS 不支持对象或数组的解构赋值。
  - ❌ 错误：`let { name, age } = user;` 或 `let [a, b] = array;`
  - ✅ 正确：`let name = user.name; let age = user.age;`
- **禁用 `for...in` 循环**：不支持通过 `for...in` 遍历对象属性。请使用普通的 `for` 循环、`for...of`（针对数组/Map）或 `Object.keys()`, `Object.entries()`。
- **禁用高级类型工具**：不支持交叉类型 (`&`)，不支持 `Partial`, `Pick`, `Omit` 等 TS 内置映射/条件类型。只能使用类继承 (`extends`) 或接口实现 (`implements`)。不支持映射类型如 `[Property in keyof C]: string`，请改用 `Record<keyof C, string>`。
- **不支持带 flag 的正则字面量**：如果正则表达式使用了标志符（如 `/pattern/g`），必须将其改为 `new RegExp('pattern', 'g')` 构造函数形式。
- **禁用隐式类型转换**：不支持一元多态操作符如 `+'5'` 或 `~'5'`，必须使用显式转换函数如 `Number.parseInt('5')`。

### 4. 函数与方法限制 (Functions & Methods)
- **禁用函数中的 `apply`, `call`, `bind`**：不允许动态改变函数上下文，请直接使用箭头函数 (`=>`) 绑定词法作用域。
- **禁止独立的 `this`**：函数内不能使用独立的 `this`，必须放在类的方法中或作为参数传入。类的静态方法中禁止使用 `this`，必须使用类名（如 `ClassName.value`）。
- **不支持在构造函数参数中声明字段**：
  - ❌ 错误：`constructor(private name: string) {}`
  - ✅ 正确：必须在类作用域内先声明 `private name: string;`，然后在 `constructor(name: string) { this.name = name; }` 中赋值。
- **禁止动态方法替换**：类的实例方法一旦声明不可被覆盖（如 `c1.add = sub` 是错误的）。如需动态修改，请将方法声明为函数类型的类属性（如 `add: (a: number, b: number) => number = (a, b) => a + b`）。
- **禁用调用签名和构造签名**：
  - ❌ `interface I { (value: string): void }` -> ✅ `type I = (value: string) => void`
  - ❌ `new (value: string): ClassName` -> ✅ `() => ClassName`

### 5. 其他重要规则
- **变量声明**：仅支持 `let` 和 `const`，**绝对禁止**使用 `var`。
- **私有属性**：使用 `private` 关键字，**禁止**使用 `#` 开头的私有字段语法。
- **禁用 `globalThis`**：ArkTS不支持 `globalThis`。请通过构造单例对象（如 `GlobalContext` 类配合 `getInstance()`）来实现全局对象的功能。
- **不支持 `Object.fromEntries()`**：需手动遍历赋值给 `Record` 对象。
- **禁用 `typeof` 类型查询**：不能用 `let t: typeof c` 声明类型，必须导入并使用显式的类或接口名。

## 代码审查与重构流程

当你被要求审查一段 TS/JS 代码并将其转换为 ArkTS 时，请按以下步骤输出：
1. **违规语法分析**：指出原代码中使用了哪些 ArkTS 不支持的特性（如 `any`，动态属性，解构赋值，鸭子类型，globalThis 等）。
2. **规范解释**：简要说明 ArkTS 为什么不支持该特性（如：为了静态编译性能，锁定内存布局，安全类型检查）。
3. **ArkTS 标准代码**：提供完全符合上述所有约束的 ArkTS 修正代码。

## 示例 (Examples)

### 示例 1：字典与动态属性的替换
**输入 (TS)**:
```typescript
let cache: any = {};
cache['user_1'] = { name: 'Alice' };
```
**输出 (ArkTS)**:
```typescript
class User {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
}
// 使用 Record 或 Map 替代动态对象和 any
let cache: Record<string, User> = {};
cache['user_1'] = new User('Alice'); 
// 或者使用 Map
let mapCache: Map<string, User> = new Map();
mapCache.set('user_1', new User('Alice'));
```

### 示例 2：解构赋值与鸭子类型的替换
**输入 (TS)**:
```typescript
function processUser({ name, age }: { name: string, age: number }) {
  console.log(name, age);
}
processUser({ name: 'Bob', age: 30 });
```
**输出 (ArkTS)**:
```typescript
class UserData {
  name: string;
  age: number;
  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
}

function processUser(user: UserData): void {
  let name = user.name; // 移除解构
  let age = user.age;
  console.log(name, age.toString());
}
// 移除鸭子类型，使用 new 实例化
processUser(new UserData('Bob', 30));
```

### 示例 3：避免 globalThis 和 静态方法 this
**输入 (TS)**:
```typescript
class App {
  static version = '1.0';
  static getVersion() {
    return this.version; // 静态方法使用 this
  }
}
globalThis.appData = 'test'; // 使用 globalThis
```
**输出 (ArkTS)**:
```typescript
export class GlobalContext {
  private constructor() {}
  private static instance: GlobalContext;
  private _objects = new Map<string, Object>();

  public static getContext(): GlobalContext {
    if (!GlobalContext.instance) {
      GlobalContext.instance = new GlobalContext();
    }
    return GlobalContext.instance;
  }

  getObject(key: string): Object | undefined { return this._objects.get(key); }
  setObject(key: string, objectClass: Object): void { this._objects.set(key, objectClass); }
}

class App {
  static version = '1.0';
  static getVersion() {
    return App.version; // 静态方法使用类名
  }
}
GlobalContext.getContext().setObject('appData', 'test'); // 单例替代 globalThis
```

---
> Source: [Kenshin2438/openHarmony-docs](https://github.com/Kenshin2438/openHarmony-docs) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
