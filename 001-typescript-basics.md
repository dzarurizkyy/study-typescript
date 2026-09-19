# 📘 TypeScript Basics

A practical reference guide for learning TypeScript from scratch — covering project setup, the type system, interfaces, functions, and control flow.

---

## 📋 Table of Contents

- [Introduction](#-introduction)
  - [What Is TypeScript?](#what-is-typescript)
  - [Why Learn TypeScript?](#why-learn-typescript)
  - [The Toolchain at a Glance](#the-toolchain-at-a-glance)
- [Project Setup](#-project-setup)
- [Say Hello Function](#-say-hello-function)
- [Compiling TypeScript](#-compiling-typescript)
- [Include and Exclude](#-include-and-exclude)
- [Primitive Data Types & Variables](#-primitive-data-types--variables)
  - [Primitive Data Types](#primitive-data-types)
  - [Variable Declaration](#variable-declaration)
  - [Babel and TypeScript](#babel-and-typescript)
- [Array Types](#-array-types)
  - [Array](#array)
  - [Read-Only Array](#read-only-array)
  - [Tuple](#tuple)
- [Any & Union Types](#-any--union-types)
  - [Any](#any)
  - [Union Type](#union-type)
  - [Narrowing a Union Type](#narrowing-a-union-type)
- [Type Alias & Object Types](#-type-alias--object-types)
  - [Type Alias](#type-alias)
  - [Type Alias for Union Types](#type-alias-for-union-types)
  - [Object Type](#object-type)
  - [Optional Properties](#optional-properties)
- [Enum, Null & Undefined](#-enum-null--undefined)
  - [Enum](#enum)
  - [Null and Undefined](#null-and-undefined)
- [Interface](#-interface)
  - [Type Alias vs Interface](#type-alias-vs-interface)
  - [Readonly Properties](#readonly-properties)
  - [Function Interface](#function-interface)
  - [Indexable Interface](#indexable-interface)
  - [Extending Interface](#extending-interface)
  - [Functions in Interface](#functions-in-interface)
- [Intersection Types & Type Assertions](#-intersection-types--type-assertions)
  - [Intersection Types](#intersection-types)
  - [Type Assertions](#type-assertions)
- [Functions](#-functions)
  - [Function Declaration](#function-declaration)
  - [Function Parameters](#function-parameters)
  - [Function Overloading](#function-overloading)
  - [Function as Parameter](#function-as-parameter)
- [Control Flow & Loops](#-control-flow--loops)
  - [If Statement](#if-statement)
  - [Ternary Operator](#ternary-operator)
  - [Switch Statement](#switch-statement)
  - [For Loop](#for-loop)
  - [While Loop](#while-loop)
  - [Do While Loop](#do-while-loop)
  - [Break and Continue](#break-and-continue)
- [JavaScript Features in TypeScript](#-javascript-features-in-typescript)
- [Compilation Flow](#-compilation-flow)
- [Quick Reference](#-quick-reference)
- [Best Practices](#-best-practices)

---

## 🎯 Introduction

### What Is TypeScript?

- TypeScript is an object-oriented programming language created by Microsoft
- It **compiles down** to plain JavaScript — browsers and Node.js never run TypeScript directly
- It makes code easier to read and debug compared to plain JavaScript
- It is a **strongly typed** language, similar in spirit to Java, C#, and C/C++

> Reference: [typescriptlang.org](https://www.typescriptlang.org/)

### Why Learn TypeScript?

- **Wide adoption** — Many companies have adopted TypeScript because it removes a lot of the friction of programming at scale
- **Automatic transpilation** — Because TypeScript compiles to JavaScript, you don't need to worry about which JavaScript features are unsupported in your target environment; TypeScript handles that automatically
- **Ecosystem momentum** — Popular frameworks such as React, Vue, and NestJS increasingly default to TypeScript

### The Toolchain at a Glance

TypeScript is not one tool but several, and knowing which piece does what explains most of the surprises later in this guide:

| Piece | Role |
| --- | --- |
| `typescript` (`tsc`) | The compiler and type checker — the **only** tool here that actually validates types |
| `tsconfig.json` | Every compiler setting: which files to compile, which JavaScript to emit, how strict to be |
| `@babel/preset-typescript` | Strips type annotations so Jest can run tests — without checking them |
| `jest` | The test runner used throughout this guide |
| `@types/*` | Type definitions for libraries that ship plain JavaScript |
| `dist/` | Where compiled JavaScript lands — the only code Node.js or a browser ever runs |

> **Key Insight:** Every type annotation TypeScript adds is erased at compile time — the emitted JavaScript has no trace of `string`, `number`, or `interface`. Type safety only protects you while writing and building code, never at runtime. [Compilation Flow](#-compilation-flow) traces that pipeline end to end, and [Babel and TypeScript](#babel-and-typescript) shows what it means in practice.

---

## 🏗️ Project Setup

> **Key Insight:** TypeScript is not a runtime — it is a compiler and a type checker bolted onto JavaScript. Every TypeScript project therefore needs a compilation and testing pipeline before you write a single line of business logic.

1. **Install Node.js**:
   - Download the **LTS** version from `https://nodejs.org/`
   - Verify the installation:

     ```bash
     node -v
     npm -v
     ```

2. **Create the Project**:

   ```bash
   mkdir belajar-typescript-dasar
   cd belajar-typescript-dasar
   npm init
   ```

   - Open `package.json` and add `"type": "module"`

   > **Note:** `"type": "module"` switches `package.json` to native ES modules, which is why every example in this guide uses `import`/`export` syntax instead of CommonJS `require`.

3. **Add Jest for Unit Testing**:

   ```bash
   npm install --save-dev jest @types/jest
   ```

   > Reference: [npmjs.com/package/jest](https://www.npmjs.com/package/jest)

4. **Add Babel**:

   ```bash
   npm install --save-dev babel-jest @babel/preset-env
   ```

   > Reference: [babeljs.io/setup#installation](https://babeljs.io/setup#installation)

5. **Install TypeScript**:

   ```bash
   npm install --save-dev typescript
   ```

   - Generate `tsconfig.json`:

     ```bash
     npx tsc --init
     ```

   - Verify the installation:

     ```bash
     npx tsc -v
     ```

   > TypeScript is installed **locally per project** (`--save-dev`), not globally — that's why every command in this guide uses `npx tsc` instead of a bare `tsc`.

6. **Set Up TypeScript for Jest**:

   ```bash
   npm install --save-dev @babel/preset-typescript
   npm install --save-dev @jest/globals
   ```

   > Reference: [jestjs.io/docs/getting-started#using-typescript](https://jestjs.io/docs/getting-started#using-typescript)

   - **`basic-typescript/babel.config.json`**:

     ```json
     {
       "presets": ["@babel/preset-env", "@babel/preset-typescript"]
     }
     ```

---

## 👋 Say Hello Function

Before diving into the type system, let's create a simple `sayHello` function in TypeScript and cover it with a unit test.

**`src/say-hello.ts`**

```typescript
export function sayHello(name: string): string {
  return `Hello ${name}`;
}
```

**`tests/say-hello.test.ts`**

```typescript
import { sayHello } from "../src/say-hello";

describe("sayHello", () => {
  it("should return hello dzaru", () => {
    expect(sayHello("Dzaru")).toBe("Hello Dzaru");
  });
});
```

```bash
npm test
```

```text
> typescript-basic@1.0.0 test
> jest

PASS tests/say-hello.test.ts
PASS tests/hello.test.ts

Test Suites: 2 passed, 2 total
Tests:       2 passed, 2 total
```

---

## 🛠️ Compiling TypeScript

- TypeScript code cannot run directly — it must first be **compiled** into JavaScript
- Compile with `npx tsc`
- By default, compiled output is saved next to the original `.ts` file
- Most TypeScript projects separate compiled output into its own folder, typically `dist` (distribution)
- Change the output location via `tsconfig.json`

---

## 📂 Include and Exclude

By default, TypeScript tries to compile every `.ts` file it finds. Usually you only want to compile source code — not unit tests.

> **Gotcha:** Without an explicit `include`/`exclude`, `tsc` compiles every `.ts` file under the project root (aside from `node_modules`) — test files included. That's why the `tsconfig.json` below excludes `tests/**/*` explicitly.
>
> Reference: [include](https://www.typescriptlang.org/tsconfig#include) · [exclude](https://www.typescriptlang.org/tsconfig#exclude)

```json
{
  "include": ["src/**/*"],
  "exclude": ["tests/**/*"],
  "compilerOptions": {
    "rootDir": "./src",
    "outDir": "./dist",

    // Environment Settings
    // See also https://aka.ms/tsconfig/module
    "module": "es6",
    "target": "es6",
    "types": ["jest"],
    // For nodejs:
    // "lib": ["esnext"],
    // "types": ["node"],
    // and npm install -D @types/node

    // Other Outputs
    "sourceMap": true,
    "declaration": true,
    "declarationMap": true,

    // Stricter Typechecking Options
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true,

    // Recommended Options
    "strict": true,
    "jsx": "react-jsx",
    "verbatimModuleSyntax": true,
    "isolatedModules": true,
    "noUncheckedSideEffectImports": true,
    "moduleDetection": "force",
    "skipLibCheck": true
  }
}
```

---

## 🔢 Primitive Data Types & Variables

### Primitive Data Types

TypeScript reuses JavaScript's data types, so `string`, `number`, and `boolean` are supported automatically.

| Primitive Type | Description |
| --- | --- |
| `number` | JavaScript's Number |
| `boolean` | JavaScript's Boolean |
| `string` | JavaScript's String |

### Variable Declaration

> **Key Insight:** Because TypeScript is strongly typed, every variable's type must be determined at declaration time — and once set, it cannot silently become a different type.

- TypeScript can infer a variable's type automatically, or you can declare it explicitly:

```typescript
const variableName: dataType = value;
```

**`basic-typescript/tests/data-type.test.ts`**

```typescript
describe("Data Type", function () {
  it("should must declare", function () {
    const name: string = "Dzaru Rizky Fathan Fortuna";
    const balance: number = 100_000_000;
    const isVip: boolean = true;

    console.info(name);
    console.info(balance);
    console.info(isVip);
  });
});
```

### Babel and TypeScript

- Unit tests run through Jest and Babel
- Many TypeScript developers complain that development feels slower because code must be compiled first
- To work around this, `@babel/preset-typescript` compiles by **stripping out** all TypeScript syntax and emitting plain JavaScript — nothing is actually type-checked
- As a result, type errors that *should* fail your unit tests sometimes slip through, because Babel simply erases the TypeScript annotations
- You must still run `npx tsc` regularly to catch real type errors

> **Gotcha:** `npm test` alone cannot catch type errors — Babel only strips annotations like `: string` and `: number` without validating them. A build is only safe once `npx tsc` has also run clean; see [Best Practices](#-best-practices).

**`basic-typescript/tests/data-type.test.ts`**

```typescript
describe("Data Type", function () {
  it("should must declare", function () {
    let name: string = "Dzaru Rizky Fathan Fortuna";
    let balance: number = 100_000_000;
    let isVip: boolean = true;

    console.info(name);
    console.info(balance);
    console.info(isVip);

    name = 100;
    balance = "Dzaru";
    isVip = 100;
  });
});
```

```bash
npx tsc
```

```text
tests/data-type.test.ts:11:5 - error TS2322: Type 'number' is not assignable to type 'string'.
tests/data-type.test.ts:12:5 - error TS2322: Type 'string' is not assignable to type 'number'.
tests/data-type.test.ts:13:5 - error TS2322: Type 'number' is not assignable to type 'boolean'.

Found 3 errors in the same file, starting at: tests/data-type.test.ts:11
```

```bash
npm run test
```

```text
PASS dist/tests/say-hello.test.js
PASS dist/tests/hello.test.js
PASS dist/tests/data-type.test.js
PASS tests/data-type.test.ts
PASS tests/say-hello.test.ts
PASS tests/hello.test.ts

Test Suites: 6 passed, 6 total
Tests:       6 passed, 6 total
Snapshots:   0 total
```

`tsc` in watch mode reports the same three errors, and clears them once the invalid assignments are removed:

```text
[04:56:07 PM] File change detected. Starting incremental compilation...

tests/data-type.test.ts:11:5 - error TS2322: Type 'number' is not assignable to type 'string'.
tests/data-type.test.ts:12:5 - error TS2322: Type 'string' is not assignable to type 'number'.
tests/data-type.test.ts:13:5 - error TS2322: Type 'number' is not assignable to type 'boolean'.

Found 3 errors. Watching for file changes.

[04:57:46 PM] File change detected. Starting incremental compilation...
[04:57:47 PM] Found 0 errors. Watching for file changes.
```

---

## 📦 Array Types

Arrays carry over from JavaScript unchanged. What TypeScript adds is a declaration of what lives *inside* the array — and, for a tuple, how fixed its shape is.

### Array

Array types work the same way as in JavaScript. TypeScript supports two syntaxes: `DataType[]` or `Array<DataType>`.

```typescript
it("should same with javasript", function () {
  const names: string[] = ["Dzaru", "Rizky", "Fathan", "Fortuna"];
  const values: number[] = [1, 2, 3];

  console.info(names);
  console.info(values);
});
```

### Read-Only Array

Arrays can be made immutable using `ReadonlyArray<TipeData>`.

```typescript
it("should support readonly array", function () {
  const hobbies: ReadonlyArray<string> = ["Read", "Write"];
  console.info(hobbies);

  // hobbies[0] = "Playing Game"; // ❌ not allowed
});
```

> **Tip:** Reach for `ReadonlyArray` on function parameters and returned data you don't want the caller mutating. It's a compile-time guarantee only, though — an [`as` type assertion](#type-assertions) can still strip it back to a mutable array at runtime.

### Tuple

A tuple is an array type where both the length and the type at each index are fixed. A tuple can also be marked `readonly` to make it immutable.

```typescript
it("should support tuple", function () {
  const person: readonly [string, string, number] = ["Dzaru", "Rizky", 23];
  console.info(person);

  console.info(person[0]);
  console.info(person[1]);
  console.info(person[2]);
});
```

> **Note:** Unlike a plain `Array`, a tuple's length is fixed and each index carries its own type — `person[2]` is guaranteed `number`, never `string`. Marking it `readonly` (as above) blocks both reassigning an element and pushing a new one.

---

## 🎭 Any & Union Types

Both exist for values that aren't a single fixed type — but they differ completely in what they give up. `any` abandons checking entirely; a union type keeps it, listing exactly which types are allowed.

### Any

> **Key Insight:** `any` opts a value out of the type checker entirely. It exists for interop with untyped JavaScript, not as a default choice.

- Ideally, every value in TypeScript has a declared type
- For example, a plain JavaScript object usually should have its attributes declared explicitly — something JavaScript doesn't require
- When you genuinely need JavaScript-style freedom, use the `any` type
- `any` tells TypeScript to skip all checking on that value

**`basic-typescript/tests/any.test.ts`**

```typescript
describe("Any", function () {
  it("should support in typescript", function () {
    const person: any = {
      id: 1,
      name: "Dzaru Rizky",
      age: 22,
    };

    person.age = 23;
    person.address = "Indonesia";

    console.info(person);
  });
});
```

### Union Type

JavaScript lets a single variable hold different data types over its lifetime. TypeScript forbids this by default, treating it as bad practice — but when you genuinely need a variable that can change shape, declare it with a union type. TypeScript then allows reassignment, as long as the new value matches one of the declared types.

**`basic-typescript/tests/union.test.ts`**

```typescript
it("should support in typescript", function () {
  let sample: number | string | boolean = "Dzaru";
  console.info(sample);

  sample = 100;
  console.info(sample);

  sample = true;
  console.info(sample);
});
```

### Narrowing a Union Type

Calling a method on a union-typed variable requires care, since its runtime type can vary. Narrow it first with `typeof`.

**`basic-typescript/tests/union.test.ts`**

```typescript
it("should support typeof operator", function () {
  function process(value: number | string | boolean) {
    if (typeof value === "string") {
      return value.toUpperCase();
    } else if (typeof value === "number") {
      return value + 2;
    } else {
      return value;
    }
  }

  expect(process("Dzaru")).toBe("DZARU");
  expect(process(100)).toBe(102);
  expect(process(true)).toBe(true);
});
```

> **Tip:** `typeof` narrowing only covers primitives (`string`, `number`, `boolean`, etc.). Narrowing objects or arrays instead relies on `instanceof` checks, discriminated union tags, or a user-defined type guard.

---

## 🏷️ Type Alias & Object Types

Once a value is an object rather than a primitive, its shape needs declaring — either inline on the variable itself, or once as a named alias that can be reused everywhere.

### Type Alias

- Using `any` is generally **not recommended**
- Reach for `any` only when working with data from a library you can't change, or when the shape is genuinely unpredictable
- When you define your own JavaScript object shape, create a **type alias** for its structure instead

**`basic-typescript/src/type-alias.ts`**

```typescript
export type ID = string | number;

export type Category = {
  id: ID;
  name: string;
};

export type Product = {
  id: ID;
  name: string;
  price: number;
  category: Category;
};
```

> **Note:** A `type` alias and an `interface` overlap heavily for object shapes. This guide uses `type` for unions and simple shapes, and reaches for `interface` (see [Interface](#-interface)) whenever a shape is expected to grow later via `extends`.

### Type Alias for Union Types

A type alias can also name a union type.

**`basic-typescript/tests/type-alias.test.ts`**

```typescript
import type { Category, Product } from "../src/type-alias";

describe("Type Alias", function () {
  it("should support in typescript", function () {
    const category: Category = {
      id: "1",
      name: "Handphone",
    };

    const product: Product = {
      id: 1,
      name: "iPhone 14 Pro Max",
      price: 20_000_000,
      category: category,
    };

    console.info(category);
    console.info(product);
  });
});
```

### Object Type

For simple cases, declaring a full type alias can feel like overkill — you can inline an object type directly on the variable declaration instead.

**`basic-typescript/tests/object.test.ts`**

```typescript
describe("Object", function () {
  it("should support in typescript", function () {
    const person: { id: string; name: string } = {
      id: "1",
      name: "Dzaru Rizky Fathan Fortuna",
    };

    console.info(person);

    person.id = "2";
    person.name = "Dzaru Rizky F. F";

    console.info(person);
  });
});
```

### Optional Properties

By default, every attribute declared on an object or type is required. When an attribute isn't always required, mark it optional with `?`.

**`basic-typescript/src/type-alias.ts`**

```typescript
export type ID = string | number;

export type Category = {
  id: ID;
  name: string;
  description?: string;
};

export type Product = {
  id: ID;
  name: string;
  price: number;
  category: Category;
};
```

> **Note:** `description?: string` is shorthand for `description: string | undefined` — reading an optional property that wasn't provided returns `undefined`, so guard it the same way covered in [Null and Undefined](#null-and-undefined).

---

## 🔀 Enum, Null & Undefined

Cases where TypeScript expresses something JavaScript cannot: a fixed set of allowed values, and the difference between a value that is merely absent and one that is explicitly empty.

### Enum

TypeScript's `enum` type represents a fixed set of possible values — a type JavaScript doesn't have natively. By default, an enum's members compile to strings in JavaScript, though they can also be numeric.

**`basic-typescript/src/enum.ts`**

```typescript
export enum CustomerType {
  REGULAR = "REGULAR",
  GOLD = "GOLD",
  PLATINUM = "PLATINUM",
}

export type Customer = {
  id: number;
  name: string;
  type: CustomerType;
};
```

**`basic-typescript/tests/enum.test.ts`**

```typescript
import { CustomerType, type Customer } from "../src/enum";

describe("Enum", function () {
  it("should support in typescript", function () {
    const customer: Customer = {
      id: 1,
      name: "Dzaru",
      type: CustomerType.REGULAR,
    };

    console.info(customer);
  });
});
```

> **Note:** These enum members are declared as strings (`REGULAR = "REGULAR"`), which is why they read cleanly in logs and JSON. Omit the initializer (`enum X { A, B }`) and TypeScript falls back to auto-incrementing numbers starting at `0` instead.

### Null and Undefined

Marking a variable or parameter with `?` automatically allows `undefined`. When you also need to allow `null` explicitly, declare it with the `null` type.

**`basic-typescript/tests/undefined.test.ts`**

```typescript
import { sayHello } from "../src/say-hello";

describe("Optional parameter", function () {
  it("should support null and undefined", function () {
    function sayHello(name?: string) {
      if (name) {
        console.info(`Hello ${name}`);
      } else {
        console.info("Hello");
      }
    }

    const name: string | undefined = undefined;
    sayHello("Dzaru");
    sayHello(name);
  });
});
```

---

## 🧩 Interface

> **Key Insight:** `interface` and `type` overlap heavily, but interfaces are easier to extend incrementally. That's why most TypeScript developers reach for `interface` on complex, evolving data shapes.

### Type Alias vs Interface

For a plain data shape the two are interchangeable. The differences only surface once that shape has to grow:

| | `type` | `interface` |
| --- | --- | --- |
| Object shapes | Yes | Yes |
| Unions (`number \| string`) | Yes | No |
| Combine with another shape | `&` — an [intersection type](#intersection-types) | `extends` |
| Re-opened by a later declaration | No | Yes — same-name interfaces merge |
| Best for | Unions, primitives, one-off shapes | Evolving object and function contracts |

### Readonly Properties

A property can be made `readonly`, meaning it can never be reassigned after creation.

**`basic-typescript/src/seller.ts`**

```typescript
export interface Seller {
  id: number;
  name: string;
  address?: string;
  readonly nib?: string;
  readonly npwp?: string;
}
```

**`basic-typescript/tests/interface.test.ts`**

```typescript
import type { Seller } from "../src/seller";

describe("Interface", function () {
  it("should support in typescript", function () {
    const seller: Seller = {
      id: 1,
      name: "Toko ABC",
    };

    console.info(seller);
  });
});
```

### Function Interface

TypeScript also lets you declare a function's shape as an interface, so any variable holding that function can be typed against it.

```typescript
it("should support fuction interface", function () {
  interface AddFunction {
    (value1: number, value2: number): number;
  }

  const add: AddFunction = (value1, value2) => {
    return value1 + value2;
  };

  expect(add(2, 2)).toBe(4);
});
```

### Indexable Interface

An interface can describe a type indexed by number or string — the same shape as an `Array` or plain `Object`.

```typescript
it("should support indexable interface", function () {
  interface StringArray {
    [index: number]: string;
  }

  const names: StringArray = ["Dzaru", "Rizky", "Fathan", "Fortuna"];
  console.info(names);
});

it("should support indexable interface for non number index", function () {
  interface StringDictionary {
    [key: string]: string;
  }

  const dictionary: StringDictionary = {
    name: "Dzaru",
    address: "Indonesia",
  };

  expect(dictionary["name"]).toBe("Dzaru");
  expect(dictionary["address"]).toBe("Indonesia");
});
```

### Extending Interface

An interface can `extend` another interface. The extending interface automatically inherits every attribute of the base interface, which makes building complex types much easier.

**`basic-typescript/src/employee.ts`**

```typescript
export interface Employee {
  id: string;
  name: string;
  division: string;
}

export interface Manager extends Employee {
  numberOfEmployees: number;
}
```

**`basic-typescript/tests/interface.test.ts`**

```typescript
it("should support extends interface", function () {
  const employee: Employee = {
    id: "1",
    name: "Dzaru",
    division: "IT",
  };

  console.info(employee);

  const manager: Manager = {
    id: "2",
    name: "Rizky",
    division: "HR",
    numberOfEmployees: 20,
  };

  console.info(manager);
});
```

> **Tip:** Unlike a class, an interface can `extends` more than one interface at once (`interface Manager extends Employee, Timestamped { ... }`), which makes composing several shapes together straightforward.

### Functions in Interface

Under the hood, an interface is implemented as a JavaScript object — and just like an object can hold a function as an attribute, so can an interface.

```typescript
it("should support function in interface", function () {
  interface Person {
    name: string;
    sayHello(name: string): string;
  }

  const person: Person = {
    name: "Dzaru",
    sayHello: function (name: string): string {
      return `Hello ${name}`;
    },
  };

  expect(person.sayHello("Dzaru")).toBe("Hello Dzaru");
});
```

---

## 🔗 Intersection Types & Type Assertions

Two ways of reshaping types you already have — one the compiler verifies, and one it simply believes.

### Intersection Types

An intersection type combines two existing types into a new one. It's especially useful when `extends` isn't an option on an interface. Combine types with the `&` operator.

```typescript
it("should support intersection types", function () {
  interface HasName {
    name: string;
  }

  interface HasId {
    id: string;
  }

  type Domain = HasId & HasName;

  const domain: Domain = {
    id: "1",
    name: "Dzaru",
  };

  console.info(domain);
});
```

> **Note:** Intersection types (`&`) and `interface extends` solve the same problem from opposite directions — reach for `&` when combining two existing `type` aliases, and `extends` when defining a new `interface` from scratch (see [Extending Interface](#extending-interface)).

### Type Assertions

Sometimes you know a value's real type even though TypeScript doesn't — typically when working with JavaScript code that returns `any`. In that case, convert the value to the type you want using the `as` keyword. This is called a **type assertion**.

> ⚠️ **Warning:** A type assertion only affects the compiler's view of the value — it performs no runtime check. Asserting the wrong shape compiles cleanly and fails at runtime instead.

```typescript
it("should support type assertions", function () {
  const person: any = {
    name: "Dzaru",
    age: 23,
  };

  const person2: Person = person as Person;
  person2.sayHello("Dzaru");

  console.info(person2);
});
```

```text
TypeError: person2.sayHello is not a function

  104 |
  105 |     const person2: Person = person as Person;
> 106 |     person2.sayHello("Dzaru")
      |             ^
  107 |
  108 |     console.info(person2);
  109 |   })

  at Object.sayHello (tests/interface.test.ts:106:13)
```

---

## ⚙️ Functions

Functions are where most annotations end up: every parameter and every return value is a contract the compiler can check at each call site.

### Function Declaration

Function declarations mirror JavaScript closely. The key difference is that TypeScript requires a type on every parameter and on the return value. When a function returns nothing, use `void` — or omit the return type entirely, just like in JavaScript.

**`basic-typescript/tests/function.test.ts`**

```typescript
describe("Function", function () {
  it("should support in typescript", function () {
    function sayHello(name: string): string {
      return `Hello ${name}`;
    }

    expect(sayHello("Dzaru")).toBe("Hello Dzaru");

    function printHello(name: string): void {
      console.info(`Hello ${name}`);
    }

    printHello("Dzaru");
  });
});
```

### Function Parameters

Just like JavaScript, a TypeScript function supports multiple parameters, rest parameters (variable arguments), and default values. The key difference from JavaScript: every parameter is required by default, unless explicitly marked optional with `?`.

**`basic-typescript/tests/function.test.ts`**

```typescript
it("should support default value", function () {
  function sayHello(name: string = "Guest"): string {
    return `Hello ${name}`;
  }

  expect(sayHello("Dzaru")).toBe("Hello Dzaru");
  expect(sayHello()).toBe("Hello Guest");
});

it("should support rest parameter", function () {
  function sum(...values: number[]): number {
    let total = 0;

    for (const value of values) {
      total += value;
    }

    return total;
  }

  expect(sum(1, 2, 3, 4, 5)).toBe(15);
  expect(sum(10, 20, 30)).toBe(60);
  expect(sum()).toBe(0);
});

it("should suppport optional parameter", function () {
  function sayHello(firstName?: string, lastName?: string): string {
    if (firstName && lastName) {
      return `Hello ${firstName} ${lastName}`;
    } else if (firstName) {
      return `Hello ${firstName}`;
    } else {
      return "Hello Guest";
    }
  }

  expect(sayHello("Dzaru", "Rizky")).toBe("Hello Dzaru Rizky");
  expect(sayHello("Dzaru")).toBe("Hello Dzaru");
  expect(sayHello()).toBe("Hello Guest");
});
```

> **Note:** Just like JavaScript, a required parameter can't follow an optional or default one — `function f(a?: string, b: string)` is a compile error. A rest parameter must always come last in the list.

### Function Overloading

Function overloading lets you declare a function with the same name but different parameter signatures. In JavaScript, writing one function that accepts different input shapes and returns different output shapes is common — but this can make a function unsafe, since its output type becomes unpredictable. TypeScript's function overloading makes this pattern safer.

**`basic-typescript/tests/function.test.ts`**

```typescript
it("should support function overloading", function () {
  function callMe(value: number): number;
  function callMe(value: string): string;
  function callMe(value: any): any {
    if (typeof value === "number") {
      return value * 10;
    } else if (typeof value === "string") {
      return value;
    }
  }

  expect(callMe(10)).toBe(100);
  expect(callMe("dzaru")).toBe("dzaru");
});
```

> **Note:** Only the overload signatures (`function callMe(value: number): number;` etc.) are visible to callers — the implementation signature (`function callMe(value: any): any`) is erased along with every other type annotation, the same way covered in the [Introduction](#-introduction).

### Function as Parameter

Just like in JavaScript, TypeScript functions can be passed as parameters — most commonly for callbacks. TypeScript needs to know a function parameter's own signature: either via a function interface, or by declaring the parameter count and return type inline.

**`basic-typescript/tests/function.test.ts`**

```typescript
it("should function as parameter", function () {
  function sayHello(name: string, filter: (name: string) => string): string {
    return filter(name);
  }

  function toUpper(name: string): string {
    return name.toUpperCase();
  }

  expect(sayHello("Dzaru", toUpper)).toBe("DZARU");
  expect(sayHello("Dzaru", (name: string): string => name.toLowerCase())).toBe(
    "dzaru"
  );
});
```

---

## 🔁 Control Flow & Loops

Control flow is pure JavaScript — none of the syntax below is new. What TypeScript adds is invisible: it follows each branch and narrows types as conditions are tested, which is what makes [narrowing a union type](#narrowing-a-union-type) work.

### If Statement

`if` statements in TypeScript work exactly like in JavaScript.

**`basic-typescript/tests/if.test.ts`**

```typescript
describe("If statement", function () {
  it("should support in typescript", function () {
    const examValue = 90;

    if (examValue >= 90) {
      console.info("A");
    } else if (examValue >= 80) {
      console.info("B");
    } else if (examValue >= 70) {
      console.info("C");
    } else {
      console.info("D");
    }
  });
});
```

### Ternary Operator

The ternary operator works the same as in JavaScript.

**`basic-typescript/tests/if.test.ts`**

```typescript
it("should support ternary operator", function () {
  const value = 80;
  const say = value >= 75 ? "Congratulation" : "Try Again";
  console.info(say);
});
```

### Switch Statement

`switch` statements also work the same as in JavaScript.

**`basic-typescript/tests/if.test.ts`**

```typescript
it("should support switch statement", function () {
  function getHello(name: string): string {
    switch (name) {
      case "Dzaru":
        return "Hello Dzaru";
      case "Rizky":
        return "Hello Rizky";
      case "Fathan":
        return "Hello Fathan";
      case "Fortuna":
        return "Hello Fortuna";
      default:
        return "Hello Guest";
    }
  }

  getHello("Dzaru");
  getHello("Rizky");
  getHello("Fathan");
  getHello("Fortuna");
  getHello("Test");
});
```

### For Loop

`for` loops behave exactly like in JavaScript. TypeScript supports the classic `for`, `for...in`, and `for...of`.

```typescript
describe("Loop", function () {
  it("should support for loop", function () {
    const names: string[] = ["Dzaru", "Rizky", "Fathan", "Fortuna"];

    for (let i = 0; i < names.length; i++) {
      console.info(names[i]);
    }

    for (const name of names) {
      console.info(name);
    }

    for (const index in names) {
      console.info(names[index]);
    }
  });
});
```

### While Loop

`while` loops work the same as in JavaScript.

**`basic-typescript/tests/loop.test.ts`**

```typescript
it("should support while loop", function () {
  let counter: number = 0;

  while (counter < 10) {
    console.info(counter);
    counter++;
  }
});
```

### Do While Loop

`do...while` loops are also fully supported.

**`basic-typescript/tests/loop.test.ts`**

```typescript
it("should support do while loop", function () {
  let counter: number = 0;
  do {
    console.info(counter);
    counter++;
  } while (counter < 10);
});
```

### Break and Continue

Just like in JavaScript, `break` and `continue` are commonly used inside `while` and `do...while` loops — and they work identically in TypeScript.

```typescript
it("should support break and continue", function () {
  let counter = 0;
  do {
    counter++;

    if (counter === 10) {
      break;
    }

    if (counter % 2 === 0) {
      continue;
    }
    console.info(counter);
  } while (true);
});
```

---

## 🌐 JavaScript Features in TypeScript

Every feature covered in a JavaScript course also works in TypeScript — arithmetic, comparison, and logical operators, template strings, optional chaining, `with` statements, default parameters, generator functions, getters/setters, destructuring, modules, and the standard library all carry over.

The one difference: because TypeScript is strongly typed, every variable and parameter needs a declared type. If you want JavaScript-style flexibility — a variable or parameter that accepts any type — declare it as `any`.

---

## 🔄 Compilation Flow

Knowing what `tsc` actually does — and what it deliberately does not — is what makes the rest of this guide click. A project like this one has **two** pipelines, and only one of them checks types:

```text
src/*.ts                          the code you write
  ↓
── Type checking ───────────────   npx tsc
  ↓
Parse + bind                      names, scopes, imports
  ↓
Type inference                    the type of every expression you didn't annotate
  ↓
Type check                        assignability, narrowing, overload resolution
  ↓                               errors are reported here — and only here
Type erasure                      annotations, interfaces and type aliases removed
  ↓
Emit                              dist/*.js (+ .d.ts, .js.map) per tsconfig.json
  ↓
Node.js / the browser runs plain JavaScript


tests/*.test.ts                   the Jest pipeline, in parallel
  ↓
babel-jest + @babel/preset-typescript
  ↓
Type erasure                      annotations stripped, nothing checked
  ↓
Jest runs the JavaScript          tests can pass while tsc still reports errors
```

That split explains the behaviour that surprises people throughout this guide:

| Question | Answer |
| --- | --- |
| Why do my tests pass while `tsc` reports errors? | Jest compiles through Babel, which erases annotations without validating them — only `tsc` type-checks |
| Why is there no type information at runtime? | Every type is erased before emit, so `typeof` sees JavaScript values, never your `interface` |
| Why does a [type assertion](#type-assertions) fail at runtime? | `as` changes what the checker believes, and nothing survives to runtime to verify it |
| Why does an `enum` exist at runtime when an `interface` doesn't? | `enum` emits a real JavaScript object; `interface` and `type` emit nothing at all |
| Why does an optional parameter need a guard? | `?` widens the type to include `undefined`, and `strict` mode forces you to handle it |
| Why was my new `.ts` file never compiled? | [`include` and `exclude`](#-include-and-exclude) in `tsconfig.json` decide what `tsc` even looks at |

---

## 🎯 Quick Reference

| Concept | Purpose | Key Syntax |
| --- | --- | --- |
| **Primitive Types** | Basic values reused from JavaScript | `string`, `number`, `boolean` |
| **Array** | Ordered list of a single type | `string[]` or `Array<string>` |
| **ReadonlyArray** | Immutable array | `ReadonlyArray<string>` |
| **Tuple** | Fixed-length array with per-index types | `[string, number]` |
| **Any** | Opt out of type checking | `any` |
| **Union Type** | Variable that accepts multiple types | `number \| string` |
| **Type Alias** | Name a custom (often object) type | `type Product = { ... }` |
| **Object Type** | Inline object shape | `{ id: string; name: string }` |
| **Optional Property** | Attribute that may be omitted | `description?: string` |
| **Enum** | Fixed set of named values | `enum Status { ACTIVE, INACTIVE }` |
| **Interface** | Extensible object/function contract | `interface Seller { ... }` |
| **Intersection Type** | Combine multiple types into one | `type Domain = HasId & HasName` |
| **Type Assertion** | Force TypeScript to trust a type | `value as Person` |
| **Readonly Property** | Attribute that can't be reassigned | `readonly nib?: string` |
| **Narrowing** | Make a union safe to use | `if (typeof value === "string")` |
| **Function Interface** | Type the shape of a function | `interface Add { (a: number, b: number): number }` |
| **Indexable Interface** | Type an array- or dictionary-like shape | `[key: string]: string` |
| **Function Overloading** | Multiple signatures, one implementation | `function callMe(value: number): number;` |
| **Optional Parameter** | Argument that may be omitted | `function f(name?: string)` |
| **Default Parameter** | Argument with a fallback value | `function f(name: string = "Guest")` |
| **Rest Parameter** | Variable number of arguments | `function sum(...values: number[])` |
| **Callback Parameter** | Pass a function into a function | `filter: (name: string) => string` |
| **Void** | Function that returns nothing | `function printHello(name: string): void` |

---

## 💡 Best Practices

**✅ Do This**

- **Run `tsc` regularly** — Babel strips TypeScript syntax without checking it, so `npm test` alone can hide real type errors
- **Keep `"strict": true` in `tsconfig.json`** — it turns on `strictNullChecks`, which is what forces `null` and `undefined` to be handled explicitly
- **Scope compilation with `include` and `exclude`** so `tsc` compiles `src/` rather than every `.ts` file it can find
- **Let inference do the obvious work**, and annotate deliberately where it isn't obvious — function parameters and return types especially
- **Prefer `interface` for evolving object shapes** — easier to extend later than a `type`
- **Use `type` for unions, primitives and one-off shapes** — an `interface` cannot express `number | string`
- **Mark truly optional data with `?`** — don't default everything to optional just to avoid type errors
- **Use `readonly` and `ReadonlyArray`** for values that should never be reassigned after creation
- **Narrow union types with `typeof`** before calling type-specific methods
- **Give exported functions an explicit return type** — the error then surfaces at the function, not at every call site
- **Reach for function overloading** when a function genuinely takes different input shapes, so callers keep a precise return type

**❌ Avoid This**

- **Defaulting to `any`** — it silently disables the type checker; reserve it for untyped third-party data
- **Trusting a type assertion (`as`) blindly** — it isn't a runtime check, so an incorrect assertion still compiles and only fails when the code actually runs
- **Using `as` to silence an error you don't understand** — narrow the value or fix the type instead
- **Skipping `tsc` because tests pass** — Babel-compiled tests can pass even with real type errors present
- **Expecting types to exist at runtime** — every annotation is erased before the JavaScript is emitted
- **Declaring a required parameter after an optional one** — it's a compile error, exactly as it would be in JavaScript
- **Reaching for a numeric `enum` when the values get logged or serialized** — string enums stay readable in JSON
- **Repeating the same object shape inline in several places** — name it once as a `type` or an `interface`
- **Assuming a `readonly` array is immutable at runtime** — the guarantee disappears the moment it's asserted back to a mutable one

> Reference: [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html) · [tsconfig reference](https://www.typescriptlang.org/tsconfig) · [Jest with TypeScript](https://jestjs.io/docs/getting-started#using-typescript)
