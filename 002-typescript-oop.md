# 🧱 TypeScript Object-Oriented Programming

A practical reference guide for object-oriented programming in TypeScript — covering classes, inheritance, visibility, polymorphism, abstract classes, and more.

---

## 📋 Table of Contents

- [Introduction](#-introduction)
  - [What is Object-Oriented Programming?](#what-is-object-oriented-programming)
  - [What is an Object?](#what-is-an-object)
  - [What is a Class?](#what-is-a-class)
  - [OOP in TypeScript](#oop-in-typescript)
- [Project Setup](#-project-setup)
  - [Creating the Project](#creating-the-project)
  - [Adding Jest for Unit Testing](#adding-jest-for-unit-testing)
  - [Adding Babel](#adding-babel)
  - [Adding TypeScript](#adding-typescript)
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
- [Quick Reference](#-quick-reference)
- [Best Practices](#-best-practices)

---

## 🎯 Introduction

**What is Object-Oriented Programming?**

- Object-Oriented Programming (OOP) is a programming paradigm built around the concept of "objects"
- Many programming paradigms exist, but OOP is by far the most popular today
- Two core terms are essential to understanding OOP: **Object** and **Class**

**What is an Object?**

- An object is data that holds fields / properties / attributes, and methods / functions / behavior

**What is a Class?**

- A class is a blueprint, prototype, or template used to create objects
- A class declares all the properties and functions that its objects will have
- Every object is always created from a class
- A single class can create an unlimited number of objects

**OOP in TypeScript**

> **Key Insight:** TypeScript's OOP features ultimately compile down to plain JavaScript objects and prototypes — the syntax is safer, but the runtime model underneath is still JavaScript's.

- OOP in TypeScript is implemented by compiling down to JavaScript code
- JavaScript itself was originally designed as a procedural language, not an object-oriented one
- Because of that, OOP in JavaScript isn't as fully-featured as in languages built from the ground up around OOP, such as Java or C++
- OOP in TypeScript works almost identically to OOP in JavaScript — a solid grasp of JavaScript's OOP model carries over directly

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

## 🧱 Class Basics

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

---

## 📋 Properties & Methods

### Properties and Fields

- Properties (also called fields) are the attributes that belong to a class
- In JavaScript, an attribute can be created directly without declaring it ahead of time
- In TypeScript, a property must be declared explicitly, along with its type
- Just like attributes on a `type` or `interface`, class properties can also be optional, mandatory, or `readonly`
- Mandatory properties must be assigned a value inside the constructor

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

---

## 🧬 Inheritance

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

---

## 🔐 Visibility

In JavaScript and TypeScript, properties and methods are accessible both inside and outside a class by default (`public`). JavaScript has private properties/methods using the `#` prefix, which restricts access to inside the class only. TypeScript makes this easier by introducing three explicit keywords.

| Visibility  | Description                                                               |
| ----------- | -------------------------------------------------------------------------- |
| `public`    | Accessible anywhere; the default when no visibility keyword is given       |
| `private`   | Accessible only from within the class itself                               |
| `protected` | Same as `private`, but also accessible from subclasses                     |

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

---

## 🔄 instanceof & Polymorphism

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

### Polymorphism

"Polymorphism" comes from Greek, meaning "many forms." In OOP, polymorphism is an object's ability to take on a different form, and it's closely tied to inheritance.

**Method Polymorphism**

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

### Type Casting

Basic TypeScript covers type assertions, which let you convert a value from one type to a more specific one. The same technique applies to method polymorphism — combine `instanceof` with a type assertion to safely narrow a polymorphic value.

> ⚠️ **Note:** When type-casting, always check the most specific subclass first. If the order were swapped — checking `Manager` before `VicePresident` — a `VicePresident` object would also match the `Manager` check (since it's a subclass of `Manager` too), so it would stop there and never reach the `VicePresident` branch.

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

---

## 🎯 Quick Reference

| Concept               | Purpose                                                     | Key Syntax                                |
| ---------------------- | ------------------------------------------------------------ | -------------------------------------------- |
| **Class**             | Blueprint for creating objects                               | `class Customer { }`                       |
| **Constructor**        | Runs once, the first time an object is created                | `constructor(id: number) { ... }`          |
| **Readonly Property**  | Immutable after construction                                  | `readonly id: number`                      |
| **Getter / Setter**    | Controlled read/write access to a property                    | `getName()` / `setName(value)`             |
| **extends**            | Inherit properties and methods from a parent class             | `class Manager extends Employee`           |
| **implements**         | Enforce an interface's contract on a class                    | `class Person implements HasName`          |
| **super**              | Call the parent class's constructor or method                 | `super(name)` / `super.sayHello()`         |
| **Visibility**         | Control where a member can be accessed                        | `public`, `private`, `protected`           |
| **Parameter Property** | Shorthand that turns a constructor parameter into a property  | `constructor(public name: string)`         |
| **instanceof**         | Check whether an object is an instance of a class              | `obj instanceof ClassName`                 |
| **Polymorphism**       | Treat a subclass instance as its parent type                   | `let e: Employee = new Manager(...)`       |
| **Abstract Class**     | Unfinished base class that can't be instantiated directly       | `abstract class Customer { ... }`          |
| **static**             | Member that belongs to the class itself, not an instance       | `static NAME: string`                      |
| **namespace**          | Group related code inside a module                             | `namespace MathUtil { ... }`                |

---

## 💡 Best Practices

**✅ Do This**

- **Order `instanceof` checks from most specific to least specific** — check subclasses before their parent class when type-casting a polymorphic value
- **Use `protected` instead of `private`** when a subclass legitimately needs access to a parent's member
- **Use Parameter Properties** to cut down constructor boilerplate for simple data-holding classes
- **Use `abstract` classes** to enforce a shared contract across subclasses without allowing direct instantiation
- **Extend `Error`** for domain-specific error types so `catch` blocks can narrow with `instanceof`

**❌ Avoid This**

- **Checking a class instance with `typeof`** — it always returns `"object"`; use `instanceof` instead
- **Reordering polymorphism checks carelessly** — checking a base class before its subclass short-circuits and swallows the subclass case
- **Forgetting `super(...)` in a subclass constructor** — TypeScript requires the parent constructor to run before `this` can be used
- **Relying on `private`/`protected` for security** — they're compile-time-only checks, erased at runtime like other TypeScript types
- **Assuming structural typing means identical intent** — two classes with matching shapes are assignable to each other even if they model unrelated concepts
