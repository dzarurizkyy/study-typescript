# Study TypeScript 🔷

This repository contains a comprehensive reference guide for TypeScript — covering the language fundamentals, object-oriented programming, generics, runtime validation, and a complete RESTful API built end-to-end with Express, Prisma, and PostgreSQL.

## Installation 🔧

1. **Install Node.js**:
   - Download the **LTS** version from `https://nodejs.org/`
   - Verify the installation:

     ```bash
     node -v
     npm -v
     ```

2. **Clone the Repository**:

   ```bash
   git clone https://github.com/dzarurizkyy/study-typescript.git
   cd study-typescript
   ```

3. **Install TypeScript** (inside a chapter's project folder):

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

   > TypeScript is installed **locally per project** (`--save-dev`), not globally — that's why every command across this guide uses `npx tsc` instead of a bare `tsc`.

4. **Follow Along Per Chapter**:
   - Start with [TypeScript Basics](001-typescript-basics.md) — every later chapter builds on the same toolchain

   > Each chapter is a **separate** project — there is no single root `package.json`. Repeat step 3 inside each chapter's own project folder ([001](001-typescript-basics.md), [002](002-typescript-oop.md), [003](003-typescript-generic.md), [004](004-typescript-validation.md), [005](005-typescript-study-case.md)).

## List of Material 📚

- 📘 **[TypeScript Basics](001-typescript-basics.md)**

  Setting up a TypeScript project from scratch, then the type system itself — primitives, arrays, unions, type aliases, enums, functions, and control flow:

  ```typescript
  export function sayHello(name: string): string {
    return `Hello ${name}`;
  }
  ```

- 🧱 **[TypeScript Object-Oriented Programming](002-typescript-oop.md)**

  Classes, inheritance, visibility, polymorphism, and abstract classes — `super()` calls the parent constructor before a child class extends it with its own fields:

  ```typescript
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
  ```

- 🧩 **[TypeScript Generic](003-typescript-generic.md)**

  Generic classes and functions, constraints, defaults, and the built-in generic collection types (`Array<T>`, `Set<T>`, `Map<K, V>`) — one class, reused safely across every data type:

  ```typescript
  class GenericData<T> {
    value: T;
    constructor(value: T) {
      this.value = value;
    }
  }

  const dataNumber = new GenericData<number>(1);
  const dataString = new GenericData<string>("Dzaru");
  ```

- 🛡️ **[TypeScript Validation (with Zod)](004-typescript-validation.md)**

  Runtime validation with [Zod](https://zod.dev/) — schemas, type coercion, objects, collections, custom error messages, optional fields, transforms, and custom validation logic:

  ```typescript
  import z from "zod";

  const schema = z.string().min(3).max(100);
  const request = "Dzaru";
  const result = schema.parse(request);
  ```

- 🚀 **[TypeScript RESTful API — Contact Management](005-typescript-study-case.md)**

  A full RESTful API built stage by stage across three modules — User → Contact → Address — using Express, Prisma, PostgreSQL, Zod, and Jest:

  ```typescript
  export const authMiddleware = async (
    req: UserRequest,
    res: Response,
    next: NextFunction,
  ) => {
    const token = req.headers["x-api-token"] as string;

    if (token) {
      const user = await prismaClient.user.findFirst({
        where: {
          token: token,
        },
      });
      if (user) {
        req.user = user;
        next();
        return;
      }
    }
    throw new ResponseError(401, "Unauthorized");
  };
  ```

## 📍 References

- [Udemy](https://www.udemy.com/course/belajar-typescript)

## 👨‍💻 Contributors

- [Dzaru Rizky Fathan Fortuna](https://www.linkedin.com/in/dzarurizky)
