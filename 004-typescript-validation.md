# 🛡️ TypeScript Validation (with Zod)

A practical reference guide for data validation in TypeScript — covering schemas, primitive types, type coercion, error handling, objects, collections, custom messages, optional fields, transforms, and custom validation logic, all using the [Zod](https://zod.dev/) library.

---

## 📋 Table of Contents

- [Introduction](#-introduction)
  - [What is Validation?](#what-is-validation)
- [Project Setup](#-project-setup)
  - [Creating the Project](#creating-the-project)
  - [Adding Jest for Unit Testing](#adding-jest-for-unit-testing)
  - [Adding Babel](#adding-babel)
  - [Adding TypeScript](#adding-typescript)
  - [Setting Up the TypeScript Project](#setting-up-the-typescript-project)
  - [Setting Up TypeScript for Jest](#setting-up-typescript-for-jest)
  - [Installing Zod](#installing-zod)
- [Schema](#-schema)
- [Validating Primitive Data Types](#-validating-primitive-data-types)
- [Data Type Conversion](#-data-type-conversion)
- [Date Validation](#-date-validation)
- [Validation Error](#-validation-error)
- [Validation Error Without Exception](#-validation-error-without-exception)
- [Object Validation](#-object-validation)
  - [Nested Object](#nested-object)
- [Collection Validation](#-collection-validation)
- [Custom Validation Message](#-custom-validation-message)
- [Optional Validation](#-optional-validation)
- [Transform](#-transform)
- [Custom Validation](#-custom-validation)
- [Quick Reference](#-quick-reference)
- [Best Practices](#-best-practices)

---

## 🎯 Introduction

**What is Validation?**

- Validation is one of the most important things to do when building an application
- Validation makes sure data is correct, or in the expected shape, before it gets processed
- Validation keeps data consistent and prevents it from becoming corrupted
- Validation is usually done in application code, as well as through constraints at the database table level

> **Key Insight:** TypeScript's type system only exists at compile time — once compiled to JavaScript, every type annotation is erased. Validation is what enforces those same rules at runtime, against data TypeScript can't see coming (user input, API responses, database rows).

TypeScript unfortunately doesn't ship a built-in validation library, so validation has to be done manually. Fortunately, the TypeScript community has built many libraries that make this much easier — one of the most popular is [Zod](https://zod.dev/).

---

## 🏗️ Project Setup

### Creating the Project

```bash
mkdir study-typescript-validation
cd study-typescript-validation
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

**`study-typescript-validation/package.json`**

```json
{
  "name": "study-typescript-validation",
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
  "type": "module",
  "devDependencies": {
    "@babel/preset-env": "^7.29.7",
    "@babel/preset-typescript": "^7.29.7",
    "@jest/globals": "^30.4.1",
    "@types/jest": "^30.0.0",
    "babel-jest": "^30.4.1",
    "jest": "^30.4.2",
    "typescript": "^6.0.3"
  },
  "dependencies": {
    "zod": "^4.4.3"
  }
}
```

**`study-typescript-validation/babel.config.json`**

```json
{
  "presets": ["@babel/preset-env", "@babel/preset-typescript"]
}
```

**`study-typescript-validation/tsconfig.json`**

```json
{
  "compilerOptions": {
    "module": "es6",
    "moduleResolution": "bundler",
    "target": "es6",
    "types": ["jest"],

    "sourceMap": true,
    "declaration": true,
    "declarationMap": true,

    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true,

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

> **Key Insight:** setting `"module": "es6"` without an explicit `moduleResolution` leaves TypeScript on its legacy `"Classic"` resolution strategy, which ignores the `"exports"` field in a package's `package.json`. Zod v4 exposes its ESM/CJS builds through `exports`, so without `moduleResolution` set, TypeScript reports `Cannot find module 'zod'` (`ts(2792)`) even though the package is installed in `node_modules`. Setting `"moduleResolution": "bundler"` fixes it — it understands `exports` maps while still leaving the actual transpilation to Babel.

### Installing Zod

```bash
npm install zod
```

> Reference: [zod.dev](https://zod.dev/)

---

## 📐 Schema

The first thing needed to perform validation is a **schema** — a set of rules that have already been defined. Once a schema exists, it can be used to validate data against those rules.

---

## 🔤 Validating Primitive Data Types

Zod supports validating many TypeScript primitive types, such as `string`, `number`, `boolean`, and more. To use Zod, `z` is imported from the `zod` package, then the method matching the target data type is called.

> References: [String](https://zod.dev/?id=strings) · [Number](https://zod.dev/?id=numbers) · [Boolean](https://zod.dev/?id=booleans)

**`study-typescript-validation/test/validation.test.ts`**

```typescript
import z from "zod";

describe("validation", () => {
  it("should support validation", async () => {
    const schema = z.string().min(3).max(100);
    const request = "Dzaru";
    const result = schema.parse(request);

    expect(result).toBe(request);
  });

  it("should support validate primitive data type", async () => {
    const usernameSchema = z.email();
    const isAdminSchema = z.boolean();
    const priceSchema = z.number().min(1000).max(1_000_000);

    const username = usernameSchema.parse("dzarurizkybusiness@gmail.com");
    const isAdmin = isAdminSchema.parse(true);
    const price = priceSchema.parse(10000);

    console.info(username);
    console.info(isAdmin);
    console.info(price);
  });
});
```

---

## 🔄 Data Type Conversion

When a schema is created for a type like `string`, `number`, or `boolean`, `parse()` must be called with a value of that same type. User input, though, often arrives as a different type — a `number` sent as the string `"1234"`, or a `boolean` sent as the string `"true"`. Zod's `coerce` object performs that type conversion automatically before validating.

**`study-typescript-validation/test/validation.test.ts`**

```typescript
describe("validation", () => {
  // ...previous tests stay the same...

  it("should support data conversion", async () => {
    const usernameSchema = z.coerce.string();
    const isAdminSchema = z.coerce.boolean();
    const priceSchema = z.coerce.number().min(1000).max(1_000_000);

    const username = usernameSchema.parse(123);
    const isAdmin = isAdminSchema.parse("true");
    const price = priceSchema.parse("1000");

    console.info(username);
    console.info(isAdmin);
    console.info(price);
  });
});
```

---

## 📅 Date Validation

Zod can also validate the `Date` data type.

> Reference: [zod.dev/?id=dates](https://zod.dev/?id=dates)

**`study-typescript-validation/test/validation.test.ts`**

```typescript
describe("validation", () => {
  // ...previous tests stay the same...

  it("should support date validation", () => {
    const birthDateSchema = z.coerce
      .date()
      .min(new Date(1980, 0, 1))
      .max(new Date(2020, 0, 1));
    const birthDate = birthDateSchema.parse("1990-01-01");
    console.info(birthDate);

    const birthDate2 = birthDateSchema.parse(new Date(1990, 10, 10));
    console.info(birthDate2);
  });
});
```

---

## ❌ Validation Error

When data fails validation, Zod throws a `ZodError`.

**`study-typescript-validation/test/validation.test.ts`**

```typescript
import z, { ZodError } from "zod";

describe("validation", () => {
  // ...previous tests stay the same...

  it("should return zod error if invalid", async () => {
    const schema = z.string().min(3).max(300);

    try {
      schema.parse("dz");
    } catch (err) {
      if (err instanceof ZodError) {
        err.issues.forEach((err) => {
          console.info(err.message);
        });
      }
    }
  });
});
```

> **Key Insight:** in Zod v4, `ZodError` exposes its list of failures through the `.issues` property — a plain array, not a callable method. Calling `err.errors()` throws `TypeError: err.errors is not a function`, since `.errors` doesn't exist on the error object at all in v4. Iterate `err.issues` directly instead.

---

## 🛟 Validation Error Without Exception

Besides `parse()`, which throws on failure, Zod also provides `safeParse()`, which never throws — it returns a result object describing whether validation succeeded instead.

**`study-typescript-validation/test/validation.test.ts`**

```typescript
describe("validation", () => {
  // ...previous tests stay the same...

  it("should return zod error if invalid without exception", async () => {
    const schema = z.string().min(3).max(100);
    const result = schema.safeParse("dzaru");

    if (result.success) {
      console.info(result.data);
    } else {
      console.info(result.error.message);
    }
  });
});
```

- `result.success` tells whether validation passed
- `result.data` holds the parsed value when it did
- `result.error` holds the `ZodError` when it didn't

---

## 📦 Object Validation

Applications frequently work with plain JavaScript objects. Zod can validate a JS object too, making it possible to validate every field at once.

> Reference: [zod.dev/?id=objects](https://zod.dev/?id=objects)

**`study-typescript-validation/test/validation.test.ts`**

```typescript
describe("validation", () => {
  // ...previous tests stay the same...

  it("should can validate object", async () => {
    const loginSchema = z.object({
      username: z.email(),
      password: z.string().min(6).max(20),
    });

    const request = {
      username: "dzarurizkybusiness@gmail.com",
      password: "pass1234",
      ignore: true,
    };

    const result = loginSchema.parse(request);
    console.info(result);
  });
});
```

> **Note:** the `ignore` field in `request` isn't declared in `loginSchema`. `z.object()` silently strips any key that isn't part of the schema — `result` only ever contains `username` and `password`.

### Nested Object

Zod can also validate a nested object. To validate a nested object, its own object schema must be defined as well.

**`study-typescript-validation/test/validation.test.ts`**

```typescript
describe("validation", () => {
  // ...previous tests stay the same...

  it("should support nested object", async () => {
    const createUserSchema = z.object({
      id: z.uuid(),
      name: z.string().min(3).max(100),
      email: z.email(),
      address: z.object({
        street: z.string().min(3).max(100),
        city: z.string().min(3).max(100),
        province: z.string().min(3).max(100),
        country: z.string().min(3).max(100),
        postalCode: z.string().length(5),
      }),
    });

    const request = {
      id: crypto.randomUUID(),
      name: "Dzaru",
      email: "dzarurizkybusiness@gmail.com",
      address: {
        street: "jalan pahlawan",
        city: "bandung",
        province: "jawa barat",
        country: "indonesia",
        postalCode: "12345",
      },
    };

    const result = createUserSchema.parse(request);
    console.info(result);
  });
});
```

---

## 📚 Collection Validation

Besides objects, Zod can also validate **collection** types, such as `Array`, `Set`, and `Map`.

> References: [Array](https://zod.dev/?id=arrays) · [Set](https://zod.dev/?id=sets) · [Map](https://zod.dev/?id=maps)

**`study-typescript-validation/test/validation.test.ts`**

```typescript
describe("validation", () => {
  // ...previous tests stay the same...

  it("should support array validation", async () => {
    const schema = z.array(z.email()).min(1).max(3);

    const request = ["dzaru@gmail.com"];
    const result = schema.parse(request);
    console.info(result);
  });

  it("should support set validation", async () => {
    const schema = z.set(z.email()).min(1).max(3);

    const request = new Set(["dzaru@gmail.com", "dzaru2@gmail.com"]);
    const result = schema.parse(request);
    console.info(result);
  });

  it("should support map validation", async () => {
    const schema = z.map(z.string(), z.number()).min(1).max(3);

    const request = new Map<string, number>([
      ["dzaru", 1234],
      ["dzaru2", 5678],
    ]);
    const result = schema.parse(request);
    console.info(result);
  });
});
```

---

## 💬 Custom Validation Message

Zod already provides a default error message out of the box. If a different message is needed, it can be set as an extra argument on the schema method.

**`study-typescript-validation/test/validation.test.ts`**

```typescript
describe("validation", () => {
  // ...previous tests stay the same...

  it("should can validate object with message", async () => {
    const loginSchema = z.object({
      username: z.email("Email is invalid"),
      password: z
        .string()
        .min(6, "Password must be at least 6 characters long"),
    });

    const request = {
      username: "dzarurizkybusiness@gmail.com",
      password: "pass1234",
      ignore: true,
    };

    const result = loginSchema.parse(request);
    console.info(result);
  });
});
```

---

## ❓ Optional Validation

By default, every field declared in a schema is required. Sometimes a field genuinely isn't required — `optional()` marks a schema field as not mandatory.

**`study-typescript-validation/test/validation.test.ts`**

```typescript
describe("validation", () => {
  // ...previous tests stay the same...

  it("should can support optional validation", async () => {
    const registerSchema = z.object({
      username: z.email(),
      password: z.string().min(6).max(20),
      firstName: z.string().min(3).max(100),
      lastName: z.string().min(3).max(100).optional(),
    });

    const request = {
      username: "dzarurizkybusiness@gmail.com",
      password: "pass1234",
      firstName: "Dzaru",
    };

    const result = registerSchema.parse(request);
    console.info(result);
  });
});
```

---

## 🔁 Transform

Every schema has a `transform()` function that can be used to reshape data right after `parse()` succeeds.

**`study-typescript-validation/test/validation.test.ts`**

```typescript
describe("validation", () => {
  // ...previous tests stay the same...

  it("should can support transform", async () => {
    const schema = z.string().transform((val) => val.toUpperCase());

    const result = schema.parse("dzaru");
    console.info(result);
  });
});
```

---

## 🧪 Custom Validation

`transform()` can also take a second parameter, `RefinementCtx`, which can be used to register an issue when a problem is found. This makes it possible to implement validation rules Zod doesn't provide out of the box.

**`study-typescript-validation/test/validation.test.ts`**

```typescript
import z, { ZodError, type RefinementCtx } from "zod";

describe("validation", () => {
  // ...previous tests stay the same...

  function mustUppercase(data: string, ctx: RefinementCtx) {
    if (data !== data.toUpperCase()) {
      ctx.addIssue({
        code: "custom",
        message: "username must be uppercase",
      });
      return z.NEVER;
    } else {
      return data;
    }
  }

  it("should can create custom validation", () => {
    const loginSchema = z.object({
      name: z.string().transform(mustUppercase),
      password: z.string().min(3).max(100),
    });

    const result = loginSchema.parse({
      name: "dzaru",
      password: "[PASSWORD]",
    });
    console.info(result);
  });
});
```

> ⚠️ **Note:** `name: "dzaru"` is lowercase, so `mustUppercase` calls `ctx.addIssue(...)` and returns `z.NEVER`, which makes `loginSchema.parse(...)` throw a `ZodError`. That call isn't wrapped in `try/catch` here, so running this test as written currently fails with an uncaught `ZodError` — pass an already-uppercase `name` (e.g. `"DZARU"`), or wrap the call in `try/catch`/use `safeParse`, to see it resolve successfully instead.

---

## 🎯 Quick Reference

| Concept | Purpose | Key Syntax |
| --- | --- | --- |
| **Schema** | Rule set used to validate data | `z.string().min(3).max(100)` |
| **Primitive Validation** | Validate `string`/`number`/`boolean`/email | `z.string()`, `z.number()`, `z.boolean()`, `z.email()` |
| **Coercion** | Convert input to the expected type before validating | `z.coerce.number()` |
| **Date Validation** | Validate/convert `Date` values | `z.coerce.date().min(...).max(...)` |
| **parse()** | Validate and throw `ZodError` on failure | `schema.parse(data)` |
| **safeParse()** | Validate without throwing | `schema.safeParse(data)` |
| **Object Schema** | Validate the shape of a JS object | `z.object({ ... })` |
| **Nested Object** | Validate an object property that's itself an object | `z.object({ address: z.object({ ... }) })` |
| **Array\<T\> validation** | Validate a collection of items sharing one schema | `z.array(z.email()).min(1).max(3)` |
| **Set\<T\> validation** | Validate a `Set` of items sharing one schema | `z.set(z.email())` |
| **Map\<K, V\> validation** | Validate a `Map`'s keys and values | `z.map(z.string(), z.number())` |
| **Custom Message** | Override the default error message | `z.string().min(6, "message")` |
| **Optional Field** | Make an object field not required | `z.string().optional()` |
| **Transform** | Reshape data after it passes validation | `z.string().transform((v) => v.toUpperCase())` |
| **Custom Validation** | Add validation logic Zod doesn't provide out of the box | `ctx.addIssue(...); return z.NEVER;` |

---

## 💡 Best Practices

**✅ Do This**

- **Reach for `safeParse()`** instead of wrapping `parse()` in `try/catch` whenever a validation failure is an expected, regular code path (e.g. handling user input) — it avoids throwing for ordinary control flow
- **Read a caught `ZodError`'s `.issues` array** to inspect what failed and why
- **Reach for `z.coerce.*`** at the boundary where input naturally arrives as the wrong primitive type (query strings, form fields), instead of converting it by hand
- **Give schemas custom messages** (`z.string().min(6, "message")`) for user-facing fields, so failures are actionable instead of generic
- **Use `transform()` to reshape already-valid data**, and `RefinementCtx.addIssue()` inside a transform when a rule can't be expressed with schema methods alone

**❌ Avoid This**

- **Assuming an undeclared extra field survives `z.object()` parsing** — by default it's silently stripped, not merely ignored (see the `ignore` field in the object validation examples)
- **Calling `.errors()` on a caught `ZodError`** — in Zod v4 it's `.issues`, a plain array property, not a method
- **Leaving a `parse()` call unwrapped when the input can plausibly fail validation** — an uncaught `ZodError` propagates like any other exception (see the Custom Validation note above)
- **Trusting `npm test` to catch type errors** — this project's Jest transform (`babel-jest`) strips types without checking them; run `npx tsc --noEmit` to actually validate types
