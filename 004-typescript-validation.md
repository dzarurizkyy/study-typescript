# 🛡️ TypeScript Validation (with Zod)

A practical reference guide for data validation in TypeScript — covering schemas, primitive types, type coercion, error handling, objects, collections, custom messages, optional fields, transforms, and custom validation logic, all using the [Zod](https://zod.dev/) library.

---

## 📋 Table of Contents

- [Introduction](#-introduction)
  - [What is Validation?](#what-is-validation)
- [Installing Zod](#-installing-zod)
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
- [Validation Flow](#-validation-flow)
- [Quick Reference](#-quick-reference)
- [Best Practices](#-best-practices)

---

## 🎯 Introduction

### What is Validation?

- Validation is one of the most important things to do when building an application
- Validation makes sure data is correct, or in the expected shape, before it gets processed
- Validation keeps data consistent and prevents it from becoming corrupted
- Validation is usually done in application code, as well as through constraints at the database table level

> **Key Insight:** TypeScript's type system only exists at compile time — once compiled to JavaScript, every type annotation is erased. Validation is what enforces those same rules at runtime, against data TypeScript can't see coming (user input, API responses, database rows).

TypeScript unfortunately doesn't ship a built-in validation library, so validation has to be done manually. Fortunately, the TypeScript community has built many libraries that make this much easier — one of the most popular is [Zod](https://zod.dev/).

---

## 📥 Installing Zod

```bash
npm install zod
```

> Reference: [zod.dev](https://zod.dev/)

> **Key Insight:** Setting `"module": "es6"` in `tsconfig.json` without an explicit `moduleResolution` leaves TypeScript on its legacy `"Classic"` resolution strategy, which ignores the `"exports"` field in a package's `package.json`. Zod v4 exposes its ESM/CJS builds through `exports`, so without `moduleResolution` set, TypeScript reports `Cannot find module 'zod'` (`ts(2792)`) even though the package is installed in `node_modules`. Setting `"moduleResolution": "bundler"` fixes it — it understands `exports` maps while still leaving the actual transpilation to Babel.

---

## 📐 Schema

The first thing needed to perform validation is a **schema** — a set of rules that have already been defined. Once a schema exists, it can be used to validate data against those rules.

Every schema in this guide is built the same way, and that shape never changes no matter how complex the rules get:

| Step | What it looks like |
| --- | --- |
| Declare the rules | `const schema = z.string().min(3).max(100)` |
| Feed it data | `schema.parse(request)` |
| Get back typed data | `result` is a `string`, guaranteed at runtime |

> **Key Insight:** A schema is a *value*, not a type — it exists at runtime, which is exactly why it can check data TypeScript never sees. The two worlds meet through `z.infer<typeof schema>`, which derives a static TypeScript type from the schema, so the rules are written once and both the compiler and the runtime honour them.

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

> **Note:** `z.email()` is a top-level schema in Zod v4. Plenty of tutorials still show the v3 form, `z.string().email()` — both validate an email address, but the v4 spelling is the one that matches the version installed here.

> **Tip:** `.min()` and `.max()` mean different things depending on the schema they hang off. On `z.string()` they bound the **length**; on `z.number()` they bound the **value**; on `z.array()` they bound the **item count**. Same method name, three different rules.

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

Coercion runs the value through JavaScript's own conversion functions before any rule is checked:

| Schema | Conversion used | `"true"` | `"false"` | `""` |
| --- | --- | --- | --- | --- |
| `z.coerce.string()` | `String(value)` | `"true"` | `"false"` | `""` |
| `z.coerce.number()` | `Number(value)` | `NaN` → fails | `NaN` → fails | `0` |
| `z.coerce.boolean()` | `Boolean(value)` | `true` | **`true`** | `false` |

> ⚠️ **Warning:** `z.coerce.boolean()` is JavaScript truthiness, not string parsing. Every non-empty string is `true` — so the string `"false"` coerces to `true`, and so do `"0"` and `"no"`. For a checkbox or a query-string flag, validate the literal instead (`z.enum(["true", "false"])`, then map it yourself) rather than trusting coercion.

> **Key Insight:** Coercion happens **before** validation, never after. `z.coerce.number().min(1000)` converts `"1000"` to the number `1000` first, and only then checks the minimum — which is why the `min`/`max` bounds are written against the converted type, not the incoming one.

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

> **Note:** `z.coerce.date()` accepts both a date string and a real `Date`, which is why the same schema handles `"1990-01-01"` and `new Date(1990, 10, 10)`. A plain `z.date()` would reject the string outright.

> **Gotcha:** `new Date(1980, 0, 1)` uses a **zero-based month** — `0` is January, not February. It also builds the date in the machine's local timezone, while the string `"1990-01-01"` is parsed as UTC. On a machine east or west of UTC those two can land on different calendar days, which matters when the bound sits right on the boundary.

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

> **Note:** `.issues` is an array because validation doesn't stop at the first failure. An object schema checks every field and reports all of them at once — which is what lets a form highlight all its invalid inputs in a single pass instead of one per submission.

> **Tip:** Each issue carries a `path` alongside its `message` — `["address", "postalCode"]` for a [nested object](#nested-object). That path is what maps an error back onto the field that produced it.

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

| | `parse()` | `safeParse()` |
| --- | --- | --- |
| On success | Returns the parsed data | Returns `{ success: true, data }` |
| On failure | Throws `ZodError` | Returns `{ success: false, error }` |
| Handled with | `try` / `catch` | An `if` on `result.success` |
| Best for | Data that *should* already be valid | Input that can legitimately be wrong |

> **Key Insight:** `result` is a **discriminated union** — checking `result.success` is what narrows it. Inside the `if`, TypeScript knows `result.data` exists; inside the `else`, it knows `result.error` does. Reaching for `result.data` before the check is a compile error, which is the whole point of the design.

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

> **Tip:** Stripping is the default, not the only option. `z.strictObject({ ... })` rejects unknown keys with an error instead, and `z.looseObject({ ... })` passes them through untouched. Reach for the strict variant on API payloads, where an unexpected field usually means the caller got something wrong.

> **Key Insight:** Stripping is a security feature, not just tidiness. Parsed output contains *only* what the schema declared, so an attacker can't smuggle an extra `isAdmin: true` into an object that later gets spread into a database write — the field is gone before your code ever sees it.

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

> **Note:** Nesting is just composition — `z.object()` accepts any schema as a property value, including another `z.object()`. The same applies in reverse, so `z.array(createUserSchema)` validates a list of these users without a single new rule being written.

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

| Collection | Schema | What `.min()` / `.max()` bound |
| --- | --- | --- |
| `Array<T>` | `z.array(itemSchema)` | Number of items |
| `Set<T>` | `z.set(itemSchema)` | Number of unique entries |
| `Map<K, V>` | `z.map(keySchema, valueSchema)` | Number of entries |

> **Note:** Every element is validated, not just the collection itself. `z.array(z.email()).min(1)` enforces the length *and* checks each item is a valid email — an invalid entry produces an issue whose `path` names the failing index.

> **Gotcha:** `z.set()` and `z.map()` expect a real `Set` or `Map` instance. A plain array or object fails validation outright, even when it holds the exact same data — which is why the examples above construct `new Set([...])` and `new Map([...])`.

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

> **Tip:** The message attaches to the specific rule it's passed to, not to the field as a whole. `z.string().min(6, "...").max(20, "...")` carries a different message for each bound — so tell the user which rule they broke, rather than repeating one generic sentence twice.

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

"Not required" has more than one meaning, and Zod gives each its own method:

| Method | Accepts | Resulting type |
| --- | --- | --- |
| `.optional()` | A missing key or `undefined` | `T \| undefined` |
| `.nullable()` | An explicit `null` | `T \| null` |
| `.nullish()` | Either of the above | `T \| null \| undefined` |
| `.default(v)` | A missing key, substituting `v` | `T` — never absent |

> **Key Insight:** `.optional()` and `.default()` look interchangeable but produce different types. An optional field forces every reader to handle `undefined`; a defaulted field is always present after parsing, so downstream code never checks at all. Pick the default whenever a sensible fallback exists — it removes the check rather than deferring it.

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

> **Note:** `transform()` runs only after the rules before it have passed, so the callback always receives a value of the validated type — no defensive checks needed inside it.

> **Gotcha:** A transform changes the *output* type while leaving the input type alone. `z.string().transform((v) => v.length)` takes a `string` and returns a `number`, so anything chained after the transform is working against the new type, not the original one. Order matters in the chain.

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

> **Gotcha:** `name: "dzaru"` is lowercase, so `mustUppercase` calls `ctx.addIssue(...)` and returns `z.NEVER`, which makes `loginSchema.parse(...)` throw a `ZodError`. That call isn't wrapped in `try/catch` here, so running this test as written currently fails with an uncaught `ZodError` — pass an already-uppercase `name` (e.g. `"DZARU"`), or wrap the call in `try/catch`/use `safeParse`, to see it resolve successfully instead.

> **Key Insight:** `return z.NEVER` is there for the *type checker*, not the runtime. `addIssue` has already recorded the failure, but the function still has to return something matching its declared return type — `z.NEVER` is the sentinel that says "this path produces no value," keeping the signature honest.

> **Tip:** When a rule only needs to *check* a value rather than reshape it, `.refine()` is the more direct tool: `z.string().refine((v) => v === v.toUpperCase(), "username must be uppercase")`. Reach for the `transform` + `RefinementCtx` form when one callback has to both validate and convert.

---

## 🔍 Validation Flow

Every `parse()` call in this guide runs the same pipeline. Knowing its order explains most of the behaviour above — especially which stage a given failure comes from:

```text
Unknown input                     a request body, a form field, a database row
  ↓
Coercion                          z.coerce.* — String(), Number(), Boolean(), new Date()
  ↓                               runs FIRST, so later rules see the converted type
Type check                        is it a string / number / Date / Set / Map at all?
  ↓                               wrong type → issue recorded, this branch stops
Rule checks                       .min() .max() .length() .email() .uuid()
  ↓                               every rule runs; failures accumulate, no early exit
Unknown-key handling              z.object() strips · strictObject rejects · looseObject keeps
  ↓
── Any issues so far? ──────────
  ↓ yes                           parse() throws ZodError · safeParse() returns { success: false }
  ↓ no
Transform                         .transform() — runs only on already-valid data
  ↓                               ctx.addIssue() here can still fail the parse
Refinement                        .refine() — custom checks on the final shape
  ↓
Validated, typed output           parse() returns it · safeParse() wraps it in { success: true, data }
```

What that order explains:

| Question | Answer |
| --- | --- |
| Why does `z.coerce.number().min(1000)` bound the number, not the string? | Coercion runs before any rule, so `min` is always checked against the converted value |
| Why does `"false"` coerce to `true`? | Coercion is plain `Boolean(value)`, and every non-empty string is truthy |
| Why does `.issues` hold several entries? | Rule checks don't stop at the first failure — they accumulate across every field |
| Why did the extra `ignore` key disappear? | Unknown-key handling strips it before the result is ever returned |
| Why does a `transform()` callback never need a type check? | It runs only after every preceding rule has passed |
| Why does `ctx.addIssue()` still fail the parse? | A transform sits inside the pipeline, so an issue raised there is collected like any other |
| Why does `safeParse()` never throw? | Only the reporting step differs — the same pipeline runs either way |

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
| **Nullable Field** | Allow an explicit `null` | `z.string().nullable()` |
| **Default Value** | Substitute a fallback when the key is missing | `z.string().default("guest")` |
| **Strict Object** | Reject unknown keys instead of stripping them | `z.strictObject({ ... })` |
| **Transform** | Reshape data after it passes validation | `z.string().transform((v) => v.toUpperCase())` |
| **Refine** | Add a custom check without reshaping | `z.string().refine(fn, "message")` |
| **Custom Validation** | Add validation logic Zod doesn't provide out of the box | `ctx.addIssue(...); return z.NEVER;` |
| **Inferred Type** | Derive a TypeScript type from a schema | `type Login = z.infer<typeof loginSchema>` |

---

## 💡 Best Practices

**✅ Do This**

- **Reach for `safeParse()`** instead of wrapping `parse()` in `try/catch` whenever a validation failure is an expected, regular code path (e.g. handling user input) — it avoids throwing for ordinary control flow
- **Read a caught `ZodError`'s `.issues` array** to inspect what failed and why
- **Reach for `z.coerce.*`** at the boundary where input naturally arrives as the wrong primitive type (query strings, form fields), instead of converting it by hand
- **Give schemas custom messages** (`z.string().min(6, "message")`) for user-facing fields, so failures are actionable instead of generic
- **Use `transform()` to reshape already-valid data**, and `RefinementCtx.addIssue()` inside a transform when a rule can't be expressed with schema methods alone
- **Prefer `.refine()` when a rule only checks** and doesn't convert — it says what it does, without the `z.NEVER` ceremony
- **Derive types with `z.infer<typeof schema>`** so the schema stays the single source of truth instead of being restated as a separate `interface`
- **Reach for `z.strictObject()` on API payloads**, where an unexpected key usually means the caller sent something wrong
- **Prefer `.default()` over `.optional()`** whenever a sensible fallback exists — it removes the `undefined` check rather than pushing it downstream
- **Validate at the boundary**, once, as data enters the application — everything past that point can trust its types

**❌ Avoid This**

- **Assuming an undeclared extra field survives `z.object()` parsing** — by default it's silently stripped, not merely ignored (see the `ignore` field in the object validation examples)
- **Calling `.errors()` on a caught `ZodError`** — in Zod v4 it's `.issues`, a plain array property, not a method
- **Leaving a `parse()` call unwrapped when the input can plausibly fail validation** — an uncaught `ZodError` propagates like any other exception (see the Custom Validation note above)
- **Trusting `z.coerce.boolean()` to read `"true"` and `"false"`** — it's JavaScript truthiness, so every non-empty string becomes `true`
- **Treating a TypeScript type as a runtime guarantee** — annotations are erased at compile time, which is the entire reason this library exists
- **Restating a schema as a separate `interface`** — the two drift apart; infer the type from the schema instead
- **Passing a plain array or object to `z.set()` / `z.map()`** — they require real `Set` and `Map` instances
- **Trusting `npm test` to catch type errors** — this project's Jest transform (`babel-jest`) strips types without checking them; run `npx tsc --noEmit` to actually validate types

> Reference: [zod.dev](https://zod.dev/) · [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html) · [tsconfig reference](https://www.typescriptlang.org/tsconfig)
