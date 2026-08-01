# Study TypeScript 🔷

This repository contains a comprehensive reference guide for TypeScript — covering the language fundamentals, object-oriented programming, generics, runtime validation, and a complete RESTful API built end-to-end with Express, Prisma, and PostgreSQL.

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

- 🚀 **[TypeScript RESTful API — Contact Management](005-typescript-restful-api.md)**

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
