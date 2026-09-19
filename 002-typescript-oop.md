# 🧱 TypeScript Object-Oriented Programming

A practical reference guide for object-oriented programming in TypeScript — covering classes, inheritance, visibility, polymorphism, abstract classes, and more.

---

## 📋 Table of Contents

- [Introduction](#-introduction)
  - [What is Object-Oriented Programming?](#what-is-object-oriented-programming)
  - [What is an Object?](#what-is-an-object)
  - [What is a Class?](#what-is-a-class)
  - [Terminology at a Glance](#terminology-at-a-glance)
  - [OOP in TypeScript](#oop-in-typescript)
- [Project Setup](#-project-setup)
  - [Creating the Project](#creating-the-project)
  - [Adding Jest for Unit Testing](#adding-jest-for-unit-testing)
  - [Adding Babel](#adding-babel)
  - [Setting Up the TypeScript Project](#setting-up-the-typescript-project)
  - [Setting Up TypeScript for Jest](#setting-up-typescript-for-jest)
- [Class Basics](#-class-basics)
  - [Creating a Class](#creating-a-class)
  - [Constructor](#constructor)
- [Properties & Methods](#-properties--methods)
  - [Properties and Fields](#properties-and-fields)
  - [Default Values](#default-values)
  - [Methods](#methods)
  - [Getters and Setters](#getters-and-setters)
- [Inheritance](#-inheritance)
  - [Extending a Class](#extending-a-class)
  - [Interface Inheritance](#interface-inheritance)
  - [extends vs implements](#extends-vs-implements)
  - [Super Constructor](#super-constructor)
  - [Method Overriding](#method-overriding)
  - [Super Method](#super-method)
- [Visibility](#-visibility)
  - [Parameter Properties](#parameter-properties)
- [instanceof & Polymorphism](#-instanceof--polymorphism)
  - [The instanceof Operator](#the-instanceof-operator)
  - [Polymorphism](#polymorphism)
  - [Type Casting](#type-casting)
- [Abstract Classes](#-abstract-classes)
- [Static Members](#-static-members)
- [Class Relationships](#-class-relationships)
- [Error Handling](#-error-handling)
- [Namespace](#-namespace)
- [Object Lifecycle](#-object-lifecycle)
- [Quick Reference](#-quick-reference)
- [Best Practices](#-best-practices)

---

## 🎯 Introduction

### What is Object-Oriented Programming?

- Object-Oriented Programming (OOP) is a programming paradigm built around the concept of "objects"
- Many programming paradigms exist, but OOP is by far the most popular today
- Two core terms are essential to understanding OOP: **Object** and **Class**

### What is an Object?

- An object is data that holds fields / properties / attributes, and methods / functions / behavior

### What is a Class?

- A class is a blueprint, prototype, or template used to create objects
- A class declares all the properties and functions that its objects will have
- Every object is always created from a class
- A single class can create an unlimited number of objects

### Terminology at a Glance

Every term in this guide maps onto one of a handful of ideas:

| Term | Meaning |
| --- | --- |
| **Class** | The blueprint — declares what its objects will have |
| **Object** / **Instance** | One concrete thing built from that blueprint with `new` |
| **Property** / **Field** | A value that belongs to an object |
| **Method** | A function that belongs to an object |
| **Constructor** | The method that runs once, as the object is created |
| **Inheritance** | A class taking on everything its parent class declares |
| **Polymorphism** | Treating a subclass instance as its parent type |
| **Abstract class** | A blueprint left deliberately unfinished, meant to be inherited |

### OOP in TypeScript

- OOP in TypeScript is implemented by compiling down to JavaScript code
- JavaScript itself was originally designed as a procedural language, not an object-oriented one
- Because of that, OOP in JavaScript isn't as fully-featured as in languages built from the ground up around OOP, such as Java or C++
- OOP in TypeScript works almost identically to OOP in JavaScript — a solid grasp of JavaScript's OOP model carries over directly

> **Key Insight:** TypeScript's OOP features ultimately compile down to plain JavaScript objects and prototypes — the syntax is safer, but the runtime model underneath is still JavaScript's. [Object Lifecycle](#-object-lifecycle) traces exactly which half of that does what.

---

## 🏗️ Project Setup

### Creating the Project

```bash
mkdir belajar-typescript-oop
cd belajar-typescript-oop
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

### Setting Up the TypeScript Project

> Installing TypeScript itself and generating `tsconfig.json` (`npm install --save-dev typescript` + `npx tsc --init`) is covered once in the repository's [README](README.md#installation-).

- All compiler configuration is generated into `tsconfig.json`
- Change `"module"` from `"commonjs"` to `"ES6"`

### Setting Up TypeScript for Jest

> Reference: [jestjs.io/docs/getting-started#using-typescript](https://jestjs.io/docs/getting-started#using-typescript)

```bash
npm install --save-dev @babel/preset-typescript
npm install --save-dev ts-jest
```

**`oop-typescript/package.json`**

```json
{
  "name": "oop-typescript",
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
    "@types/jest": "^30.0.0",
    "babel-jest": "^30.4.1",
    "jest": "^30.4.2",
    "ts-jest": "^29.4.11",
    "typescript": "^6.0.3"
  }
}
```

**`oop-typescript/babel.config.json`**

```json
{
  "presets": ["@babel/preset-env", "@babel/preset-typescript"]
}
```

**`oop-typescript/tsconfig.json`**

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
    "target": "esnext",
    "types": [],
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

---

## 🧩 Class Basics

A class declares the shape; `new` turns that declaration into an actual object. Everything else in this guide builds on those two steps.

> Reference: [typescriptlang.org — Classes](https://www.typescriptlang.org/docs/handbook/2/classes.html)

### Creating a Class

To create a class in TypeScript, use the `class` keyword — just like in JavaScript. Creating an object from a class also just uses the `new` keyword, again just like in JavaScript.

**`oop-typescript/test/class.test.ts`**

```typescript
describe("Class", () => {
  it("should can create class", () => {
    class Customer {}
    class Order {}

    const customer: Customer = new Customer();
    const order = new Order();
  });
});
```

> **Note:** `const customer: Customer = new Customer()` annotates the type explicitly, while `const order = new Order()` lets TypeScript infer it. Both are identical to the compiler — the annotation only earns its place when it differs from what would be inferred.

### Constructor

A constructor is a method or function called the first time an object is created from a class. A constructor works just like an ordinary function and can take parameters — the difference is that a constructor never returns a value.

**`oop-typescript/test/class.test.ts`**

```typescript
class Customer {
  constructor() {
    console.info("Create new customer");
  }
}

class Order {}

describe("Class", () => {
  it("should can create class", () => {
    const customer: Customer = new Customer();
    const order = new Order();
  });

  it("should can create constructor", () => {
    new Customer();
    new Customer();
  });
});
```

> **Gotcha:** A class only gets a free no-argument constructor while it doesn't declare one. As soon as a constructor takes parameters, every `new` call has to supply them — which is why `Order`, with no constructor at all, still works as `new Order()`.

---

## 🔧 Properties & Methods

The members a class declares come in two kinds: properties, which hold state, and methods, which act on it. TypeScript requires both to be declared before use — the one real departure from JavaScript here.

### Properties and Fields

- Properties (also called fields) are the attributes that belong to a class
- In JavaScript, an attribute can be created directly without declaring it ahead of time
- In TypeScript, a property must be declared explicitly, along with its type
- Just like attributes on a `type` or `interface`, class properties can also be optional, mandatory, or `readonly`
- Mandatory properties must be assigned a value inside the constructor

> **Gotcha:** Under `"strict": true`, a required property must be *definitely* assigned by the time the constructor finishes. Declaring `name: string` and forgetting to set it is a compile error (TS2564), not the silent `undefined` it would be in JavaScript.

### Default Values

Properties can also have a default value, assigned with the `=` operator right on the property declaration.

**`oop-typescript/test/properties.test.ts`**

```typescript
describe("Properties", () => {
  class Customer {
    readonly id: number;
    name: string = "Guest";
    age?: number;

    constructor(id: number) {
      this.id = id;
    }
  }

  it("should can have properties", () => {
    const customer = new Customer(1);
    customer.age = 23;

    console.info(customer);
  });
});
```

> **Note:** A property with a default value doesn't need assigning in the constructor — `name: string = "Guest"` already satisfies the checker. `readonly id` still does, because `readonly` blocks assignment everywhere *except* the declaration itself and the constructor.

### Methods

Besides properties, a class can also have functions — more commonly called **methods**. Declaring one works just like in JavaScript, except TypeScript requires a type for every parameter and for the return value.

**`oop-typescript/test/properties.test.ts`**

```typescript
describe("Properties", () => {
  class Customer {
    readonly id: number;
    name: string;
    age?: number;

    constructor(id: number, name: string) {
      this.id = id;
      this.name = name;
    }

    sayHello(name: string): void {
      console.info(`Hello ${name}, my name is ${this.name}`);
    }
  }

  it("should can have properties", () => {
    const customer = new Customer(1, "Dzaru");
    customer.age = 23;

    console.info(customer);
  });

  it("should can have methods", () => {
    const customer = new Customer(1, "Dzaru");
    customer.sayHello("Dzaru");
  });
});
```

> **Tip:** Annotate `void` when a method returns nothing, as `sayHello` does here. It states the intent, and it stops a later `return someValue` from quietly changing the method's contract.

### Getters and Setters

Up to now, changing a property has meant using `=` directly, and reading one has meant using `.`. JavaScript has a feature called getters and setters, and so does TypeScript — a method dedicated to reading a property, and another dedicated to writing it. Because they're just methods, you can add any validation you like before the underlying property actually changes.

**`oop-typescript/test/gettersetter.test.ts`**

```typescript
describe("Getter and Setter", () => {
  class Category {
    _name?: string;

    getName(): string {
      if (this._name) {
        return this._name;
      } else {
        return "empty";
      }
    }

    setName(value: string): void {
      if (value !== "") {
        this._name = value;
      }
    }
  }

  it("should support in class", () => {
    const category = new Category();
    console.info(category.getName());

    category.setName("Food");
    console.info(category.getName());
  });
});
```

> **Key Insight:** These are ordinary methods, called as `category.getName()`. TypeScript also supports *real* accessors via the `get` and `set` keywords — `get name(): string { ... }` and `set name(value: string) { ... }` — which are used like a plain property (`category.name = "Food"`) while still running the validation in between. The `_name` prefix convention exists precisely so the accessor can take the clean name.

---

## 🧬 Inheritance

Inheritance is the mechanism behind most of the rest of this guide: [polymorphism](#polymorphism), [abstract classes](#-abstract-classes) and [`instanceof`](#the-instanceof-operator) all depend on the parent-child chain built here.

### Extending a Class

Just like JavaScript, TypeScript supports inheritance between classes using the `extends` keyword. Every property and method on the parent class is automatically inherited by the child class. Inheritance in TypeScript, as in JavaScript, only allows a single parent class — but a single parent class can have any number of child classes.

**`oop-typescript/test/inheritance.test.ts`**

```typescript
describe("Class", () => {
  class Employee {
    name: string;

    constructor(name: string) {
      this.name = name;
    }
  }

  class Manager extends Employee {
    sayHello(name: string) {
      console.info("Hello Manager, ", name);
    }
  }

  it("should support", () => {
    const employee = new Employee("Dzaru");
    console.info(employee.name);

    const manager = new Manager("Rizky");
    console.info(manager.name);
    manager.sayHello("Rizky");
  });
});
```

> **Note:** `Manager` declares no constructor of its own, so it inherits `Employee`'s — which is why `new Manager("Rizky")` works and `manager.name` is populated. Declaring any constructor on the child makes [`super(...)`](#super-constructor) mandatory.

### Interface Inheritance

In languages like Java, an interface is sometimes used as a contract. TypeScript supports this too — a class can commit to an interface's contract using the `implements` keyword. Because this isn't inheritance, a class can `implements` more than one interface, which isn't possible with `extends`.

**`oop-typescript/test/interface.test.ts`**

```typescript
describe("Interface", () => {
  interface HasName {
    name: string;
  }

  interface CanSayHello {
    sayHello(name: string): void;
  }

  class Person implements HasName, CanSayHello {
    name: string;

    constructor(name: string) {
      this.name = name;
    }

    sayHello(name: string): void {
      console.info("Hello", this.name);
    }
  }

  it("should support inheritance", () => {
    const person = new Person("Budi");
    person.sayHello("Budi");
  });
});
```

> **Key Insight:** `implements` inherits nothing — it only *checks* that the class supplies every member the interface names. `Person` still writes `name` and `sayHello` itself; the interface just guarantees they exist. That's exactly why a class can implement many interfaces but extend only one parent.

### extends vs implements

Both appear in a class declaration and both mention another type, but they do entirely different jobs:

| | `extends` | `implements` |
| --- | --- | --- |
| Purpose | Inherit an implementation from a parent | Commit to a contract |
| How many allowed | One parent class | Any number of interfaces |
| Brings code with it | Yes — properties and method bodies | No — you write every member yourself |
| Exists at runtime | Yes — a real prototype chain | No — erased at compile time |
| Works with `instanceof` | Yes | No |

### Super Constructor

When a child class defines its own constructor, it must call the parent class's constructor — just like in JavaScript. Use the `super` keyword to call the parent class's constructor.

**`oop-typescript/test/super-constructor.test.ts`**

```typescript
describe("Super Constructor", () => {
  class Person {
    name: string;

    constructor(name: string) {
      this.name = name;
    }
  }

  class Employee extends Person {
    department: string;

    constructor(name: string, department: string) {
      super(name);
      this.department = department;
    }
  }

  it("should support", () => {
    const employee = new Employee("Dzaru", "IT");
    console.info(employee);
  });
});
```

> **Gotcha:** `super(name)` has to run before the first use of `this` — swapping those two lines is a compile error, and in plain JavaScript it throws a `ReferenceError` at runtime. The parent must finish building the object before the child can add to it.

### Method Overriding

A child class can redeclare a method that already exists on its parent class. When the redeclared method has an identical signature, that's **method overriding**.

**`oop-typescript/test/method-overriding.test.ts`**

```typescript
describe("Method Overriding", () => {
  class Employee {
    name: string;

    constructor(name: string) {
      this.name = name;
    }

    sayHello(name: string): void {
      console.info(`Hello ${name}, my name is ${this.name}`);
    }
  }

  class Manager extends Employee {
    sayHello(name: string): void {
      console.info(`Hello ${name}, my name is ${this.name} and I am manager`);
    }
  }

  it("should support method overriding", () => {
    const employee = new Employee("Dzaru");
    employee.sayHello("Budi");

    const manager = new Manager("Dzaru");
    manager.sayHello("Budi");
  });
});
```

> **Tip:** Enable `noImplicitOverride` in `tsconfig.json` (it sits in the commented-out "Style Options" block above) and TypeScript will require an explicit `override` keyword on `Manager.sayHello`. That catches the case where the parent's method is later renamed and the "override" silently becomes a brand-new method instead.

### Super Method

Just like with the constructor, when overriding a method you can still call the parent class's version using `super`, followed by the method name.

**`oop-typescript/test/super-method.test.ts`**

```typescript
describe("Super Method", () => {
  class Person {
    name: string;

    constructor(name: string) {
      this.name = name;
    }
  }

  class Employee extends Person {
    department: string;

    constructor(name: string, department: string) {
      super(name);
      this.department = department;
    }

    sayHello(name: string): void {
      console.info(`Hello ${name}, my name is ${this.name}`);
    }
  }

  class Manager extends Employee {
    sayHello(name: string): void {
      super.sayHello(name);
      console.info("I am manager");
    }
  }

  it("should support", () => {
    const employee = new Employee("Dzaru", "IT");
    console.info(employee);

    const manager = new Manager("Dzaru", "IT");
    manager.sayHello("Budi");
  });
});
```

> **Note:** `super.sayHello(name)` runs the parent's implementation before adding to it. Without that call, `Manager.sayHello` would replace `Employee`'s version outright — which is the difference between *extending* behaviour and discarding it.

---

## 🔐 Visibility

In JavaScript and TypeScript, properties and methods are accessible both inside and outside a class by default (`public`). JavaScript has private properties/methods using the `#` prefix, which restricts access to inside the class only. TypeScript makes this easier by introducing three explicit keywords.

| Visibility | Description |
| --- | --- |
| `public` | Accessible anywhere; the default when no visibility keyword is given |
| `private` | Accessible only from within the class itself |
| `protected` | Same as `private`, but also accessible from subclasses |

> **Key Insight:** Visibility keywords are a compile-time-only check. They're erased once TypeScript compiles to JavaScript, so don't rely on them as a runtime security boundary — use JavaScript's `#` private fields if you need real runtime privacy.

**`oop-typescript/test/visibility.test.ts`**

```typescript
describe("Visibility", () => {
  class Counter {
    protected counter: number = 0;

    public increment(): void {
      this.counter++;
    }

    public getCounter(): number {
      return this.counter;
    }
  }

  class DoubleCounter extends Counter {
    public increment(): void {
      this.counter += 2;
    }
  }

  it("should support private", () => {
    const counter = new Counter();
    counter.increment();
    counter.increment();
    counter.increment();
    console.info(counter.getCounter());
  });

  it("should support protected", () => {
    const counter = new DoubleCounter();
    counter.increment();
    counter.increment();
    counter.increment();
    console.info(counter.getCounter());
  });
});
```

> **Note:** `DoubleCounter` can touch `this.counter` only because it's `protected`. Had it been `private`, the subclass would be rejected at compile time — even though the property is sitting right there on the object at runtime.

### Parameter Properties

Constructors often end up with parameters whose only job is to populate a property of the same name. For this case, use **Parameter Properties** — a constructor parameter is automatically turned into a class property by adding a visibility keyword directly on it.

**`oop-typescript/test/parameter-properties.test.ts`**

```typescript
describe("Parameter Properties", () => {
  class Person {
    constructor(public name: string) {}
  }

  it("should support", () => {
    const person = new Person("Dzaru");
    console.info(person.name);
    person.name = "Budi";
    console.info(person.name);
  });
});
```

> **Tip:** `constructor(public name: string) {}` is exactly equivalent to declaring `name: string` and writing `this.name = name` — TypeScript generates both for you. Any modifier works in that position, so `private`, `protected` and `readonly` shorthands are available too.

---

## 🔄 instanceof & Polymorphism

Inheritance creates objects that are several types at once. These are the tools for asking which type you actually hold, and for writing code that doesn't need to ask.

### The instanceof Operator

Sometimes you need to check whether an object is an instance of a particular class. `typeof` doesn't help here — for a class instance, `typeof` always returns `"object"`. The `instanceof` operator returns a boolean: `true` if the object really is an instance of that class, `false` otherwise.

**`oop-typescript/test/instanceof.test.ts`**

```typescript
describe("Instance of", () => {
  class Employee {}
  class Manager {}

  const budi = new Employee();
  const dzaru = new Manager();

  it("should have problem using typeof", () => {
    console.info(typeof budi);
    console.info(typeof dzaru);
  });

  it("should can check object using instanceof", () => {
    expect(budi instanceof Employee).toBe(true);
    expect(dzaru instanceof Manager).toBe(true);
  });
});
```

> **Note:** `instanceof` walks the whole prototype chain, so it's `true` for every ancestor as well — a `VicePresident` is `instanceof VicePresident`, `Manager` *and* `Employee`. That's precisely the behaviour [type casting](#type-casting) has to account for.

### Polymorphism

"Polymorphism" comes from Greek, meaning "many forms." In OOP, polymorphism is an object's ability to take on a different form, and it's closely tied to inheritance. It shows up most often in method parameters:

- When a function or method takes a parameter, you can pass it any polymorphic value that matches
- For example, a function that takes an `Employee` parameter can also accept a `Manager` or `VicePresident` object
- That works because `Manager` and `VicePresident` are subclasses of `Employee`, so any of `Employee`'s descendants can be passed in

**`oop-typescript/test/polymorphism.test.ts`**

```typescript
describe("Polymorphism", () => {
  class Employee {
    constructor(public name: string) {}
  }

  class Manager extends Employee {}
  class VicePresident extends Manager {}

  function sayHello(employee: Employee): void {
    console.info(`Hello ${employee.name}`);
  }

  it("should support", () => {
    let employee: Employee = new Employee("Dzaru");
    console.info(employee);

    employee = new Manager("Dzaru");
    console.info(employee);

    employee = new VicePresident("Dzaru");
    console.info(employee);
  });

  it("should support function parameter", () => {
    sayHello(new Employee("Budi"));
    sayHello(new Manager("Rizky"));
    sayHello(new VicePresident("Budi"));
  });
});
```

> **Key Insight:** Polymorphism is what lets `sayHello(employee: Employee)` accept a `Manager` or a `VicePresident` without changing a line. The parameter names the *least* specific type the function actually needs, so every descendant of it is accepted automatically.

### Type Casting

Basic TypeScript covers type assertions, which let you convert a value from one type to a more specific one. The same technique applies to method polymorphism — combine `instanceof` with a type assertion to safely narrow a polymorphic value.

> **Gotcha:** When type-casting, always check the most specific subclass first. If the order were swapped — checking `Manager` before `VicePresident` — a `VicePresident` object would also match the `Manager` check (since it's a subclass of `Manager` too), so it would stop there and never reach the `VicePresident` branch.

**`oop-typescript/test/polymorphism.test.ts`**

```typescript
describe("Polymorphism", () => {
  class Employee {
    constructor(public name: string) {}
  }

  class Manager extends Employee {}
  class VicePresident extends Manager {}

  function sayHello(employee: Employee): void {
    if (employee instanceof VicePresident) {
      const vp = employee as VicePresident;
      console.info(`Hello VP ${vp.name}`);
    } else if (employee instanceof Manager) {
      const manager = employee as Manager;
      console.info(`Hello Manager ${manager.name}`);
    } else {
      console.info(`Hello Employee ${employee.name}`);
    }
  }

  it("should support", () => {
    let employee: Employee = new Employee("Dzaru");
    console.info(employee);

    employee = new Manager("Dzaru");
    console.info(employee);

    employee = new VicePresident("Dzaru");
    console.info(employee);
  });

  it("should support function parameter", () => {
    sayHello(new Employee("Budi"));
    sayHello(new Manager("Rizky"));
    sayHello(new VicePresident("Budi"));
  });
});
```

> **Tip:** The `as` casts here are optional. `instanceof` has already narrowed the type inside each branch, so `employee.name` would compile fine on its own — the assertion only makes the intent explicit for a reader.

---

## 🎭 Abstract Classes

An abstract class is a class declaration that isn't fully finished. It's allowed to have properties or methods marked `abstract` — meaning no implementation has been written yet — and it can never be instantiated directly with `new`. An abstract class exists purely to serve as a parent class, inherited and completed by its child classes.

**`oop-typescript/test/abstract.test.ts`**

```typescript
describe("Abstract Class", () => {
  abstract class Customer {
    readonly id: number;
    abstract name: string;

    constructor(id: number) {
      this.id = id;
    }

    hello() {
      console.info("Hello");
    }

    abstract sayHello(name: string): void;
  }

  class RegularCustomer extends Customer {
    name: string;

    constructor(id: number, name: string) {
      super(id);
      this.name = name;
    }

    sayHello(name: string): void {
      console.info(`Hello ${name} from Regular Customer`);
    }
  }

  it("should support", () => {
    const customer = new RegularCustomer(1, "Dzaru");
    customer.sayHello("Dzaru");
  });
});
```

> **Key Insight:** `abstract` members are a contract the compiler enforces downward — `RegularCustomer` won't compile until it supplies both `name` and `sayHello`. Unlike an interface, an abstract class can also ship finished code (`hello()` here) and constructor logic that every subclass reuses.

> **Gotcha:** `new Customer(1)` is rejected at compile time, but the class is still an ordinary JavaScript class in the emitted output — `abstract`, like every other type-level construct, is erased.

---

## ⚡ Static Members

`static` is a keyword you can put on a class's properties or methods, which detaches them from any particular object created from the class. Static properties/methods behave like a global variable or function — accessible directly, without ever creating an object from the class. Static members can also carry a visibility keyword, and they're typically used on utility/helper classes.

> **Key Insight:** A static member can only access other static members directly. A non-static member can access static members directly too — but reaching a non-static member from a static one requires going through an actual object instance.

**`oop-typescript/test/static.test.ts`**

```typescript
describe("Static", () => {
  class Configuration {
    static NAME: string = "Study TypeScript OOP";
    static VERSION: number = 1.0;
    static AUTHOR: string = "Dzaru Rizky Fathan Fortuna";
  }

  class MathUtil {
    static sum(...values: number[]): number {
      let total = 0;
      for (const value of values) {
        total += value;
      }
      return total;
    }
  }

  it("should support static properties", () => {
    console.info(Configuration.NAME);
    console.info(Configuration.VERSION);
    console.info(Configuration.AUTHOR);
  });

  it("should support static method", () => {
    console.info(MathUtil.sum(1, 2, 3, 4, 5));
  });
});
```

> **Note:** Static members are reached through the class itself — `Configuration.NAME`, `MathUtil.sum(...)` — never through an instance. `new Configuration().NAME` is a compile error, because the member simply doesn't live on the object.

---

## 🔗 Class Relationships

Because a TypeScript class object compiles down to a plain JavaScript object, two objects from different classes with identical properties and methods are considered structurally the same. In cases like this, you can assign an object of type B to a variable of type A, as long as their properties and methods match.

**`oop-typescript/test/relationship.test.ts`**

```typescript
describe("Relationship", () => {
  class Person {
    constructor(
      public name: string,
      public address: string
    ) {}
  }

  class Customer {
    constructor(
      public name: string,
      public address: string
    ) {}
  }

  it("should support", () => {
    const person: Person = new Customer("Dzaru", "Jl. Test");

    console.info(person);
  });
});
```

> **Key Insight:** This is **structural typing** — TypeScript compares shapes, not declarations. `Customer` is assignable to `Person` purely because its public members match, even though neither class mentions the other. Languages like Java use nominal typing, where the same assignment would be rejected outright.

> **Gotcha:** Structural typing has no idea what your classes *mean*. If two shapes must never be confused, give one a distinguishing member — a `private` field is the usual trick, since a class with private members is only ever compatible with itself.

---

## ⚠️ Error Handling

Just like JavaScript, TypeScript supports error handling with `try`/`catch`, and it works exactly the same way. You can also define a custom error class by extending `Error`, just like in JavaScript.

**`oop-typescript/test/error.test.ts`**

```typescript
describe("Error Handling", () => {
  class ValidationError extends Error {
    constructor(public message: string) {
      super(message);
    }
  }

  function doubleIt(value: number): number {
    if (value < 0) {
      throw new ValidationError("Value cannot be less than 0");
    }

    return value * 2;
  }

  it("should support", () => {
    try {
      const result = doubleIt(-1);
      console.info(result);
    } catch (e) {
      if (e instanceof ValidationError) {
        console.info(e.message);
      }
    }
  });
});
```

> **Gotcha:** Under `"strict": true`, `catch (e)` hands you `unknown`, not `Error` — reaching `e.message` directly is a compile error. The `instanceof ValidationError` check here is what narrows it, which is exactly why a custom error class pays for itself.

> **Note:** `Error` already defines `message`, so the `public message` parameter property merely re-declares it. `super(message)` is the line that matters — it's what populates the built-in message and the stack trace.

---

## 📦 Namespace

Besides JavaScript modules, TypeScript offers another way to organize code: **namespaces**. They're typically used to organize code when a single module contains a large amount of it — if a module is a folder, a namespace is like a subfolder inside it. Create one with the `namespace` keyword, and place classes, functions, and more inside it.

**`oop-typescript/src/math-util.ts`**

```typescript
export namespace MathUtil {
  export const PI: number = 3.14;

  export function sum(...values: number[]): number {
    let total = 0;
    for (const value of values) {
      total += value;
    }
    return total;
  }
}
```

**`oop-typescript/test/namespace.test.ts`**

```typescript
import { MathUtil } from "../src/math-util";

describe("Namespace", () => {
  it("should support", () => {
    console.info(MathUtil.PI);
    console.info(MathUtil.sum(1, 2, 3, 4, 5));
  });
});
```

> **Gotcha:** Namespaces predate ES modules and are largely legacy for application code — note that the test above still reaches `MathUtil` through a plain `import`, because the namespace is `export`ed from a module anyway. Today they earn their place mainly inside `.d.ts` declaration files.
>
> Reference: [typescriptlang.org — Namespaces](https://www.typescriptlang.org/docs/handbook/namespaces.html)

---

## 🧭 Object Lifecycle

Most of the surprises in this guide trace back to one fact: a TypeScript class is two things at once — a compile-time type and a runtime JavaScript object. Following `new VicePresident("Dzaru")` shows where each half applies:

```text
new VicePresident("Dzaru")
  ↓
── Compile time ────────────────  checked by tsc, then erased
  ↓
Type check                       arguments against the constructor signature
  ↓
Visibility check                 public / private / protected
  ↓
abstract check                   is this class instantiable at all?
  ↓
Erasure                          types, interfaces, visibility, abstract — all removed
  ↓
── Runtime ─────────────────────  plain JavaScript from here on
  ↓
Allocate the object
  ↓
Constructor chain                VicePresident → Manager → Employee, via super(...)
  ↓                              each parent finishes before its child adds to it
Field initialization             defaults first, then the constructor body, per level
  ↓
Object ready                     prototype chain: VicePresident → Manager → Employee → Object
  ↓
employee.sayHello(...)           looked up along that chain — the first match wins
```

That split explains the behaviour that surprises people throughout this guide:

| Question | Answer |
| --- | --- |
| Why does `typeof` return `"object"` for every instance? | At runtime the class is gone and only a plain object with a prototype remains — which is why [`instanceof`](#the-instanceof-operator) exists |
| Why must `super(...)` come first? | The parent constructor builds the object the child is about to extend, so `this` doesn't exist until it returns |
| Why can't `private` be trusted as security? | It's enforced during the compile-time half and erased before any code runs |
| Why does an overridden method win? | Lookup walks the prototype chain from the most specific class upward and stops at the first match |
| Why does `instanceof Manager` match a `VicePresident`? | It tests the entire prototype chain, not just the immediate class |
| Why are two unrelated classes assignable? | Assignability is [structural](#-class-relationships) — the checker compares members, never class names |

---

## 🎯 Quick Reference

| Concept | Purpose | Key Syntax |
| --- | --- | --- |
| **Class** | Blueprint for creating objects | `class Customer { }` |
| **Constructor** | Runs once, the first time an object is created | `constructor(id: number) { ... }` |
| **Property** | Attribute declared on a class | `name: string` |
| **Default Value** | Property that starts out with a value | `name: string = "Guest"` |
| **Optional Property** | Property that may be left unset | `age?: number` |
| **Readonly Property** | Immutable after construction | `readonly id: number` |
| **Method** | Function that belongs to a class | `sayHello(name: string): void` |
| **Getter / Setter** | Controlled read/write access to a property | `get name()` / `set name(value)` |
| **extends** | Inherit properties and methods from a parent class | `class Manager extends Employee` |
| **implements** | Enforce an interface's contract on a class | `class Person implements HasName` |
| **super** | Call the parent class's constructor or method | `super(name)` / `super.sayHello()` |
| **Method Overriding** | Replace a parent's method in a subclass | Same signature, redeclared on the child |
| **Visibility** | Control where a member can be accessed | `public`, `private`, `protected` |
| **Parameter Property** | Shorthand that turns a constructor parameter into a property | `constructor(public name: string)` |
| **instanceof** | Check whether an object is an instance of a class | `obj instanceof ClassName` |
| **Polymorphism** | Treat a subclass instance as its parent type | `let e: Employee = new Manager(...)` |
| **Type Casting** | Narrow a polymorphic value to a subclass | `employee as VicePresident` |
| **Abstract Class** | Unfinished base class that can't be instantiated directly | `abstract class Customer { ... }` |
| **Abstract Member** | Member every subclass is required to supply | `abstract sayHello(name: string): void` |
| **static** | Member that belongs to the class itself, not an instance | `static NAME: string` |
| **Structural Typing** | Matching shapes make matching types | `const p: Person = new Customer(...)` |
| **Custom Error** | Domain-specific error type | `class ValidationError extends Error` |
| **namespace** | Group related code inside a module | `namespace MathUtil { ... }` |

---

## 💡 Best Practices

**✅ Do This**

- **Order `instanceof` checks from most specific to least specific** — check subclasses before their parent class when type-casting a polymorphic value
- **Let `instanceof` do the narrowing** — inside the branch the type is already narrowed, so an `as` cast is usually redundant
- **Use `protected` instead of `private`** when a subclass legitimately needs access to a parent's member
- **Use Parameter Properties** to cut down constructor boilerplate for simple data-holding classes
- **Call `super(...)` first** in a subclass constructor, before touching `this`
- **Turn on `noImplicitOverride`** so an override that stops matching its parent is caught instead of silently becoming a new method
- **Use `abstract` classes** to enforce a shared contract across subclasses without allowing direct instantiation
- **Prefer `implements` when you only need a contract** — a class can implement many interfaces but extend just one parent
- **Reach for real `get`/`set` accessors** when you want property syntax with validation behind it
- **Extend `Error`** for domain-specific error types so `catch` blocks can narrow with `instanceof`
- **Prefer ES modules over namespaces** for organizing application code

**❌ Avoid This**

- **Checking a class instance with `typeof`** — it always returns `"object"`; use `instanceof` instead
- **Reordering polymorphism checks carelessly** — checking a base class before its subclass short-circuits and swallows the subclass case
- **Forgetting `super(...)` in a subclass constructor** — TypeScript requires the parent constructor to run before `this` can be used
- **Relying on `private`/`protected` for security** — they're compile-time-only checks, erased at runtime like other TypeScript types
- **Assuming structural typing means identical intent** — two classes with matching shapes are assignable to each other even if they model unrelated concepts
- **Leaving a required property unassigned** — under `strict`, TypeScript rejects a property the constructor never definitely sets
- **Reading `e.message` straight out of a `catch`** — the variable is `unknown` until you narrow it
- **Instantiating a class just to reach a `static` member** — statics live on the class itself, not on any object
- **Using `as` to silence an error you don't understand** — narrow with `instanceof` or fix the type instead
- **Reaching for a namespace where a module would do** — namespaces are mostly legacy outside `.d.ts` files

> Reference: [TypeScript Handbook — Classes](https://www.typescriptlang.org/docs/handbook/2/classes.html) · [Namespaces](https://www.typescriptlang.org/docs/handbook/namespaces.html) · [tsconfig reference](https://www.typescriptlang.org/tsconfig)
