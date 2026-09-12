# 🧩 TypeScript Generic

A practical reference guide for generics in TypeScript — covering generic classes, generic functions, constraints, defaults, and the built-in generic collection types.

---

## 📋 Table of Contents

- [Introduction](#-introduction)
  - [What is Generic?](#what-is-generic)
  - [Naming Conventions](#naming-conventions)
- [Project Setup](#-project-setup)
  - [Creating the Project](#creating-the-project)
  - [Adding Jest for Unit Testing](#adding-jest-for-unit-testing)
  - [Adding Babel](#adding-babel)
  - [Adding TypeScript](#adding-typescript)
  - [Setting Up the TypeScript Project](#setting-up-the-typescript-project)
  - [Setting Up TypeScript for Jest](#setting-up-typescript-for-jest)
- [Without Generic](#-without-generic)
- [Generic Class](#-generic-class)
- [Generic Function](#-generic-function)
- [Multiple Generic Types](#-multiple-generic-types)
- [Optional Generic Type](#-optional-generic-type)
- [Generic Parameter Default](#-generic-parameter-default)
- [Generic Constraint](#-generic-constraint)
- [Generic Collection](#-generic-collection)
  - [Array\<T\>](#arrayt)
  - [Set\<T\>](#sett)
  - [Map\<K, V\>](#mapk-v)
- [Generic Promise](#-generic-promise)
- [Type Argument Resolution](#-type-argument-resolution)
- [Quick Reference](#-quick-reference)
- [Best Practices](#-best-practices)

---

## 🎯 Introduction

### What is Generic?

- Generic is a feature that lets us write the same code once and reuse it with different data types
- Before generics existed, whenever a variable or parameter needed to accept different data types, we reached for `any`
- With generics, the data type can change per usage while still being checked — so we get flexibility without giving up the safety that `any` throws away

> **Key Insight:** Generics let TypeScript defer the decision about a concrete type until the code is actually used, while still type-checking it at compile time — unlike `any`, which opts a value out of type checking entirely.

### Naming Conventions

Single-letter names are a convention, not a rule — but following them makes generic code readable at a glance:

| Parameter | Conventional meaning |
| --- | --- |
| `T` | Type — the default, for a single unnamed type |
| `K` | Key — as in `Map<K, V>` |
| `V` | Value — the counterpart to `K` |
| `E` | Element — the item type of a collection |
| `R` | Return — the result type of a function |

> **Tip:** Nothing stops you from writing `<TData>` or `<TEmployee>`, and that's often clearer once a class carries several parameters. What matters is that a type parameter reads as a *type*, never as a value.

---

## 🏗️ Project Setup

### Creating the Project

```bash
mkdir belajar-typescript-generic
cd belajar-typescript-generic
npm init
```

- Open `package.json` and add `"type": "module"`

### Adding Jest for Unit Testing

```bash
npm install --save-dev jest @types/jest
```

> Reference: [npmjs.com/package/jest](https://www.npmjs.com/package/jest)

### Adding Babel

```bash
npm install --save-dev babel-jest @babel/preset-env
```

> Reference: [babeljs.io/setup#installation](https://babeljs.io/setup#installation)

### Adding TypeScript

```bash
npm install --save-dev typescript
```

> Reference: [npmjs.com/package/typescript](https://www.npmjs.com/package/typescript)

### Setting Up the TypeScript Project

```bash
npx tsc --init
```

- All compiler configuration is generated into `tsconfig.json`
- Change `"module"` from `"commonjs"` to `"ES6"`

### Setting Up TypeScript for Jest

```bash
npm install --save-dev @babel/preset-typescript
npm install --save-dev @jest/globals
```

> Reference: [jestjs.io/docs/getting-started#using-typescript](https://jestjs.io/docs/getting-started#using-typescript)

**`generic-typescript/package.json`**

```json
{
  "name": "generic-typescript",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "test": "jest"
  },
  "jest": {
    "transform": {
      "^.+\\.[t|j]sx?$": "babel-jest"
    }
  },
  "author": "Dzaru Rizky Fathan Fortuna",
  "license": "ISC",
  "description": "",
  "devDependencies": {
    "@babel/preset-env": "^7.29.7",
    "@babel/preset-typescript": "^7.29.7",
    "@jest/globals": "^30.4.1",
    "@types/jest": "^30.0.0",
    "babel-jest": "^30.4.1",
    "jest": "^30.4.2",
    "typescript": "^6.0.3"
  }
}
```

**`generic-typescript/babel.config.json`**

```json
{
  "presets": ["@babel/preset-env", "@babel/preset-typescript"]
}
```

**`generic-typescript/tsconfig.json`**

```json
{
  // Visit https://aka.ms/tsconfig to read more about this file
  "compilerOptions": {
    // File Layout
    // "rootDir": "./src",
    // "outDir": "./dist",

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

    // Style Options
    // "noImplicitReturns": true,
    // "noImplicitOverride": true,
    // "noUnusedLocals": true,
    // "noUnusedParameters": true,
    // "noFallthroughCasesInSwitch": true,
    // "noPropertyAccessFromIndexSignature": true,

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

> **Key Insight:** This project's Jest transform is `babel-jest`, which only strips TypeScript's types away — it never checks them. That means a type error can slip past `npm test` completely and only surface as a runtime crash. Run `npx tsc --noEmit` (or use `ts-jest` instead of `babel-jest`) whenever you actually want type errors caught before runtime.

---

## 🚫 Without Generic

Without generics, a class meant to hold values of varying types has to fall back to `any`. That buys flexibility, but it throws away every compile-time guarantee TypeScript normally gives you — mismatched usage only blows up once the code actually runs.

**`generic-typescript/test/nogeneric.test.ts`**

```typescript
describe("no generic", () => {
  class Data {
    value: any;
    constructor(value: any) {
      this.value = value;
    }
  }

  it("should accept all values", async () => {
    const data = new Data("Dzaru");
    console.info(data.value.toUpperCase());

    data.value = 100;
    // console.info(data.value.toUpperCase());
  });
});
```

- `value: any` accepts a `string` on construction, then a `number` on reassignment — TypeScript raises no complaint either time
- The second `console.info` call is left commented out on purpose: `100` has no `toUpperCase` method, so calling it would throw `TypeError: data.value.toUpperCase is not a function` at runtime, with no compile-time warning beforehand

All three approaches accept any value; what separates them is how much the checker remembers afterwards:

| | `any` | `unknown` | Generic `<T>` |
| --- | --- | --- | --- |
| Accepts any value | Yes | Yes | Yes |
| Checked when used | No — anything compiles | Only after narrowing | Yes — as the concrete type |
| Remembers what went in | No | No | Yes |
| Mistakes surface | At runtime | At compile time | At compile time |

> **Key Insight:** `any` and a generic parameter both accept every type — the difference is *memory*. `any` forgets what it was handed, so `data.value.toUpperCase()` compiles no matter what is inside. `T` remembers: once `GenericData<string>` exists, the checker knows `value` is a `string` everywhere it's touched.

---

## 🧱 Generic Class

A generic type parameter can be added when declaring a class, using `<>` (angle brackets) right after the class name to name the generic type. That type parameter can then be used throughout the class, and gets swapped for a concrete type whenever an object is created from the class. When constructing an object from a generic class, you're expected to specify which concrete type should replace the generic parameter.

**`generic-typescript/test/generic.test.ts`**

```typescript
describe("generic", () => {
  class GenericData<T> {
    value: T;
    constructor(value: T) {
      this.value = value;
    }
  }

  it("should support multiple data type", async () => {
    const dataNumber = new GenericData<number>(1);
    const dataString = new GenericData<string>("Dzaru");
    const dataBoolean = new GenericData<boolean>(true);

    expect(dataNumber.value).toBe(1);
    expect(dataString.value).toBe("Dzaru");
    expect(dataBoolean.value).toBe(true);
  });
});
```

> **Note:** `T` is a placeholder, not a real type — it exists only while the checker runs. `GenericData<number>` and `GenericData<string>` compile to the exact same JavaScript class, because the type argument is erased along with every other annotation.

> **Tip:** Generic parameters aren't limited to classes. The same `<T>` syntax works on interfaces and type aliases too — `type Box<T> = { value: T }` and `interface Repository<T> { findAll(): T[] }` are both valid.
>
> Reference: [typescriptlang.org — Generics](https://www.typescriptlang.org/docs/handbook/2/generics.html)

---

## 🔧 Generic Function

A generic type parameter declared on a class is available throughout that class. But sometimes there's no class at all — just a plain function. Generics work on functions too, using the very same `<>` syntax right after the function name.

**`generic-typescript/test/generic.test.ts`**

```typescript
describe("generic", () => {
  class GenericData<T> {
    value: T;
    constructor(value: T) {
      this.value = value;
    }

    get(): T {
      return this.value;
    }

    set(): T {
      return this.value;
    }
  }

  it("should support multiple data type", async () => {
    const dataNumber = new GenericData<number>(1);
    const dataString = new GenericData<string>("Dzaru");
    const dataBoolean = new GenericData<boolean>(true);

    expect(dataNumber.value).toBe(1);
    expect(dataString.value).toBe("Dzaru");
    expect(dataBoolean.value).toBe(true);
  });

  function create<T>(value: T): T {
    return value;
  }

  it("should support function generic", async () => {
    const number = create<number>(1);
    const string = create<string>("Dzaru");
    const boolean = create<boolean>(true);

    expect(number).toBe(1);
    expect(string).toBe("Dzaru");
    expect(boolean).toBe(true);
  });
});
```

> **Note:** The type arguments here are optional. `create(1)` infers `T` as `number` straight from the value, so `create<number>(1)` only spells out what the checker already worked out. Writing it explicitly pays off when there's nothing to infer from, or when you want a wider type than the one inferred.

---

## 🔢 Multiple Generic Types

More than one generic type parameter can be declared at once, on either a class or a function. Separate them with a comma inside `<>` to add more than one generic type.

**`generic-typescript/test/generic.test.ts`**

```typescript
describe("generic", () => {
  class GenericData<T> {
    value: T;
    constructor(value: T) {
      this.value = value;
    }

    get(): T {
      return this.value;
    }

    set(): T {
      return this.value;
    }
  }

  it("should support multiple data type", async () => {
    const dataNumber = new GenericData<number>(1);
    const dataString = new GenericData<string>("Dzaru");
    const dataBoolean = new GenericData<boolean>(true);

    expect(dataNumber.value).toBe(1);
    expect(dataString.value).toBe("Dzaru");
    expect(dataBoolean.value).toBe(true);
  });

  function create<T>(value: T): T {
    return value;
  }

  it("should support function generic", async () => {
    const number = create<number>(1);
    const string = create<string>("Dzaru");
    const boolean = create<boolean>(true);

    expect(number).toBe(1);
    expect(string).toBe("Dzaru");
    expect(boolean).toBe(true);
  });

  class Entry<K, V> {
    constructor(
      public key: K,
      public value: V,
    ) {}
  }

  class Triple<K, V, T> {
    constructor(
      public first: K,
      public second: V,
      public third: T,
    ) {}
  }

  it("should support multiple generic type", async () => {
    const entry = new Entry<number, string>(1, "Dzaru");
    expect(entry.key).toBe(1);
    expect(entry.value).toBe("Dzaru");

    const triple = new Triple<number, string, boolean>(1, "Dzaru", true);
    expect(triple.first).toBe(1);
    expect(triple.second).toBe("Dzaru");
    expect(triple.third).toBe(true);
  });
});
```

> **Tip:** Type arguments are matched by position, so `Entry<number, string>` sets `K = number` and `V = string`. Naming them `K` and `V` rather than `T1` and `T2` is exactly what keeps that order readable at the call site.

---

## ❓ Optional Generic Type

When a class's generic type is also used in its constructor, specifying the concrete type explicitly becomes optional — TypeScript can automatically infer it from the constructor's argument. That inference only kicks in when the generic type actually appears in the constructor; otherwise TypeScript has nothing to infer it from.

> **Key Insight:** Inference flows from *values* to types. `new Entry(1, "Dzaru")` works because `K` and `V` both appear in the constructor's parameters, so the checker can read them off the arguments. `SimpleGeneric` never mentions `T` in a constructor, so there's nothing to read — and that single difference is what separates the two examples below.

**`generic-typescript/test/generic.test.ts`**

```typescript
describe("generic", () => {
  class GenericData<T> {
    value: T;
    constructor(value: T) {
      this.value = value;
    }

    get(): T {
      return this.value;
    }

    set(): T {
      return this.value;
    }
  }

  it("should support multiple data type", async () => {
    const dataNumber = new GenericData<number>(1);
    const dataString = new GenericData<string>("Dzaru");
    const dataBoolean = new GenericData<boolean>(true);

    expect(dataNumber.value).toBe(1);
    expect(dataString.value).toBe("Dzaru");
    expect(dataBoolean.value).toBe(true);
  });

  function create<T>(value: T): T {
    return value;
  }

  it("should support function generic", async () => {
    const number = create<number>(1);
    const string = create<string>("Dzaru");
    const boolean = create<boolean>(true);

    expect(number).toBe(1);
    expect(string).toBe("Dzaru");
    expect(boolean).toBe(true);
  });

  class Entry<K, V> {
    constructor(
      public key: K,
      public value: V,
    ) {}
  }

  class Triple<K, V, T> {
    constructor(
      public first: K,
      public second: V,
      public third: T,
    ) {}
  }

  it("should support multiple generic type", async () => {
    const entry = new Entry<number, string>(1, "Dzaru");
    expect(entry.key).toBe(1);
    expect(entry.value).toBe("Dzaru");

    const triple = new Triple<number, string, boolean>(1, "Dzaru", true);
    expect(triple.first).toBe(1);
    expect(triple.second).toBe("Dzaru");
    expect(triple.third).toBe(true);
  });

  it("should support optional generic type", async () => {
    const entry = new Entry(1, "Dzaru");
    expect(entry.key).toBe(1);
    expect(entry.value).toBe("Dzaru");
  });

  class SimpleGeneric<T> {
    private value?: T;

    setValue(value: T) {
      this.value = value;
    }

    getValue(): T | undefined {
      return this.value;
    }
  }

  it("should create simple generic", async () => {
    const simpleGeneric = new SimpleGeneric();
    simpleGeneric.setValue("Dzaru");
    simpleGeneric.setValue(100);
    // expect(simpleGeneric.getValue()!.toUpperCase()).toBe("Dzaru");
  });
});
```

> **Gotcha:** `SimpleGeneric` has no constructor that uses `T`, so `new SimpleGeneric()` gives TypeScript nothing to infer a concrete type from — `T` silently falls back to `unknown`, which is why `setValue` happily accepts both `"Dzaru"` and `100` right after each other. Because `babel-jest` strips types instead of checking them (see the [Project Setup](#-project-setup) note above), that mistake isn't caught until the commented-out line actually runs, producing `TypeError: simpleGeneric.getValue(...).toUpperCase is not a function`. That's why it stays commented out here.

---

## 🎛️ Generic Parameter Default

When declaring a generic type, a default type can be specified for when no concrete type is given. Use `= Type` inside the `<>` to set it.

**`generic-typescript/test/generic.test.ts`**

```typescript
describe("generic", () => {
  // ...GenericData, create<T>, Entry<K, V>, Triple<K, V, T>, and their tests
  // stay the same as the previous section...

  class SimpleGeneric<T> {
    private value?: T;

    setValue(value: T) {
      this.value = value;
    }

    getValue(): T | undefined {
      return this.value;
    }
  }

  it("should create simple generic", async () => {
    const simpleGeneric = new SimpleGeneric();
    simpleGeneric.setValue("Dzaru");
    simpleGeneric.setValue(100);
    // expect(simpleGeneric.getValue()!.toUpperCase()).toBe("Dzaru");
  });

  class SimpleGeneric2<T = string> {
    private value?: T;

    setValue(value: T) {
      this.value = value;
    }

    getValue(): T | undefined {
      return this.value;
    }
  }

  it("should create simple generic 2", async () => {
    const simple = new SimpleGeneric2();
    simple.setValue("Dzaru");

    expect(simple.getValue()!.toUpperCase()).toBe("DZARU");
  });
});
```

- `SimpleGeneric2<T = string>` falls back to `string` whenever no type argument is given and none can be inferred
- `new SimpleGeneric2()` therefore resolves `T` to `string` — `getValue()` is correctly typed as `string | undefined`, so `.toUpperCase()` type-checks and runs safely, unlike the `SimpleGeneric` example above

> **Note:** A default only applies when no type argument is given *and* none can be inferred. `new SimpleGeneric2<number>()` still overrides it, and anything the checker can infer still wins over the default.

> **Tip:** Defaults have to come last, the same way optional function parameters do — `<T = string, U>` is a compile error, while `<T, U = string>` is fine.

---

## 🔒 Generic Constraint

By default, a generic type parameter accepts any data type at all. Sometimes that needs to be narrowed. Use `extends Type` inside `<>` to restrict a generic parameter to `Type` and its subtypes only.

**`generic-typescript/test/generic.test.ts`**

```typescript
describe("generic", () => {
  // ...previous classes, functions, and tests stay the same...

  interface Employee {
    id: string;
    name: string;
  }

  interface Manager extends Employee {
    totalEmployee: number;
  }

  interface VP extends Manager {
    totalManager: number;
  }

  class EmployeeData<T extends Employee> {
    constructor(public employee: T) {}
  }

  it("should support constraint", async () => {
    const data1 = new EmployeeData<Employee>({
      id: "1",
      name: "Dzaru",
    });
    console.log(data1.employee);

    const data2 = new EmployeeData<Manager>({
      id: "2",
      name: "Rizky",
      totalEmployee: 10,
    });
    console.log(data2.employee);

    const data3 = new EmployeeData<VP>({
      id: "3",
      name: "Fathan",
      totalEmployee: 10,
      totalManager: 20,
    });
    console.log(data3.employee);

    // const data4 = new EmployeeData<string>({});
  });
});
```

> **Key Insight:** A constraint does two jobs at once. It *rejects* type arguments that don't fit — and it *unlocks* the constrained members inside the class, so `this.employee.name` type-checks. Without `extends Employee`, `T` could be anything, and the checker would permit no property access at all.
>
> **Note:** `EmployeeData<T extends Employee>` accepts only `Employee` and its subtypes (`Manager`, `VP`, ...). Uncommenting `new EmployeeData<string>({})` fails to compile with `Type 'string' does not satisfy the constraint 'Employee'.` (ts2344) — which is why the line is left commented out in the test file.

---

## 📚 Generic Collection

`Array` is a data type we've already used, and it's actually a generic type itself — which is why it can be written as `Array<Type>`. Besides `Array`, TypeScript ships two more generic **collection** types:

- `Set<T>` — a collection of unique values with no guaranteed ordering
- `Map<K, V>` — a collection of key-value pairs

| Collection | Holds | Looked up by | Duplicates |
| --- | --- | --- | --- |
| `Array<T>` | An ordered list of `T` | Numeric index | Allowed |
| `Set<T>` | Unique values of `T` | The value itself (`has`) | Dropped silently |
| `Map<K, V>` | Key-value pairs | The key (`get`) | One value per key |

> **Note:** All three are plain JavaScript classes — `Array`, `Set` and `Map` exist at runtime exactly as they do in JavaScript. The generic parameter only tells the checker what goes in and what comes back out.

### Array\<T\>

The generic type `Array<T>` is simply TypeScript's representation of JavaScript's array type, so it's used exactly the same way JavaScript arrays already are.

> Reference: [MDN — Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)

### Set\<T\>

The generic type `Set<T>` is TypeScript's representation of JavaScript's `Set`, so it's used exactly the same way JavaScript's `Set` already is.

> Reference: [MDN — Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)

### Map\<K, V\>

The generic type `Map<K, V>` is TypeScript's representation of JavaScript's `Map`, so it's used exactly the same way JavaScript's `Map` already is.

> Reference: [MDN — Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)

**`generic-typescript/test/generic.test.ts`**

```typescript
describe("generic", () => {
  // ...previous classes, interfaces, functions, and tests stay the same...

  it("should support array", async () => {
    const array = new Array<string>();
    array.push("Dzaru");
    array.push("Rizky");
    array.push("Fathan");

    expect(array[0]).toBe("Dzaru");
    expect(array[1]).toBe("Rizky");
    expect(array[2]).toBe("Fathan");
  });

  it("should support set", async () => {
    const set = new Set<string>();
    set.add("Dzaru");
    set.add("Rizky");
    set.add("Fathan");

    expect(set.has("Dzaru")).toBe(true);
    expect(set.has("Rizky")).toBe(true);
    expect(set.has("Fathan")).toBe(true);
    expect(set.size).toBe(3);
  });

  it("should support map", async () => {
    const map = new Map<string, number>();

    map.set("Dzaru", 1);
    map.set("Rizky", 2);
    map.set("Fathan", 3);

    expect(map.get("Dzaru")).toBe(1);
    expect(map.get("Rizky")).toBe(2);
    expect(map.get("Fathan")).toBe(3);
    expect(map.size).toBe(3);
  });
});
```

> **Gotcha:** `Set` compares objects by reference, not by contents — adding two separate objects with identical fields leaves you with a set of size 2. Only primitives actually collapse into one entry.

> **Tip:** `map.get("Dzaru")` returns `number | undefined`, never a bare `number` — the key might not be there. The same goes for indexing an array once `noUncheckedIndexedAccess` is on, as it is in this project's `tsconfig.json`.

---

## ⏳ Generic Promise

Working with JavaScript's `async`/`await` means running into `Promise` constantly. TypeScript represents a promise's resolved value generically, as `Promise<T>` — so when writing a function that returns a promise, you can specify exactly what type of data that promise will resolve with.

**`generic-typescript/test/generic.test.ts`**

```typescript
describe("generic", () => {
  // ...previous classes, interfaces, functions, and tests stay the same...

  async function fetchData(value: string): Promise<string> {
    return new Promise<string>((resolve, reject) => {
      setTimeout(() => {
        if (value === "Dzaru") {
          resolve("Hello");
        } else {
          reject("Data not found");
        }
      }, 1000);
    });
  }

  it("should support promise", async () => {
    const result = await fetchData("Dzaru");
    expect(result.toUpperCase()).toBe("HELLO");

    try {
      await fetchData("Dzaru");
    } catch (error) {
      expect(error).toBe("Data not found");
    }
  });
});
```

> **Note:** `Promise<T>` describes only the *resolved* value. The rejection path carries no type at all, which is why `catch (error)` hands you `unknown` — exactly as a plain `try`/`catch` does, no matter what was passed to `reject(...)`.

> **Gotcha:** The `try`/`catch` above calls `fetchData("Dzaru")` — the value that **resolves**. The `catch` block therefore never runs, the assertion inside it is never reached, and the test passes without testing anything. Pass a different value (`fetchData("Budi")`) to actually exercise the rejection path.

> **Tip:** An `async` function always returns a promise, so its return annotation must be `Promise<string>`, never `string`. TypeScript infers it either way — writing it out is still worth it, because it pins the resolved type at the signature instead of leaving it to whatever the body happens to return.

---

## 🔍 Type Argument Resolution

Almost every surprise in this guide comes down to one question: when the checker meets `T`, what does it actually become? It works through the possibilities in a fixed order and stops at the first one that applies:

```text
GenericData<T>, create<T>, Entry<K, V>, SimpleGeneric<T> ...
  ↓
1. Explicit type argument?       new GenericData<string>("Dzaru")
  ↓ no                           → T = string, done
2. Inferable from an argument?   new Entry(1, "Dzaru") · create(1)
  ↓ no                           → T read off the value passed in
3. Declared a default?           class SimpleGeneric2<T = string>
  ↓ no                           → T = string
4. Fall back                     new SimpleGeneric()
                                 → T = unknown
  ↓
Constraint check                 T extends Employee — whatever T resolved to
  ↓                              must satisfy it, or the call is rejected
  ↓
Type erasure                     every <T> removed; one plain JavaScript class remains
  ↓
Runtime                          no type argument survives — Array, Set and Map are just JS
```

That order explains the behaviour running through this guide:

| Question | Answer |
| --- | --- |
| Why can `new Entry(1, "Dzaru")` skip its type arguments? | `K` and `V` appear in the constructor parameters, so step 2 reads them off the values |
| Why did `new SimpleGeneric()` end up as `unknown`? | `T` appears in no constructor parameter, so steps 1–3 all miss and step 4 applies |
| Why does `SimpleGeneric2` behave differently? | Its `T = string` default is caught at step 3, before the fallback is ever reached |
| Why can `this.employee.name` be accessed inside `EmployeeData`? | The constraint guarantees that every permitted `T` has that member |
| Why doesn't a constraint validate the object at runtime? | Constraints are checked before erasure; nothing about `T` survives into JavaScript |
| Why does `Promise<string>` not type the `catch` block? | Only the resolved value is generic — the rejection path carries no type |

---

## 🎯 Quick Reference

| Concept | Purpose | Key Syntax |
| --- | --- | --- |
| **Generic Class** | A class whose type is decided when an object is created | `class GenericData<T> { value: T }` |
| **Generic Function** | A function whose parameter/return type is decided when it's called | `function create<T>(value: T): T` |
| **Generic Interface** | An interface parameterized by a type | `interface Repository<T> { ... }` |
| **Generic Type Alias** | A type alias parameterized by a type | `type Box<T> = { value: T }` |
| **Multiple Generic Types** | More than one type parameter on the same class or function | `class Entry<K, V> { ... }` |
| **Type Inference** | Let the argument decide the type | `create(1)` → `T = number` |
| **Optional Generic Type** | Type argument inferred from a constructor/function argument | `new Entry(1, "Dzaru")` |
| **Generic Default** | Fallback type used when none is given and none can be inferred | `class SimpleGeneric2<T = string>` |
| **`unknown` Fallback** | What `T` becomes with nothing to infer and no default | `new SimpleGeneric()` → `T = unknown` |
| **Generic Constraint** | Restrict a type parameter to a type and its subtypes | `class EmployeeData<T extends Employee>` |
| **Array\<T\>** | Generic representation of a JavaScript array | `new Array<string>()` |
| **Set\<T\>** | Generic collection of unique values | `new Set<string>()` |
| **Map\<K, V\>** | Generic collection of key-value pairs | `new Map<string, number>()` |
| **Promise\<T\>** | Generic representation of an async value | `function f(): Promise<string>` |

---

## 💡 Best Practices

**✅ Do This**

- **Reach for a generic type parameter instead of `any`** whenever a class or function needs to work with more than one data type — it keeps compile-time checking intact
- **Let TypeScript infer a generic type** from constructor or function arguments instead of always specifying it explicitly, when the type parameter already appears in that argument
- **Give a generic parameter a sensible default** (`T = string`) when a class or function is commonly used with one particular type, so an omitted type argument doesn't silently fall back to `unknown`
- **Use `extends` to constrain a generic parameter** when only a specific shape (or its subtypes) should ever be allowed
- **Constrain when you need to read a member** — without `extends`, the checker permits no property access on `T` at all
- **Reach for the built-in generic collection types** — `Array<T>`, `Set<T>`, `Map<K, V>`, and `Promise<T>` — instead of hand-rolling equivalents
- **Follow the naming conventions** (`T`, `K`, `V`, `E`, `R`), or use a descriptive `TSomething` once a class carries several parameters
- **Put defaulted parameters last** — `<T, U = string>` compiles, `<T = string, U>` doesn't
- **Annotate an `async` function as `Promise<T>`**, which pins the resolved type at the signature rather than at whatever the body returns

**❌ Avoid This**

- **Falling back to `any` just because a type isn't known yet** — that's exactly the problem generics exist to solve, and it throws away every compile-time guarantee
- **Instantiating a generic class with no type argument and no way to infer one** — the type parameter silently becomes `unknown`, and mistakes only surface once the code actually runs
- **Trusting `npm test` to catch type errors** — this project's Jest transform (`babel-jest`) strips types without checking them; run `npx tsc --noEmit` (or switch to `ts-jest`) to actually validate types
- **Assuming a constraint like `T extends Employee` also validates the object's shape at runtime** — like all TypeScript types, it's erased once compiled to JavaScript
- **Spelling out a type argument the checker would infer anyway** — `create(1)` says as much as `create<number>(1)`, with less to keep in sync
- **Expecting `Promise<T>` to type the rejection path** — only the resolved value is generic; a caught error is always `unknown`
- **Treating `Set` as deduplicating objects** — it compares them by reference, so two identical-looking objects both go in
- **Reading `map.get(key)` as if it always returns a value** — the result is `T | undefined`, because the key may be missing

> Reference: [TypeScript Handbook — Generics](https://www.typescriptlang.org/docs/handbook/2/generics.html) · [Everyday Types](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html) · [tsconfig reference](https://www.typescriptlang.org/tsconfig)
