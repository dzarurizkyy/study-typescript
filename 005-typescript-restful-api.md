# 🚀 TypeScript RESTful API — Contact Management

Study notes for building a RESTful API for **Contact Management** using TypeScript, Express, Prisma, and PostgreSQL. Split into 3 interdependent modules — **User Management** → **Contact Management** → **Address Management** — each built up stage by stage (endpoint by endpoint) so the learning path is clear: what gets built first, what comes next, and why that order matters.

---

## 📋 Table of Contents

- [Requirement](#-requirement)
- [Project Setup](#-project-setup)
- [Database Design](#-database-design)
- [Application Foundation](#-application-foundation)
- [Part 1 — User Management](#-part-1--user-management)
  - [Stage 1: Register User](#stage-1-register-user)
  - [Stage 2: Login User](#stage-2-login-user)
  - [Stage 3: Get Current User](#stage-3-get-current-user)
  - [Stage 4: Update User](#stage-4-update-user)
  - [Stage 5: Logout User](#stage-5-logout-user)
- [Part 2 — Contact Management](#-part-2--contact-management)
  - [Stage 1: Create Contact](#stage-1-create-contact)
  - [Stage 2: Get Contact](#stage-2-get-contact)
  - [Stage 3: Update & Delete Contact](#stage-3-update--delete-contact)
  - [Stage 4: Search Contact](#stage-4-search-contact)
- [Part 3 — Address Management](#-part-3--address-management)
  - [Stage 1: Create Address](#stage-1-create-address)
  - [Stage 2: Get Address](#stage-2-get-address)
  - [Stage 3: Update Address](#stage-3-update-address)
  - [Stage 4: Remove Address](#stage-4-remove-address)
  - [Stage 5: List Address](#stage-5-list-address)
- [Running the Project](#-running-the-project)
- [Manual Testing](#-manual-testing)
- [Quick Reference](#-quick-reference)
- [Key Takeaways](#-key-takeaways)

---

## 🎯 Requirement

The RESTful API for Contact Management has 3 major features:

1. **User Management** — register, login, view profile, update profile, logout
2. **Contact Management** — contact CRUD + search
3. **Address Management** — address CRUD, an address always belongs to a contact

**User Data**: `username`, `password`, `name`

**User API**: Register User, Login User, Update User, Get User, Logout User

**Contact Data**: `first_name`, `last_name`, `email`, `phone`

**Contact API**: Create Contact, Update Contact, Get Contact, Search Contact, Remove Contact

**Contact Address Data**: `street`, `city`, `province`, `country`, `postal_code`

**Address API**: Create Address, Update Address, Get Address, List Address, Remove Address

> **Key Insight:** The relationship forms an ownership chain: `User → Contact → Address`. A contact **must** belong to the currently logged-in user, and an address **must** belong to a valid contact. This `checkXMustExist` pattern repeats across all three modules and is the backbone of data authorization throughout the API.

---

## 🏗️ Project Setup

### Creating the Project

```bash
mkdir belajar-typescript-restful-api
cd belajar-typescript-restful-api
npm init
```

### Adding Zod

For request body/query validation.

```bash
npm install zod
```

### Adding ExpressJS

```bash
npm install express
npm install --save-dev @types/express
```

> Reference: [npmjs.com/package/express](https://www.npmjs.com/package/express)

### Adding Prisma

ORM for talking to PostgreSQL.

```bash
npm install --save-dev prisma
```

> Reference: [prisma.io](https://www.prisma.io/)

### Adding Winston

For structured logging.

```bash
npm install winston
```

> Reference: [npmjs.com/package/winston](https://www.npmjs.com/package/winston)

### Adding BCrypt

For password hashing.

```bash
npm install bcrypt
npm install --save-dev @types/bcrypt
```

> Reference: [npmjs.com/package/bcrypt](https://www.npmjs.com/package/bcrypt)

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

### Setting Up TypeScript for Jest

```bash
npm install --save-dev @babel/preset-typescript
npm install --save-dev @jest/globals
```

> Reference: [jestjs.io/docs/getting-started#using-typescript](https://jestjs.io/docs/getting-started#using-typescript)

### Adding Supertest

For testing HTTP endpoints directly against the Express app without needing a running server.

```bash
npm install --save-dev supertest @types/supertest
```

> Reference: [npmjs.com/package/supertest](https://www.npmjs.com/package/supertest)

### Adding TypeScript

```bash
npm install --save-dev typescript
```

> Reference: [npmjs.com/package/typescript](https://www.npmjs.com/package/typescript)

### Setting Up the TypeScript Project

```bash
npx tsc --init
```

All compiler configuration is generated into `tsconfig.json`. What needs to change:

- Change `"module"` to `"commonjs"`
- Change `"moduleResolution"` to `"Node"`
- Add `"include": ["src/**/*"]`
- Change `"rootDir"` to `"./src"` and `"outDir"` to `"./dist"`

**`tsconfig.json`** (final)

```json
{
  "include": ["src/**/*"],
  "compilerOptions": {
    "rootDir": "./src",
    "outDir": "./dist",
    "module": "commonjs",
    "target": "es6",
    "types": ["jest"],
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "sourceMap": true,
    "declaration": true,
    "declarationMap": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": false,
    "strict": true,
    "jsx": "react-jsx",
    "isolatedModules": true,
    "noUncheckedSideEffectImports": true,
    "moduleDetection": "force",
    "skipLibCheck": true
  }
}
```

---

## 🗄️ Database Design

### Setting Up the Database

```
dzarurizky@MacBook-Air-Dzaru test % psql -h localhost -p 5432 -U postgres
Password for user postgres:
psql (17.9 (Homebrew), server 17.1)
Type "help" for help.

postgres=# create database study_typescript_restful_api;
CREATE DATABASE
postgres=# \c study_typescript_restful_api
psql (17.9 (Homebrew), server 17.1)
You are now connected to database "study_typescript_restful_api" as user "postgres".
```

### Setting Up Prisma

```bash
npx prisma init
```

**`prisma.config.ts`**

```typescript
// This file was generated by Prisma and assumes you have installed the following:
// npm install --save-dev prisma dotenv
import "dotenv/config";
import { defineConfig, env } from "prisma/config";

export default defineConfig({
  schema: "prisma/schema.prisma",
  migrations: {
    path: "prisma/migrations",
  },
  engine: "classic",
  datasource: {
    url: env("DATABASE_URL"),
  },
});
```

### User Model

```prisma
generator client {
  provider = "prisma-client"
  output   = "../src/generated/prisma"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  username String  @id @db.VarChar(100)
  password String  @db.VarChar(100)
  name     String  @db.VarChar(100)
  token    String? @db.VarChar(100)

  @@map("users")
}
```

```bash
npx prisma migrate dev --name add_user_model
```

```sql
-- CreateTable
CREATE TABLE "users" (
    "username" VARCHAR(100) NOT NULL,
    "password" VARCHAR(100) NOT NULL,
    "name" VARCHAR(100) NOT NULL,
    "token" VARCHAR(100),

    CONSTRAINT "users_pkey" PRIMARY KEY ("username")
);
```

### Contact Model

Contact needs a relation to `User` (one user has many contacts), so add a two-way relation to the schema.

```prisma
model User {
  username String  @id @db.VarChar(100)
  password String  @db.VarChar(100)
  name     String  @db.VarChar(100)
  token    String? @db.VarChar(100)

  contacts Contact[]

  @@map("users")
}

model Contact {
  id         Int     @id @default(autoincrement())
  first_name String  @db.VarChar(100)
  last_name  String? @db.VarChar(100)
  email      String? @db.VarChar(100)
  phone      String? @db.VarChar(20)
  username   String  @db.VarChar(100)

  user User? @relation(fields: [username], references: [username])

  @@map("contacts")
}
```

```bash
npx prisma migrate dev --name add_contact_model
```

```sql
-- CreateTable
CREATE TABLE "contacts" (
    "id" SERIAL NOT NULL,
    "first_name" VARCHAR(100) NOT NULL,
    "last_name" VARCHAR(100),
    "email" VARCHAR(100),
    "phone" VARCHAR(20),
    "username" VARCHAR(100) NOT NULL,

    CONSTRAINT "contacts_pkey" PRIMARY KEY ("id")
);

-- AddForeignKey
ALTER TABLE "contacts" ADD CONSTRAINT "contacts_username_fkey" FOREIGN KEY ("username") REFERENCES "users"("username") ON DELETE RESTRICT ON UPDATE CASCADE;
```

### Address Model

Address attaches to `Contact` (one contact has many addresses).

```prisma
model Contact {
  id         Int     @id @default(autoincrement())
  first_name String  @db.VarChar(100)
  last_name  String? @db.VarChar(100)
  email      String? @db.VarChar(100)
  phone      String? @db.VarChar(20)
  username   String  @db.VarChar(100)

  user      User?     @relation(fields: [username], references: [username])
  addresses Address[]

  @@map("contacts")
}

model Address {
  id          Int    @id @default(autoincrement())
  contact_id  Int    @db.Integer
  street      String @db.VarChar(255)
  city        String @db.VarChar(100)
  province    String @db.VarChar(100)
  country     String @db.VarChar(100)
  postal_code String @db.VarChar(10)

  contact Contact? @relation(fields: [contact_id], references: [id])

  @@map("addresses")
}
```

```bash
npx prisma migrate dev --name address_model
```

```sql
-- CreateTable
CREATE TABLE "addresses" (
    "id" SERIAL NOT NULL,
    "contact_id" INTEGER NOT NULL,
    "street" VARCHAR(255) NOT NULL,
    "city" VARCHAR(100) NOT NULL,
    "province" VARCHAR(100) NOT NULL,
    "country" VARCHAR(100) NOT NULL,
    "postal_code" VARCHAR(10) NOT NULL,

    CONSTRAINT "addresses_pkey" PRIMARY KEY ("id")
);

-- AddForeignKey
ALTER TABLE "addresses" ADD CONSTRAINT "addresses_contact_id_fkey" FOREIGN KEY ("contact_id") REFERENCES "contacts"("id") ON DELETE RESTRICT ON UPDATE CASCADE;
```

Later, `street`, `city`, and `province` were made optional (some addresses only have a country + postal code, e.g. certain foreign address formats) — `country` and `postal_code` stay required:

```prisma
model Address {
  id          Int     @id @default(autoincrement())
  contact_id  Int     @db.Integer
  street      String? @db.VarChar(255)
  city        String? @db.VarChar(100)
  province    String? @db.VarChar(100)
  country     String  @db.VarChar(100)
  postal_code String  @db.VarChar(10)

  contact Contact? @relation(fields: [contact_id], references: [id])

  @@map("addresses")
}
```

```bash
npx prisma migrate dev --name change_optional_at_address_table
```

```sql
-- AlterTable
ALTER TABLE "addresses" ALTER COLUMN "street" DROP NOT NULL,
ALTER COLUMN "city" DROP NOT NULL,
ALTER COLUMN "province" DROP NOT NULL;
```

> ⚠️ **Note:** this `NOT NULL` → nullable change is a plain additive migration (`ALTER TABLE`), not a `prisma migrate reset`. If the table already has data, `ALTER COLUMN ... DROP NOT NULL` is safe to run without losing data.

---

## ⚙️ Application Foundation

Before touching features, set up the infrastructure layer shared by every module: the database connection, the logger, error handling, and a generic validation helper.

**`src/application/logging.ts`**

```typescript
import winston from "winston";

const consoleFormat = winston.format.printf(({ level, message }) => {
  const text =
    typeof message === "object" ? JSON.stringify(message) : message;
  return `${level}: ${text}`;
});

export const logger = winston.createLogger({
  level: "debug",
  format: winston.format.json(),
  defaultMeta: { service: "user-service" },
  transports: [
    new winston.transports.Console({
      format: consoleFormat,
    }),
  ],
});
```

**`src/application/database.ts`**

```typescript
import { PrismaClient } from "../generated/prisma/client";
import { logger } from "./logging";

export const prismaClient = new PrismaClient({
  log: [
    { emit: "event", level: "query" },
    { emit: "event", level: "error" },
    { emit: "event", level: "warn" },
    { emit: "event", level: "info" },
  ],
});

prismaClient.$on("error", (e) => {
  logger.error(e);
});

prismaClient.$on("warn", (e) => {
  logger.warn(e);
});

prismaClient.$on("info", (e) => {
  logger.info(e);
});

prismaClient.$on("query", (e) => {
  logger.debug(e);
});
```

- The Prisma client is logged through Winston rather than `console.log` — every query, warning, and error flows through the same logging pipeline.

**`src/application/web.ts`** (initial version — public router first, the protected router follows in [Stage 3: Get Current User](#stage-3-get-current-user))

```typescript
import express from "express";

export const web = express();

web.use(express.json());
```

**`src/error/response-error.ts`**

```typescript
export class ResponseError extends Error {
  constructor(public status: number, public message: string) {
    super(message);
  }
}
```

**`src/validation/validation.ts`**

```typescript
import { ZodType } from "zod";

export class Validation {
  static validate<T>(schema: ZodType<T>, data: T): T {
    try {
      return schema.parse(data);
    } catch (error) {
      throw error;
    }
  }
}
```

> **Key Insight:** `Validation.validate` is a thin generic wrapper around `schema.parse`. Because it's generic (`<T>`), the same method validates any request shape — `RegisterUserRequest`, `CreateContactRequest`, `CreateAddressRequest` — without a separate validation function per model. Zod's error (`ZodError`) is deliberately re-thrown so it can be caught centrally in `error-middleware.ts`.

**`src/main.ts`**

```typescript
import "dotenv/config";
import { web } from "./application/web";
import { logger } from "./application/logging";

const port = process.env.PORT ? Number(process.env.PORT) : 3000;

web.listen(port, () => {
  logger.info(`Server is running on port ${port}`);
});
```

---

## 👤 Part 1 — User Management

### User API Spec

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| POST | `/api/users` | - | Register a new user |
| POST | `/api/users/login` | - | Login, receive a token |
| GET | `/api/users/current` | ✅ | Get the logged-in user's data |
| PATCH | `/api/users/current` | ✅ | Update name/password |
| DELETE | `/api/users/logout` | ✅ | Clear the token (logout) |

<details>
<summary>Request/response detail for each endpoint</summary>

**Register User** — `POST /api/users`

```json
// Request
{
  "username": "dzarurizkyy",
  "password": "dzaru2024",
  "name": "Dzaru Rizky Fathan Fortuna"
}
```

```json
// Response (Success)
{
  "data": {
    "username": "dzarurizkyy",
    "name": "Dzaru Rizky Fathan Fortuna"
  }
}
```

```json
// Response (Failed)
{
  "errors": "username must not blank, ..."
}
```

**Login User** — `POST /api/users/login`

```json
// Request
{
  "username": "dzarurizkyy",
  "password": "dzaru2024"
}
```

```json
// Response (Success)
{
  "data": {
    "username": "dzarurizkyy",
    "name": "Dzaru Rizky Fathan Fortuna",
    "token": ""
  }
}
```

```json
// Response (Failed)
{
  "errors": "username or password wrong, ..."
}
```

**Get User** — `GET /api/users/current` — Header: `x-api-token: [TOKEN]`

```json
// Response (Success)
{
  "data": {
    "username": "dzarurizkyy",
    "name": "Dzaru Rizky Fathan Fortuna"
  }
}
```

```json
// Response (Failed)
{
  "errors": "Unauthorized, ...."
}
```

**Update User** — `PATCH /api/users/current` — Header: `x-api-token: [TOKEN]`

```json
// Request (all fields optional)
{
  "name": "Dzaru Rizky Fathan Fortuna",
  "password": "dzaru2024"
}
```

**Logout User** — `DELETE /api/users/current` — Header: `x-api-token: [TOKEN]`

```json
// Response (Success)
{ "data": "OK" }
```

</details>

> The full spec is also kept in [`doc/user.md`](doc/user.md).

---

### Stage 1: Register User

The most basic feature: a new user signs up with username, password, name. The password must be hashed, and the username must be unique.

**`src/model/user-model.ts`**

```typescript
import { User } from "../generated/prisma/client";

export type UserResponse = {
  username: string;
  name: string;
  token?: string;
};

export type CreateUserRequest = {
  username: string;
  name: string;
  password: string;
};

export function toUserResponse(user: User): UserResponse {
  return {
    name: user.name,
    username: user.username,
  };
}
```

**`src/validation/user-validation.ts`**

```typescript
import z, { ZodType } from "zod";

export class UserValidation {
  static readonly REGISTER = z.object({
    username: z.string().min(1).max(100),
    name: z.string().min(1).max(100),
    password: z.string().min(1).max(100),
  });
}
```

**`src/service/user-service.ts`**

```typescript
import bcrypt from "bcrypt";
import { prismaClient } from "../application/database";
import { ResponseError } from "../error/response-error";
import { CreateUserRequest, toUserResponse, UserResponse } from "../model/user-model";
import { UserValidation } from "../validation/user-validation";
import { Validation } from "../validation/validation";

export class UserService {
  static async register(request: CreateUserRequest): Promise<UserResponse> {
    const registerRequest = Validation.validate(UserValidation.REGISTER, request);

    const totalUserWithSameUsername = await prismaClient.user.count({
      where: {
        username: registerRequest.username,
      },
    });

    if (totalUserWithSameUsername !== 0) {
      throw new ResponseError(400, "Username already existt");
    }

    registerRequest.password = await bcrypt.hash(registerRequest.password, 10);

    const user = await prismaClient.user.create({
      data: registerRequest,
    });

    return toUserResponse(user);
  }
}
```

- The password is **never** returned to the client — `toUserResponse` only maps `username` and `name`.
- The `count` check runs before `create` to prevent two users from sharing a username.

**`src/controller/user-controller.ts`**

```typescript
import { NextFunction, Request, Response } from "express";
import { CreateUserRequest } from "../model/user-model";
import { UserService } from "../service/user-service";

export class UserController {
  static async register(req: Request, res: Response, next: NextFunction) {
    try {
      const request: CreateUserRequest = req.body as CreateUserRequest;
      const response = await UserService.register(request);
      res.status(200).json({
        data: response,
      });
    } catch (e) {
      next(e);
    }
  }
}
```

**`src/route/public-api.ts`**

```typescript
import express from "express";
import { UserController } from "../controller/user-controller";

export const publicRouter = express.Router();
publicRouter.post("/api/users", UserController.register);
```

**`src/middleware/error-middleware.ts`**

```typescript
import { ErrorRequestHandler, Request, Response, NextFunction } from "express";
import { ZodError } from "zod";
import { ResponseError } from "../error/response-error";

export const errorMiddleware = async (error: ErrorRequestHandler, req: Request, res: Response, next: NextFunction) => {
  if (error instanceof ZodError) {
    res.status(400).json({
      errors: `Validation Error: ${JSON.stringify(error.issues)}`,
    });
  } else if (error instanceof ResponseError) {
    res.status(error.status).json({
      errors: error.message,
    });
  } else {
    res.status(500).json({
      errors: "Internal Server Error",
    });
  }
};
```

**`src/application/web.ts`** (add `publicRouter` + `errorMiddleware`)

```typescript
import express from "express";
import { publicRouter } from "../route/public-api";
import { errorMiddleware } from "../middleware/error-middleware";

export const web = express();

web.use(express.json());
web.use(publicRouter);
web.use(errorMiddleware);
```

**`src/test/test-util.ts`**

```typescript
import { prismaClient } from "../application/database";
import bcrypt from "bcrypt";

export class UserTest {
  static async delete() {
    await prismaClient.user.deleteMany({
      where: {
        username: "test",
      },
    });
  }

  static async create() {
    await prismaClient.user.create({
      data: {
        username: "test",
        password: await bcrypt.hash("test", 10),
        name: "test",
      },
    });
  }
}
```

**`src/test/user.test.ts`**

```typescript
import supertest from "supertest";
import { web } from "../application/web";
import { logger } from "../application/logging";
import { UserTest } from "./test-util";

describe("POST /api/users", () => {
  afterEach(async () => {
    await UserTest.delete();
  });

  it("should reject register new user if request is invalid", async () => {
    const response = await supertest(web).post("/api/users").send({
      username: "",
      password: "",
      name: "",
    });

    logger.debug(response.body);
    expect(response.status).toBe(400);
    expect(response.body.errors).toBeDefined();
  });

  it("should register new user", async () => {
    const response = await supertest(web).post("/api/users").send({
      username: "test",
      password: "test",
      name: "test",
    });

    logger.debug(response.body);
    expect(response.status).toBe(200);
    expect(response.body.data.username).toBe("test");
    expect(response.body.data.name).toBe("test");
    expect(response.body.data.password).toBeUndefined();
  });
});
```

---

### Stage 2: Login User

After registering, a user needs to log in to get a **token** — this token is the key to accessing every protected endpoint that follows (contacts & addresses). Login needs to generate a random token, so add the `uuid` package first:

```bash
npm install uuid
npm install --save-dev @types/uuid
```

**`src/model/user-model.ts`** (add `LoginUserRequest`)

```typescript
export type LoginUserRequest = {
  username: string;
  password: string;
};
```

**`src/validation/user-validation.ts`** (add `LOGIN`)

```typescript
export class UserValidation {
  static readonly REGISTER = z.object({
    username: z.string().min(1).max(100),
    name: z.string().min(1).max(100),
    password: z.string().min(1).max(100),
  });

  static readonly LOGIN = z.object({
    username: z.string().min(1).max(100),
    password: z.string().min(1).max(100),
  });
}
```

**`src/service/user-service.ts`** (add the `login` method)

```typescript
import { v4 as uuid } from "uuid";

export class UserService {
  // ...register above...

  static async login(request: LoginUserRequest): Promise<UserResponse> {
    const loginRequest = Validation.validate(UserValidation.LOGIN, request);

    let user = await prismaClient.user.findUnique({
      where: {
        username: loginRequest.username,
      },
    });

    if (!user) {
      throw new ResponseError(401, "Username or password wrong");
    }

    const isPasswordValid = await bcrypt.compare(
      loginRequest.password,
      user.password,
    );
    if (!isPasswordValid) {
      throw new ResponseError(401, "Username or password wrong");
    }

    user = await prismaClient.user.update({
      where: {
        username: loginRequest.username,
      },
      data: {
        token: uuid(),
      },
    });

    const response = toUserResponse(user);
    response.token = user.token!;

    return response;
  }
}
```

> **Key Insight:** The error message for a wrong username and a wrong password is **deliberately the same** (`"Username or password wrong"`). If they differed, an attacker could tell which usernames are valid just from the difference in error messages (user enumeration).

**`src/controller/user-controller.ts`** (add `login`)

```typescript
static async login(req: Request, res: Response, next: NextFunction) {
  try {
    const request: LoginUserRequest = req.body as LoginUserRequest;
    const response = await UserService.login(request);
    res.status(200).json({
      data: response,
    });
  } catch (e) {
    next(e);
  }
}
```

**`src/route/public-api.ts`**

```typescript
export const publicRouter = express.Router();
publicRouter.post("/api/users", UserController.register);
publicRouter.post("/api/users/login", UserController.login);
```

**`src/test/user.test.ts`** (add `describe("POST /api/users/login")`)

```typescript
describe("POST /api/users/login", () => {
  beforeEach(async () => {
    await UserTest.create();
  });

  afterEach(async () => {
    await UserTest.delete();
  });

  it("should reject login if request is invalid", async () => {
    const response = await supertest(web).post("/api/users/login").send({
      username: "",
      password: "",
    });

    expect(response.status).toBe(400);
    expect(response.body.errors).toBeDefined();
  });

  it("should reject login if password is wrong", async () => {
    const response = await supertest(web).post("/api/users/login").send({
      username: "test",
      password: "wrongpassword",
    });

    expect(response.status).toBe(401);
    expect(response.body.errors).toBeDefined();
  });

  it("should login", async () => {
    const response = await supertest(web).post("/api/users/login").send({
      username: "test",
      password: "test",
    });

    expect(response.status).toBe(200);
    expect(response.body.data.username).toBe("test");
    expect(response.body.data.name).toBe("test");
    expect(response.body.data.token).toBeDefined();
  });
});
```

---

### Stage 3: Get Current User

The first endpoint that requires **authentication**. This is where the router splits into two: `publicRouter` (no token needed) and `apiRouter` (token required), guarded by `authMiddleware`.

**`src/type/user-request.ts`**

```typescript
import { Request } from "express";
import { User } from "../generated/prisma/client";

export interface UserRequest extends Request {
  user?: User;
}
```

**`src/middleware/auth-middleware.ts`**

```typescript
import { prismaClient } from "../application/database";
import { ResponseError } from "../error/response-error";
import { NextFunction, Request, Response } from "express";
import { UserRequest } from "../type/user-request";

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

> **Key Insight:** `authMiddleware` attaches the logged-in `user` to `req.user` (via the `UserRequest` interface, which extends `Request`). Every controller behind this middleware just reads `req.user!` — no need to query who's logged in again.

**`src/service/user-service.ts`** (add the `get` method)

```typescript
import { User } from "../generated/prisma/client";

export class UserService {
  // ...register, login above...

  static async get(user: User): Promise<UserResponse> {
    return toUserResponse(user);
  }
}
```

**`src/controller/user-controller.ts`** (add `get`)

```typescript
import { UserRequest } from "../type/user-request";

static async get(req: UserRequest, res: Response, next: NextFunction) {
  try {
    const response = await UserService.get(req.user!);
    res.status(200).json({
      data: response,
    });
  } catch (e) {
    next(e);
  }
}
```

**`src/route/api.ts`** (a new router, dedicated to protected endpoints)

```typescript
import express from "express";
import { authMiddleware } from "../middleware/auth-middleware";
import { UserController } from "../controller/user-controller";

export const apiRouter = express.Router();
apiRouter.use(authMiddleware);
apiRouter.get("/api/users/current", UserController.get);
```

**`src/application/web.ts`** (mount `apiRouter` after `publicRouter`)

```typescript
import express from "express";
import { publicRouter } from "../route/public-api";
import { errorMiddleware } from "../middleware/error-middleware";
import { apiRouter } from "../route/api";

export const web = express();

web.use(express.json());
web.use(publicRouter);
web.use(apiRouter);
web.use(errorMiddleware);
```

**`src/test/user.test.ts`** (add `describe("GET /api/users/current")`)

```typescript
describe("GET /api/users/current", () => {
  beforeEach(async () => {
    await UserTest.create();
  });

  afterEach(async () => {
    await UserTest.delete();
  });

  it("should get current user", async () => {
    const response = await supertest(web)
      .get("/api/users/current")
      .set("x-api-token", "test");

    expect(response.status).toBe(200);
    expect(response.body.data.username).toBe("test");
    expect(response.body.data.name).toBe("test");
    expect(response.body.data.token).toBeUndefined();
  });

  it("should reject get current user if token is invalid", async () => {
    const response = await supertest(web)
      .get("/api/users/current")
      .set("x-api-token", "invalid-token");

    expect(response.status).toBe(401);
    expect(response.body.errors).toBeDefined();
  });
});
```

> Note: `UserTest.create()` at this stage is set with `token: "test"` so requests in tests can authenticate immediately without logging in first — see the final `test-util.ts` in [Stage 4](#stage-4-update-user).

---

### Stage 4: Update User

**`src/validation/user-validation.ts`** (add `UPDATE`, all fields optional)

```typescript
static readonly UPDATE = z.object({
  name: z.string().min(1).max(100).optional(),
  password: z.string().min(1).max(100).optional(),
});
```

**`src/model/user-model.ts`** (add `UpdateUserRequest`)

```typescript
export type UpdateUserRequest = {
  name?: string;
  password?: string;
};
```

**`src/service/user-service.ts`** (add the `update` method)

```typescript
static async update(
  user: User,
  request: UpdateUserRequest,
): Promise<UserResponse> {
  const updateRequest = Validation.validate(UserValidation.UPDATE, request);

  if (updateRequest.name) {
    user.name = updateRequest.name;
  }

  if (updateRequest.password) {
    user.password = await bcrypt.hash(updateRequest.password, 10);
  }

  const result = await prismaClient.user.update({
    where: {
      username: user.username,
    },
    data: {
      name: user.name,
      password: user.password,
    },
  });

  return toUserResponse(result);
}
```

- Since `name` and `password` are both optional, this method only overwrites the fields that were actually sent — it uses the existing `user` as a base and patches over it wherever a new field is present.

**`src/controller/user-controller.ts`** (add `update`)

```typescript
static async update(req: UserRequest, res: Response, next: NextFunction) {
  try {
    const request: UpdateUserRequest = req.body as UpdateUserRequest;
    const response = await UserService.update(req.user!, request);
    res.status(200).json({
      data: response,
    });
  } catch (e) {
    next(e);
  }
}
```

**`src/route/api.ts`**

```typescript
apiRouter.get("/api/users/current", UserController.get);
apiRouter.patch("/api/users/current", UserController.update);
```

**`src/test/test-util.ts`** (final User version — add `token: "test"` to `create()` and add `get()`)

```typescript
import { prismaClient } from "../application/database";
import bcrypt from "bcrypt";
import { User } from "../generated/prisma/client";

export class UserTest {
  static async delete() {
    await prismaClient.user.deleteMany({
      where: {
        username: "test",
      },
    });
  }

  static async create() {
    await prismaClient.user.create({
      data: {
        username: "test",
        password: await bcrypt.hash("test", 10),
        name: "test",
        token: "test",
      },
    });
  }

  static async get(): Promise<User> {
    const user = await prismaClient.user.findFirst({
      where: {
        username: "test",
      },
    });

    if (!user) {
      throw new Error("User not found");
    }

    return user;
  }
}
```

**`src/test/user.test.ts`** (add `describe("PATCH /api/users/current")`)

```typescript
describe("PATCH /api/users/current", () => {
  beforeEach(async () => {
    await UserTest.create();
  });

  afterEach(async () => {
    await UserTest.delete();
  });

  it("should reject update current user if token is invalid", async () => {
    const response = await supertest(web)
      .patch("/api/users/current")
      .set("x-api-token", "invalid-token")
      .send({ name: "new-test", password: "new-password" });

    expect(response.status).toBe(401);
  });

  it("should reject update current user if request is invalid", async () => {
    const response = await supertest(web)
      .patch("/api/users/current")
      .set("x-api-token", "test")
      .send({ name: "", password: "" });

    expect(response.status).toBe(400);
  });

  it("should update current user", async () => {
    const response = await supertest(web)
      .patch("/api/users/current")
      .set("x-api-token", "test")
      .send({ name: "new-test", password: "new-password" });

    expect(response.status).toBe(200);
    expect(response.body.data.name).toBe("new-test");
  });

  it("should update current user if only name is provided", async () => {
    const response = await supertest(web)
      .patch("/api/users/current")
      .set("x-api-token", "test")
      .send({ name: "new-test" });

    expect(response.status).toBe(200);
    expect(response.body.data.name).toBe("new-test");
  });

  it("should update current user if only password is provided", async () => {
    const response = await supertest(web)
      .patch("/api/users/current")
      .set("x-api-token", "test")
      .send({ password: "new-password" });

    const user = await UserTest.get();
    expect(response.status).toBe(200);
    expect(await bcrypt.compare("new-password", user.password)).toBe(true);
  });
});
```

---

### Stage 5: Logout User

The final endpoint of User Management: clear the token so the user has to log in again to get a new one.

**`src/service/user-service.ts`** (add the `logout` method)

```typescript
static async logout(user: User): Promise<UserResponse> {
  const result = await prismaClient.user.update({
    where: {
      username: user.username,
    },
    data: {
      token: null,
    },
  });

  return toUserResponse(result);
}
```

**`src/controller/user-controller.ts`** (add `logout`)

```typescript
static async logout(req: UserRequest, res: Response, next: NextFunction) {
  try {
    await UserService.logout(req.user!);
    res.status(200).json({
      data: "OK",
    });
  } catch (e) {
    next(e);
  }
}
```

**`src/route/api.ts`** (final — all User API routes)

```typescript
export const apiRouter = express.Router();
apiRouter.use(authMiddleware);

// User API
apiRouter.get("/api/users/current", UserController.get);
apiRouter.patch("/api/users/current", UserController.update);
apiRouter.delete("/api/users/logout", UserController.logout);
```

**`src/test/user.test.ts`** (add `describe("DELETE /api/users/logout")`)

```typescript
describe("DELETE /api/users/logout", () => {
  beforeEach(async () => {
    await UserTest.create();
  });

  afterEach(async () => {
    await UserTest.delete();
  });

  it("should reject logout if token is invalid", async () => {
    const response = await supertest(web)
      .delete("/api/users/logout")
      .set("x-api-token", "invalid-token");

    expect(response.status).toBe(401);
    expect(response.body.errors).toBeDefined();
  });

  it("should logout", async () => {
    const response = await supertest(web)
      .delete("/api/users/logout")
      .set("x-api-token", "test");

    expect(response.status).toBe(200);
    expect(response.body.data).toBe("OK");

    const user = await UserTest.get();
    expect(user.token).toBeNull();
  });
});
```

With this, all of **User Management** is done: Register → Login → Get Current → Update → Logout.

---

## 📇 Part 2 — Contact Management

### Contact API Spec

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| POST | `/api/contacts` | ✅ | Create a new contact |
| GET | `/api/contacts/:contactId` | ✅ | Get one contact's detail |
| PUT | `/api/contacts/:contactId` | ✅ | Update a contact |
| DELETE | `/api/contacts/:contactId` | ✅ | Delete a contact |
| GET | `/api/contacts` | ✅ | Search + pagination |

> Full request/response spec lives in [`doc/contact.md`](doc/contact.md).

> **Key Insight:** Every Contact API sits behind `authMiddleware`, and every contact query always filters by `username: user.username`. This prevents user A from reading/modifying user B's contact even if they know the ID (Insecure Direct Object Reference).

---

### Stage 1: Create Contact

**`src/model/contact-model.ts`**

```typescript
import { Contact } from "../generated/prisma/client";

export type ContactResponse = {
  id: number;
  first_name: string;
  last_name?: string | null;
  email?: string | null;
  phone?: string | null;
};

export type CreateContactRequest = {
  first_name: string;
  last_name?: string;
  email?: string;
  phone?: string;
};

export function toContactResponse(contact: Contact): ContactResponse {
  return {
    id: contact.id,
    first_name: contact.first_name,
    last_name: contact.last_name,
    email: contact.email,
    phone: contact.phone,
  };
}
```

**`src/validation/contact-validation.ts`**

```typescript
import { z } from "zod";

export class ContactValidation {
  static readonly CREATE = z.object({
    first_name: z.string().min(1).max(100),
    last_name: z.string().min(1).max(100).optional(),
    email: z.email().min(1).max(100).optional(),
    phone: z.string().min(1).max(100).optional(),
  });
}
```

**`src/service/contact-service.ts`**

```typescript
import { ContactResponse, CreateContactRequest, toContactResponse } from "../model/contact-model";
import { Validation } from "../validation/validation";
import { User } from "../generated/prisma/client";
import { ContactValidation } from "../validation/contact-validation";
import { prismaClient } from "../application/database";

export class ContactService {
  static async create(user: User, request: CreateContactRequest): Promise<ContactResponse> {
    const createRequest = Validation.validate(ContactValidation.CREATE, request);

    const record = {
      ...createRequest,
      ...{ username: user.username },
    };

    const contact = await prismaClient.contact.create({
      data: record,
    });

    return toContactResponse(contact);
  }
}
```

- `username` is injected from the logged-in `user` (`req.user!` from `authMiddleware`), **not** from the request body — a client can't create a contact under another user's name.

**`src/controller/contact-controller.ts`**

```typescript
import { NextFunction, Request, Response } from "express";
import { UserRequest } from "../type/user-request";
import { ContactService } from "../service/contact-service";
import { CreateContactRequest } from "../model/contact-model";

export class ContactController {
  static async create(req: UserRequest, res: Response, next: NextFunction) {
    try {
      const request: CreateContactRequest = req.body as CreateContactRequest;
      const response = await ContactService.create(req.user!, request);
      res.status(200).json({
        data: response,
      });
    } catch (e) {
      next(e);
    }
  }
}
```

**`src/route/api.ts`**

```typescript
// Contact API
apiRouter.post("/api/contacts", ContactController.create);
```

**`src/test/test-util.ts`** (add `ContactTest`)

```typescript
import { Contact } from "../generated/prisma/client";

export class ContactTest {
  static async create() {
    await prismaClient.contact.create({
      data: {
        first_name: "first",
        last_name: "last",
        email: "test@example.com",
        phone: "0812",
        username: "test",
      },
    });
  }

  static async get(): Promise<Contact> {
    const contact = await prismaClient.contact.findFirst({
      where: {
        user: {
          username: "test",
        },
      },
    });

    if (!contact) {
      throw new Error("Contact not found");
    }

    return contact;
  }

  static async deleteAll() {
    await prismaClient.contact.deleteMany({
      where: {
        user: {
          username: "test",
        },
      },
    });
  }
}
```

**`src/test/contact.test.ts`**

```typescript
import supertest from "supertest";
import { ContactTest, UserTest } from "./test-util";
import { web } from "../application/web";
import { logger } from "../application/logging";

describe("POST /api/contacts", () => {
  beforeEach(async () => {
    await UserTest.create();
  });

  afterEach(async () => {
    await ContactTest.deleteAll();
    await UserTest.delete();
  });

  it("should create new contact", async () => {
    const response = await supertest(web)
      .post("/api/contacts")
      .set("x-api-token", "test")
      .send({
        first_name: "first",
        last_name: "last",
        email: "example@test.com",
        phone: "0812",
      });

    expect(response.status).toBe(200);
    expect(response.body.data.id).toBeDefined();
    expect(response.body.data.first_name).toBe("first");
  });

  it("should reject create new contact if data is invalid", async () => {
    const response = await supertest(web)
      .post("/api/contacts")
      .set("x-api-token", "test")
      .send({ first_name: "", last_name: "", email: "", phone: "" });

    expect(response.status).toBe(400);
    expect(response.body.errors).toBeDefined();
  });
});
```

---

### Stage 2: Get Contact

**`src/service/contact-service.ts`** (add the `get` method)

```typescript
import { ResponseError } from "../error/response-error";

static async get(user: User, id: number): Promise<ContactResponse> {
  const contact = await prismaClient.contact.findUnique({
    where: {
      id: id,
      username: user.username,
    },
  });

  if (!contact) {
    throw new ResponseError(404, "Contact is not found");
  }

  return toContactResponse(contact);
}
```

**`src/controller/contact-controller.ts`** (add `get`)

```typescript
static async get(req: UserRequest, res: Response, next: NextFunction) {
  try {
    const contactId = parseInt(req.params.contactId as string);
    const response = await ContactService.get(req.user!, contactId);
    res.status(200).json({
      data: response,
    });
  } catch (e) {
    next(e);
  }
}
```

**`src/route/api.ts`**

```typescript
apiRouter.post("/api/contacts", ContactController.create);
apiRouter.get("/api/contacts/:contactId", ContactController.get);
```

**`src/test/contact.test.ts`** (add `describe("GET /api/contacts/:contactId")`)

```typescript
describe("GET /api/contacts/:contactId", () => {
  beforeEach(async () => {
    await UserTest.create();
    await ContactTest.create();
  });

  afterEach(async () => {
    await ContactTest.deleteAll();
    await UserTest.delete();
  });

  it("should get contact", async () => {
    const contact = await ContactTest.get();
    const response = await supertest(web)
      .get(`/api/contacts/${contact.id}`)
      .set("x-api-token", "test");

    expect(response.status).toBe(200);
    expect(response.body.data.id).toBe(contact.id);
  });

  it("should reject get contact if contact is not found", async () => {
    const response = await supertest(web)
      .get("/api/contacts/999")
      .set("x-api-token", "test");

    expect(response.status).toBe(404);
    expect(response.body.errors).toBeDefined();
  });
});
```

---

### Stage 3: Update & Delete Contact

This is where the `checkContactMustExist` pattern is introduced — a helper reused by `update`, `remove`, and later reused across modules by Address Management too.

**`src/model/contact-model.ts`** (add `UpdateContactRequest`)

```typescript
export type UpdateContactRequest = {
  id: number;
  first_name?: string;
  last_name?: string;
  email?: string;
  phone?: string;
};
```

**`src/validation/contact-validation.ts`** (add `UPDATE`)

```typescript
static readonly UPDATE = z.object({
  id: z.number().min(1).positive(),
  first_name: z.string().min(1).max(100).optional(),
  last_name: z.string().min(1).max(100).optional(),
  email: z.email().min(1).max(100).optional(),
  phone: z.string().min(1).max(100).optional(),
});
```

**`src/service/contact-service.ts`** (refactor `get` to use the new helper + add `update`, `remove`)

```typescript
export class ContactService {
  static async checkContactMustExist(username: string, contactId: number) {
    const contact = await prismaClient.contact.findUnique({
      where: {
        id: contactId,
        username: username,
      },
    });

    if (!contact) {
      throw new ResponseError(404, "Contact is not found");
    }

    return contact;
  }

  // ...create above...

  static async get(user: User, id: number): Promise<ContactResponse> {
    const contact = await this.checkContactMustExist(user.username, id);
    return toContactResponse(contact);
  }

  static async update(user: User, request: UpdateContactRequest): Promise<ContactResponse> {
    const updateRequest = Validation.validate(ContactValidation.UPDATE, request);

    await this.checkContactMustExist(user.username, updateRequest.id);

    const contact = await prismaClient.contact.update({
      where: {
        id: updateRequest.id,
        username: user.username,
      },
      data: updateRequest,
    });

    return toContactResponse(contact);
  }

  static async remove(user: User, id: number): Promise<ContactResponse> {
    await this.checkContactMustExist(user.username, id);

    const contact = await prismaClient.contact.delete({
      where: {
        id: id,
        username: user.username,
      },
    });

    return toContactResponse(contact);
  }
}
```

**`src/controller/contact-controller.ts`** (add `update`, `remove`)

```typescript
static async update(req: UserRequest, res: Response, next: NextFunction) {
  try {
    const contactId = parseInt(req.params.contactId as string);
    const request: UpdateContactRequest = req.body as UpdateContactRequest;
    request.id = contactId;

    const response = await ContactService.update(req.user!, request);
    res.status(200).json({
      data: response,
    });
  } catch (e) {
    next(e);
  }
}

static async remove(req: UserRequest, res: Response, next: NextFunction) {
  try {
    const contactId = parseInt(req.params.contactId as string);
    await ContactService.remove(req.user!, contactId);
    res.status(200).json({
      data: "OK",
    });
  } catch (e) {
    next(e);
  }
}
```

**`src/route/api.ts`**

```typescript
apiRouter.post("/api/contacts", ContactController.create);
apiRouter.get("/api/contacts/:contactId", ContactController.get);
apiRouter.put("/api/contacts/:contactId", ContactController.update);
apiRouter.delete("/api/contacts/:contactId", ContactController.remove);
```

**`src/test/contact.test.ts`** (add `describe("PUT ...")` and `describe("DELETE ...")`)

```typescript
describe("PUT /api/contacts/:contactId", () => {
  beforeEach(async () => {
    await UserTest.create();
    await ContactTest.create();
  });

  afterEach(async () => {
    await ContactTest.deleteAll();
    await UserTest.delete();
  });

  it("should update contact", async () => {
    const contact = await ContactTest.get();
    const response = await supertest(web)
      .put(`/api/contacts/${contact.id}`)
      .set("x-api-token", "test")
      .send({
        first_name: "new-first",
        last_name: "new-last",
        email: "new-email@test.com",
        phone: "08123",
      });

    expect(response.status).toBe(200);
    expect(response.body.data.first_name).toBe("new-first");
  });

  it("should reject update contact if contact is not found", async () => {
    const response = await supertest(web)
      .put("/api/contacts/999")
      .set("x-api-token", "test")
      .send({ first_name: "new-first" });

    expect(response.status).toBe(404);
  });
});

describe("DELETE /api/contacts/:contactId", () => {
  beforeEach(async () => {
    await UserTest.create();
    await ContactTest.create();
  });

  afterEach(async () => {
    await ContactTest.deleteAll();
    await UserTest.delete();
  });

  it("should delete contact", async () => {
    const contact = await ContactTest.get();
    const response = await supertest(web)
      .delete(`/api/contacts/${contact.id}`)
      .set("x-api-token", "test");

    expect(response.status).toBe(200);
    expect(response.body.data).toBe("OK");
  });

  it("should reject delete contact if contact is not found", async () => {
    const response = await supertest(web)
      .delete("/api/contacts/999")
      .set("x-api-token", "test");

    expect(response.status).toBe(404);
  });
});
```

---

### Stage 4: Search Contact

The most complex feature in Contact Management: dynamic filters (`name`, `email`, `phone` — all optional) plus pagination. This needs a generic `Pageable<T>` type so it can be reused by other modules later (Address, etc.) if they ever need paged lists too.

**`src/model/page.ts`**

```typescript
export type Paging = {
  size: number;
  total_page: number;
  current_page: number;
};

export type Pageable<T> = {
  data: Array<T>;
  paging: Paging;
};
```

**`src/model/contact-model.ts`** (add `SearchContactRequest`)

```typescript
export type SearchContactRequest = {
  name?: string;
  email?: string;
  phone?: string;
  page: number;
  size: number;
};
```

**`src/validation/contact-validation.ts`** (add `SEARCH`)

```typescript
static readonly SEARCH = z.object({
  name: z.string().min(1).optional(),
  email: z.string().min(1).optional(),
  phone: z.string().min(1).optional(),
  page: z.number().min(1).positive().default(1),
  size: z.number().min(1).positive().default(10),
});
```

**`src/service/contact-service.ts`** (add the `search` method)

```typescript
import { Prisma } from "../generated/prisma/client";
import { Pageable } from "../model/page";

static async search(user: User, request: SearchContactRequest): Promise<Pageable<ContactResponse>> {
  const searchRequest = Validation.validate(ContactValidation.SEARCH, request);
  const skip = (searchRequest.page - 1) * searchRequest.size;

  const filters: Prisma.ContactWhereInput[] = [];

  filters.push({ username: user.username });

  if (searchRequest.name) {
    filters.push({
      OR: [
        { first_name: { contains: searchRequest.name } },
        { last_name: { contains: searchRequest.name } },
      ],
    });
  }

  if (searchRequest.email) {
    filters.push({ email: { contains: searchRequest.email } });
  }

  if (searchRequest.phone) {
    filters.push({ phone: { contains: searchRequest.phone } });
  }

  const contacts = await prismaClient.contact.findMany({
    where: { AND: filters },
    take: searchRequest.size,
    skip: skip,
  });

  const total = await prismaClient.contact.count({
    where: { AND: filters },
  });

  return {
    data: contacts.map((contact) => toContactResponse(contact)),
    paging: {
      current_page: searchRequest.page,
      size: searchRequest.size,
      total_page: Math.ceil(total / searchRequest.size),
    },
  };
}
```

> **Key Insight:** Filters are built as an **array of conditions** (`Prisma.ContactWhereInput[]`) that only gets populated when the matching query param is present, then combined with `AND: filters`. This avoids nested `if/else` for every filter combination — `username: user.username` is always in the array so search can never leak another user's contacts.

**`src/controller/contact-controller.ts`** (add `search`)

```typescript
static async search(req: UserRequest, res: Response, next: NextFunction) {
  try {
    const request: SearchContactRequest = {
      name: req.query.name as string,
      email: req.query.email as string,
      phone: req.query.phone as string,
      page: req.query.page ? Number(req.query.page) : 1,
      size: req.query.size ? Number(req.query.size) : 10,
    };

    const response = await ContactService.search(req.user!, request);
    res.status(200).json({
      data: response.data,
      paging: response.paging,
    });
  } catch (error) {
    next(error);
  }
}
```

**`src/route/api.ts`** (final — all Contact API routes)

```typescript
// Contact API
apiRouter.post("/api/contacts", ContactController.create);
apiRouter.get("/api/contacts/:contactId", ContactController.get);
apiRouter.put("/api/contacts/:contactId", ContactController.update);
apiRouter.delete("/api/contacts/:contactId", ContactController.remove);
apiRouter.get("/api/contacts", ContactController.search);
```

**`src/test/contact.test.ts`** (add `describe("GET /api/contacts")`)

```typescript
describe("GET /api/contacts", () => {
  beforeEach(async () => {
    await UserTest.create();
    await ContactTest.create();
  });

  afterEach(async () => {
    await ContactTest.deleteAll();
    await UserTest.delete();
  });

  it("should get contacts without param", async () => {
    const response = await supertest(web)
      .get("/api/contacts")
      .set("x-api-token", "test");

    expect(response.status).toBe(200);
    expect(response.body.data.length).toBe(1);
    expect(response.body.paging.current_page).toBe(1);
  });

  it("should get contacts with name param", async () => {
    const response = await supertest(web)
      .get("/api/contacts")
      .query({ name: "first" })
      .set("x-api-token", "test");

    expect(response.status).toBe(200);
    expect(response.body.data.length).toBe(1);
  });

  it("should get contacts with page 2", async () => {
    const response = await supertest(web)
      .get("/api/contacts")
      .query({ page: 2, size: 10 })
      .set("x-api-token", "test");

    expect(response.status).toBe(200);
    expect(response.body.data.length).toBe(0);
    expect(response.body.paging.current_page).toBe(2);
  });
});
```

Contact Management is done: Create → Get → Update/Delete → Search.

---

## 🏠 Part 3 — Address Management

> ⚠️ **This part previously had no implementation notes at all** — only the API spec existed, with no walkthrough. The content below was reconstructed by scanning the source code directly (`src/model/address-model.ts`, `src/validation/address-validation.ts`, `src/service/address-service.ts`, `src/controller/address-controller.ts`, `src/test/address.test.ts`, `src/route/api.ts`, and the Prisma schema) so it's complete and consistent with the Contact Management pattern.

### Address API Spec

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| POST | `/api/contacts/:contactId/addresses` | ✅ | Create a new address for a contact |
| GET | `/api/contacts/:contactId/addresses/:addressId` | ✅ | Get one address' detail |
| PUT | `/api/contacts/:contactId/addresses/:addressId` | ✅ | Update an address |
| DELETE | `/api/contacts/:contactId/addresses/:addressId` | ✅ | Delete an address |
| GET | `/api/contacts/:contactId/addresses` | ✅ | List all addresses of a contact |

> Full request/response spec lives in [`doc/address.md`](doc/address.md).

> **Key Insight:** An address is always accessed **through its contact** — the URL is always shaped like `/api/contacts/:contactId/addresses/...`, never a bare `/api/addresses/:id`. This reinforces the `User → Contact → Address` ownership chain: before an address is checked, its contact must be checked first (and ownership of that contact already guarantees ownership of the addresses beneath it).

---

### Stage 1: Create Address

**`src/model/address-model.ts`**

```typescript
import { Address } from "../generated/prisma/client";

export type AddressResponse = {
  id: number;
  street?: string | null;
  city?: string | null;
  province?: string | null;
  country: string;
  postal_code: string;
};

export type CreateAddressRequest = {
  contact_id: number;
  street?: string;
  city?: string;
  province?: string;
  country: string;
  postal_code: string;
};

export function toAddressResponse(address: Address): AddressResponse {
  return {
    id: address.id,
    street: address.street,
    city: address.city,
    province: address.province,
    country: address.country,
    postal_code: address.postal_code,
  };
}
```

**`src/validation/address-validation.ts`**

```typescript
import { CreateAddressRequest } from "../model/address-model";
import { ZodType, z } from "zod";

export class AddressValidation {
  static readonly CREATE = z.object({
    contact_id: z.number().int().positive(),
    street: z.string().min(1).max(100).optional(),
    city: z.string().min(1).max(100).optional(),
    province: z.string().min(1).max(100).optional(),
    country: z.string().min(1).max(100),
    postal_code: z.string().min(1).max(100),
  });
}
```

**`src/service/address-service.ts`**

```typescript
import { prismaClient } from "../application/database";
import { AddressValidation } from "../validation/address-validation";
import { ContactService } from "./contact-service";
import { Validation } from "../validation/validation";
import { AddressResponse, CreateAddressRequest, toAddressResponse } from "../model/address-model";
import { User } from "../generated/prisma/client";

export class AddressService {
  static async create(
    user: User,
    request: CreateAddressRequest,
  ): Promise<AddressResponse> {
    const createRequest = Validation.validate(AddressValidation.CREATE, request);

    await ContactService.checkContactMustExist(
      user.username,
      createRequest.contact_id,
    );

    const address = await prismaClient.address.create({
      data: createRequest,
    });

    return toAddressResponse(address);
  }
}
```

- Before creating an address, `create` calls `ContactService.checkContactMustExist` — the same check used in Contact Management ([Stage 3](#stage-3-update--delete-contact)). If the contact doesn't belong to the logged-in `user` (or doesn't exist), the request is rejected with 404 before it ever touches the `addresses` table.

**`src/controller/address-controller.ts`**

```typescript
import { Response, NextFunction } from "express";
import { AddressService } from "../service/address-service";
import { UserRequest } from "../type/user-request";
import { CreateAddressRequest } from "../model/address-model";

export class AddressController {
  static async create(req: UserRequest, res: Response, next: NextFunction) {
    try {
      const request: CreateAddressRequest = req.body as CreateAddressRequest;
      const contactId = Number(req.params.contactId);

      request.contact_id = contactId;

      const response = await AddressService.create(req.user!, request);
      res.status(200).json({
        data: response,
      });
    } catch (error) {
      next(error);
    }
  }
}
```

- `contactId` comes from the **URL param**, then gets attached to `request.contact_id` — not from the body — to stay consistent with the URL shape (`/api/contacts/:contactId/addresses`).

**`src/route/api.ts`**

```typescript
import { AddressController } from "../controller/address-controller";

// Address API
apiRouter.post("/api/contacts/:contactId/addresses", AddressController.create);
```

**`src/test/test-util.ts`** (add `AddressTest`)

```typescript
import { Address } from "../generated/prisma/client";

export class AddressTest {
  static async deleteAll() {
    await prismaClient.address.deleteMany({
      where: {
        contact: {
          username: "test",
        },
      },
    });
  }

  static async create() {
    const contact = await ContactTest.get();
    await prismaClient.address.create({
      data: {
        contact_id: contact.id,
        street: "jl. mangga",
        city: "jakarta barat",
        province: "DKI jakarta",
        country: "indonesia",
        postal_code: "123456",
      },
    });
  }

  static async get(): Promise<Address> {
    const address = await prismaClient.address.findFirst({
      where: {
        contact: {
          username: "test",
        },
      },
    });

    if (!address) {
      throw new Error("Address not found");
    }

    return address;
  }
}
```

**`src/test/address.test.ts`**

```typescript
import supertest from "supertest";
import { AddressTest, ContactTest, UserTest } from "./test-util";
import { web } from "../application/web";
import { logger } from "../application/logging";

describe("POST /api/contacts/:contactId/addresses", () => {
  beforeEach(async () => {
    await UserTest.create();
    await ContactTest.create();
  });

  afterEach(async () => {
    await AddressTest.deleteAll();
    await ContactTest.deleteAll();
    await UserTest.delete();
  });

  it("should create address successfully", async () => {
    const contact = await ContactTest.get();
    const response = await supertest(web)
      .post(`/api/contacts/${contact.id}/addresses`)
      .set("x-api-token", "test")
      .send({
        street: "jl. mangga",
        city: "jakarta barat",
        province: "DKI jakarta",
        country: "indonesia",
        postal_code: "123456",
      });

    expect(response.status).toBe(200);
    expect(response.body.data.street).toBe("jl. mangga");
    expect(response.body.data.postal_code).toBe("123456");
  });

  it("should reject if contact is not found", async () => {
    const contact = await ContactTest.get();
    const response = await supertest(web)
      .post(`/api/contacts/${contact.id + 1}/addresses`)
      .set("x-api-token", "test")
      .send({
        street: "jl. mangga",
        city: "jakarta barat",
        province: "DKI jakarta",
        country: "indonesia",
        postal_code: "123456",
      });

    expect(response.status).toBe(404);
    expect(response.body.errors).toBeDefined();
  });

  it("should reject if request is invalid", async () => {
    const contact = await ContactTest.get();
    const response = await supertest(web)
      .post(`/api/contacts/${contact.id}/addresses`)
      .set("x-api-token", "test")
      .send({ street: "", city: "", province: "", country: "", postal_code: "" });

    expect(response.status).toBe(400);
    expect(response.body.errors).toBeDefined();
  });
});
```

---

### Stage 2: Get Address

This introduces the `checkAddressMustExist` pattern — similar to `checkContactMustExist`, but layered two levels deep: check the contact first, then the address.

**`src/model/address-model.ts`** (add `GetAddressRequest`)

```typescript
export type GetAddressRequest = {
  contact_id: number;
  id: number;
};
```

**`src/validation/address-validation.ts`** (add `GET`)

```typescript
static readonly GET = z.object({
  contact_id: z.number().int().positive(),
  id: z.number().int().positive(),
});
```

**`src/service/address-service.ts`** (add the `checkAddressMustExist` helper + `get` method)

```typescript
import { GetAddressRequest } from "../model/address-model";
import { Address } from "../generated/prisma/client";
import { ResponseError } from "../error/response-error";

export class AddressService {
  static async checkAddressMustExist(
    username: string,
    contactId: number,
    addressId: number,
  ): Promise<Address> {
    await ContactService.checkContactMustExist(username, contactId);
    const address = await prismaClient.address.findUnique({
      where: {
        id: addressId,
        contact_id: contactId,
      },
    });

    if (!address) {
      throw new ResponseError(404, "Address is not found");
    }

    return address;
  }

  // ...create above...

  static async get(
    user: User,
    request: GetAddressRequest,
  ): Promise<AddressResponse> {
    const validateRequest = Validation.validate(AddressValidation.GET, request);

    const address = await this.checkAddressMustExist(
      user.username,
      validateRequest.contact_id,
      validateRequest.id,
    );

    return toAddressResponse(address);
  }
}
```

> **Key Insight:** `checkAddressMustExist` calls `ContactService.checkContactMustExist` on the first line — if the contact alone isn't valid, the method throws `404 "Contact is not found"` immediately, before ever querying the `addresses` table. This two-layer check is what keeps addresses fully isolated per user even if the address ID is guessed correctly.

**`src/controller/address-controller.ts`** (add `get`)

```typescript
import { GetAddressRequest } from "../model/address-model";

static async get(req: UserRequest, res: Response, next: NextFunction) {
  try {
    const request: GetAddressRequest = {
      contact_id: Number(req.params.contactId),
      id: Number(req.params.addressId),
    };

    const response = await AddressService.get(req.user!, request);
    res.status(200).json({
      data: response,
    });
  } catch (error) {
    next(error);
  }
}
```

**`src/route/api.ts`**

```typescript
apiRouter.post("/api/contacts/:contactId/addresses", AddressController.create);
apiRouter.get("/api/contacts/:contactId/addresses/:addressId", AddressController.get);
```

**`src/test/address.test.ts`** (add `describe("GET .../:addressId")`)

```typescript
describe("GET /api/contacts/:contactId/addresses/:addressId", () => {
  beforeEach(async () => {
    await UserTest.create();
    await ContactTest.create();
    await AddressTest.create();
  });

  afterEach(async () => {
    await AddressTest.deleteAll();
    await ContactTest.deleteAll();
    await UserTest.delete();
  });

  it("should get address successfully", async () => {
    const contact = await ContactTest.get();
    const address = await AddressTest.get();
    const response = await supertest(web)
      .get(`/api/contacts/${contact.id}/addresses/${address.id}`)
      .set("x-api-token", "test");

    expect(response.status).toBe(200);
    expect(response.body.data.street).toBe("jl. mangga");
  });

  it("should reject if address not found", async () => {
    const contact = await ContactTest.get();
    const address = await AddressTest.get();
    const response = await supertest(web)
      .get(`/api/contacts/${contact.id}/addresses/${address.id + 1}`)
      .set("x-api-token", "test");

    expect(response.status).toBe(404);
  });

  it("should reject if contact not found", async () => {
    const contact = await ContactTest.get();
    const address = await AddressTest.get();
    const response = await supertest(web)
      .get(`/api/contacts/${contact.id + 1}/addresses/${address.id}`)
      .set("x-api-token", "test");

    expect(response.status).toBe(404);
  });
});
```

---

### Stage 3: Update Address

**`src/model/address-model.ts`** (add `UpdateAddressRequest`)

```typescript
export type UpdateAddressRequest = {
  contact_id: number;
  id: number;
  street?: string;
  city?: string;
  province?: string;
  country?: string;
  postal_code?: string;
};
```

**`src/validation/address-validation.ts`** (add `UPDATE`)

```typescript
static readonly UPDATE = z.object({
  contact_id: z.number().int().positive(),
  id: z.number().int().positive(),
  street: z.string().min(1).max(100).optional(),
  city: z.string().min(1).max(100).optional(),
  province: z.string().min(1).max(100).optional(),
  country: z.string().min(1).max(100),
  postal_code: z.string().min(1).max(100),
});
```

**`src/service/address-service.ts`** (add the `update` method)

```typescript
import { UpdateAddressRequest } from "../model/address-model";

static async update(
  user: User,
  request: UpdateAddressRequest,
): Promise<AddressResponse> {
  const validateRequest = Validation.validate(AddressValidation.UPDATE, request);

  await this.checkAddressMustExist(
    user.username,
    validateRequest.contact_id,
    validateRequest.id,
  );

  const address = await prismaClient.address.update({
    where: {
      id: validateRequest.id,
      contact_id: validateRequest.contact_id,
    },
    data: validateRequest,
  });

  return toAddressResponse(address);
}
```

**`src/controller/address-controller.ts`** (add `update`)

```typescript
import { UpdateAddressRequest } from "../model/address-model";

static async update(req: UserRequest, res: Response, next: NextFunction) {
  try {
    const request: UpdateAddressRequest = req.body as UpdateAddressRequest;

    request.contact_id = Number(req.params.contactId);
    request.id = Number(req.params.addressId);

    const response = await AddressService.update(req.user!, request);
    res.status(200).json({
      data: response,
    });
  } catch (error) {
    next(error);
  }
}
```

**`src/route/api.ts`**

```typescript
apiRouter.put("/api/contacts/:contactId/addresses/:addressId", AddressController.update);
```

**`src/test/address.test.ts`** (add `describe("PUT .../:addressId")`)

```typescript
describe("PUT /api/contacts/:contactId/addresses/:addressId", () => {
  beforeEach(async () => {
    await UserTest.create();
    await ContactTest.create();
    await AddressTest.create();
  });

  afterEach(async () => {
    await AddressTest.deleteAll();
    await ContactTest.deleteAll();
    await UserTest.delete();
  });

  it("should update address successfully", async () => {
    const contact = await ContactTest.get();
    const address = await AddressTest.get();
    const response = await supertest(web)
      .put(`/api/contacts/${contact.id}/addresses/${address.id}`)
      .set("x-api-token", "test")
      .send({
        street: "jl. mangga dua",
        city: "jakarta utara",
        province: "DKI jakarta",
        country: "indonesia",
        postal_code: "567890",
      });

    expect(response.status).toBe(200);
    expect(response.body.data.street).toBe("jl. mangga dua");
  });

  it("should reject if address not found", async () => {
    const contact = await ContactTest.get();
    const address = await AddressTest.get();
    const response = await supertest(web)
      .put(`/api/contacts/${contact.id}/addresses/${address.id + 1}`)
      .set("x-api-token", "test")
      .send({ street: "jl. mangga", country: "indonesia", postal_code: "123456" });

    expect(response.status).toBe(404);
  });

  it("should reject if contact not found", async () => {
    const contact = await ContactTest.get();
    const address = await AddressTest.get();
    const response = await supertest(web)
      .put(`/api/contacts/${contact.id + 1}/addresses/${address.id}`)
      .set("x-api-token", "test")
      .send({ street: "jl. mangga", country: "indonesia", postal_code: "123456" });

    expect(response.status).toBe(404);
  });

  it("should reject if request is invalid", async () => {
    const contact = await ContactTest.get();
    const address = await AddressTest.get();
    const response = await supertest(web)
      .put(`/api/contacts/${contact.id}/addresses/${address.id}`)
      .set("x-api-token", "test")
      .send({ street: "", city: "", province: "", country: "", postal_code: "" });

    expect(response.status).toBe(400);
  });
});
```

---

### Stage 4: Remove Address

**`src/validation/address-validation.ts`** (add `REMOVE` — same shape as `GET`, kept separate so the intent is clear from the name)

```typescript
static readonly REMOVE = z.object({
  contact_id: z.number().int().positive(),
  id: z.number().int().positive(),
});
```

**`src/service/address-service.ts`** (add the `remove` method)

```typescript
static async remove(
  user: User,
  request: GetAddressRequest,
): Promise<AddressResponse> {
  const validateRequest = Validation.validate(AddressValidation.GET, request);

  let address = await this.checkAddressMustExist(
    user.username,
    validateRequest.contact_id,
    validateRequest.id,
  );

  address = await prismaClient.address.delete({
    where: {
      id: validateRequest.id,
      contact_id: validateRequest.contact_id,
    },
  });

  return toAddressResponse(address);
}
```

**`src/controller/address-controller.ts`** (add `remove`)

```typescript
static async remove(req: UserRequest, res: Response, next: NextFunction) {
  try {
    const request: GetAddressRequest = {
      contact_id: Number(req.params.contactId),
      id: Number(req.params.addressId),
    };

    await AddressService.remove(req.user!, request);
    res.status(200).json({
      data: "OK",
    });
  } catch (error) {
    next(error);
  }
}
```

**`src/route/api.ts`**

```typescript
apiRouter.delete("/api/contacts/:contactId/addresses/:addressId", AddressController.remove);
```

**`src/test/address.test.ts`** (add `describe("DELETE .../:addressId")`)

```typescript
describe("DELETE /api/contacts/:contactId/addresses/:addressId", () => {
  beforeEach(async () => {
    await UserTest.create();
    await ContactTest.create();
    await AddressTest.create();
  });

  afterEach(async () => {
    await AddressTest.deleteAll();
    await ContactTest.deleteAll();
    await UserTest.delete();
  });

  it("should delete address successfully", async () => {
    const contact = await ContactTest.get();
    const address = await AddressTest.get();
    const response = await supertest(web)
      .delete(`/api/contacts/${contact.id}/addresses/${address.id}`)
      .set("x-api-token", "test");

    expect(response.status).toBe(200);
    expect(response.body.data).toBe("OK");
  });

  it("should reject if address not found", async () => {
    const contact = await ContactTest.get();
    const address = await AddressTest.get();
    const response = await supertest(web)
      .delete(`/api/contacts/${contact.id}/addresses/${address.id + 1}`)
      .set("x-api-token", "test");

    expect(response.status).toBe(404);
  });

  it("should reject if contact not found", async () => {
    const contact = await ContactTest.get();
    const address = await AddressTest.get();
    const response = await supertest(web)
      .delete(`/api/contacts/${contact.id + 1}/addresses/${address.id}`)
      .set("x-api-token", "test");

    expect(response.status).toBe(404);
  });
});
```

---

### Stage 5: List Address

The final endpoint — unlike `get`/`update`/`remove`, `list` does **not** need `checkAddressMustExist` (there's no specific address ID to check yet); it only needs to confirm the contact is valid, then fetch every address underneath it.

**`src/service/address-service.ts`** (add the `list` method)

```typescript
static async list(
  user: User,
  contact_id: number,
): Promise<AddressResponse[]> {
  await ContactService.checkContactMustExist(user.username, contact_id);

  const addresses = await prismaClient.address.findMany({
    where: {
      contact_id,
    },
  });

  return addresses.map((address) => toAddressResponse(address));
}
```

**`src/controller/address-controller.ts`** (add `list`)

```typescript
static async list(req: UserRequest, res: Response, next: NextFunction) {
  try {
    const contactId = Number(req.params.contactId);
    const response = await AddressService.list(req.user!, contactId);
    res.status(200).json({
      data: response,
    });
  } catch (error) {
    next(error);
  }
}
```

**`src/route/api.ts`** (final — all Address API routes)

```typescript
// Address API
apiRouter.post("/api/contacts/:contactId/addresses", AddressController.create);
apiRouter.get("/api/contacts/:contactId/addresses/:addressId", AddressController.get);
apiRouter.put("/api/contacts/:contactId/addresses/:addressId", AddressController.update);
apiRouter.delete("/api/contacts/:contactId/addresses/:addressId", AddressController.remove);
apiRouter.get("/api/contacts/:contactId/addresses", AddressController.list);
```

**`src/test/address.test.ts`** (add `describe("GET .../addresses")`)

```typescript
describe("GET /api/contacts/:contactId/addresses", () => {
  beforeEach(async () => {
    await UserTest.create();
    await ContactTest.create();
    await AddressTest.create();
  });

  afterEach(async () => {
    await AddressTest.deleteAll();
    await ContactTest.deleteAll();
    await UserTest.delete();
  });

  it("should get address successfully", async () => {
    const contact = await ContactTest.get();
    const response = await supertest(web)
      .get(`/api/contacts/${contact.id}/addresses`)
      .set("x-api-token", "test");

    expect(response.status).toBe(200);
    expect(response.body.data).toBeDefined();
    expect(response.body.data.length).toBe(1);
  });

  it("should reject if contact not found", async () => {
    const contact = await ContactTest.get();
    const response = await supertest(web)
      .get(`/api/contacts/${contact.id + 1}/addresses`)
      .set("x-api-token", "test");

    expect(response.status).toBe(404);
    expect(response.body.errors).toBeDefined();
  });
});
```

With this, **Address Management** is done: Create → Get → Update → Remove → List — and the entire Contact Management RESTful API (User + Contact + Address) is complete.

---

## 🛠️ Running the Project

All the day-to-day commands are already wired up in `package.json`:

**`package.json`** (scripts)

```json
"scripts": {
  "migrate": "npx prisma migrate dev",
  "generate": "npx prisma generate",
  "build": "tsc --noEmit false",
  "start": "node dist/main.js",
  "dev": "tsx watch src/main.ts",
  "serve": "tsx src/main.ts",
  "test": "jest --runInBand --detectOpenHandles"
}
```

| Script | Command | When to use it |
|---|---|---|
| `npm run migrate` | `npx prisma migrate dev` | Whenever `prisma/schema.prisma` changes (new model, new column, changed constraint). Generates a new migration file under `prisma/migrations`, applies it to the database, **and** regenerates the Prisma Client — this is the command used throughout [Database Design](#-database-design) for the User, Contact, and Address models. |
| `npm run generate` | `npx prisma generate` | Regenerates the Prisma Client (`src/generated/prisma`) from the current schema **without** creating a migration. Useful when the schema itself hasn't changed locally but the generated client is stale (e.g. after `git pull`, or after `node_modules` was reinstalled). |
| `npm run build` | `tsc --noEmit false` | Compiles everything in `src/` into plain JavaScript under `dist/` (per `tsconfig.json`'s `rootDir`/`outDir`) — this is the artifact `npm start` runs. |
| `npm start` | `node dist/main.js` | Runs the **compiled** build output. Requires `npm run build` to have been run first — this is the closest thing to how the app would run in production. |
| `npm run dev` | `tsx watch src/main.ts` | Runs straight from TypeScript source with `tsx`, auto-restarting on every file change. The command to use while actively developing a feature. |
| `npm run serve` | `tsx src/main.ts` | Runs straight from TypeScript source, once, no watch, no build step. A quick way to boot the server for a one-off manual check. |
| `npm test` | `jest --runInBand --detectOpenHandles` | Runs the entire Jest suite (`user.test.ts`, `contact.test.ts`, `address.test.ts`). `--runInBand` forces tests to run serially instead of in parallel — required here because every test file shares the same Postgres database (via `UserTest`/`ContactTest`/`AddressTest`) and would otherwise race each other's `beforeEach`/`afterEach` cleanup. `--detectOpenHandles` helps surface any Prisma connection left open after the suite finishes. |

> **Key Insight:** `build` and `start` are split on purpose. `dev`/`serve` skip compilation entirely (ts run directly by `tsx`), while `build` + `start` mirror how the app would actually be deployed: compile once, then run the plain JS with plain `node` — no TypeScript tooling needed at runtime.

**Typical day-to-day flow:**

1. Changed `prisma/schema.prisma`? → `npm run migrate`
2. Working on a feature → `npm run dev` (auto-reloads on save)
3. Before committing → `npm test`
4. Want to run it the way it'll run in production → `npm run build` then `npm start`

---

## 🧪 Manual Testing

Unit tests (`npm test`) cover each service/controller in isolation with a disposable `"test"` user. The last verification step — after the automated suite is green — is booting the real server and hitting every endpoint by hand, using `manual-test.http`.

**`manual-test.http`**

```http
### Register User
POST http://localhost:3000/api/users
Content-Type: application/json
Accept: application/json
{
  "username": "test",
  "password": "password",
  "name": "Test User"
}

### Login User
POST http://localhost:3000/api/users/login
Content-Type: application/json
Accept: application/json
{
  "username": "test",
  "password": "password"
}

### Get User
GET http://localhost:3000/api/users/current
Content-Type: application/json
Accept: application/json
x-api-token: 53ea080a-bfe5-4658-b2b5-d7d17491fd4e

### Update User
PATCH http://localhost:3000/api/users/current
Content-Type: application/json
Accept: application/json
x-api-token: 53ea080a-bfe5-4658-b2b5-d7d17491fd4e

{
    "name": "Update Test User"
}

### Update Password
PATCH http://localhost:3000/api/users/current
Content-Type: application/json
Accept: application/json
x-api-token: 53ea080a-bfe5-4658-b2b5-d7d17491fd4e

{
    "password": "newpassword"
}

### Logout User
DELETE  http://localhost:3000/api/users/logout
Content-Type: application/json
x-api-token: 53ea080a-bfe5-4658-b2b5-d7d17491fd4e

### Create Contact
POST http://localhost:3000/api/contacts
Content-Type: application/json
Accept: application/json
x-api-token: 53ea080a-bfe5-4658-b2b5-d7d17491fd4e

{
  "first_name": "Test",
  "last_name": "Contact",
  "email": "test@gmail.com",
  "phone": "1234567890"
}

### Get Contact
GET http://localhost:3000/api/contacts/3802
Content-Type: application/json
Accept: application/json
x-api-token: 53ea080a-bfe5-4658-b2b5-d7d17491fd4e

### Update Contact
PUT http://localhost:3000/api/contacts/3802
Content-Type: application/json
Accept: application/json
x-api-token: 53ea080a-bfe5-4658-b2b5-d7d17491fd4e

{
  "first_name": "Update Test",
  "last_name": "Contact",
  "email": "newtest@gmail.com",
  "phone": "1234567890"
}

### Search Contact
GET http://localhost:3000/api/contacts?page=1&size=1
Content-Type: application/json
Accept: application/json
x-api-token: 53ea080a-bfe5-4658-b2b5-d7d17491fd4e

### Remove Contact
DELETE http://localhost:3000/api/contacts/3803
Content-Type: application/json
Accept: application/json
x-api-token: 53ea080a-bfe5-4658-b2b5-d7d17491fd4e

### Add Address
POST http://localhost:3000/api/contacts/3802/addresses
Content-Type: application/json
Accept: application/json
x-api-token: 53ea080a-bfe5-4658-b2b5-d7d17491fd4e

{
  "street": "jl. mangga",
  "city": "jakarta barat",
  "province": "DKI jakarta",
  "country": "indonesia",
  "postal_code": "123456"
}

### Get Address
GET http://localhost:3000/api/contacts/3802/addresses/617
Content-Type: application/json
Accept: application/json
x-api-token: 53ea080a-bfe5-4658-b2b5-d7d17491fd4e

### Update Address
PUT http://localhost:3000/api/contacts/3802/addresses/4082
Content-Type: application/json
Accept: application/json
x-api-token: 53ea080a-bfe5-4658-b2b5-d7d17491fd4e

{
  "street": "jl. mangga dua",
  "city": "jakarta utara",
  "province": "DKI jakarta",
  "country": "indonesia",
  "postal_code": "567890"
}

### Remove Address
DELETE http://localhost:3000/api/contacts/3802/addresses/618
Content-Type: application/json
Accept: application/json
x-api-token: 53ea080a-bfe5-4658-b2b5-d7d17491fd4e

### List Addresses
GET http://localhost:3000/api/contacts/3802/addresses?page=1&size=1
Content-Type: application/json
Accept: application/json
x-api-token: 53ea080a-bfe5-4658-b2b5-d7d17491fd4e
```

- This is the plain-text `.http` request format understood by editor plugins like VS Code's **REST Client** extension or JetBrains' built-in HTTP Client — each block starting with `###` is one independent request, runnable on its own by clicking "Send Request" above it.
- Unlike the automated tests, this file talks to your **real, running dev database** — not a disposable `"test"` user cleaned up by `afterEach`.

**How to actually run through it:**

1. Start the server: `npm run dev` (or `npm run build && npm start`) so it's listening on `http://localhost:3000`.
2. Run **Register User**, then **Login User** — copy the `token` from the login response body.
3. Replace every `x-api-token: ...` value in the file with that fresh token (tokens are regenerated on every login, so a hardcoded one goes stale immediately).
4. Run **Get User** to confirm the token is accepted.
5. Run **Create Contact**, note the returned `id`, then swap that ID into the URLs for **Get/Update/Search/Remove Contact** and every Address request below it.
6. Run **Add Address**, note its `id`, then swap that ID into **Get/Update/Remove Address** and **List Addresses**.
7. Deliberately test the failure paths too — an invalid token, a contact/address ID that doesn't belong to you, or a blank required field — and confirm the response matches the API spec's "Failed" shape (`{ "errors": "..." }`) documented earlier in each module.

> ⚠️ **Note:** the `token`, contact `id`s (`3802`, `3803`), and address `id`s (`617`, `618`, `4082`) in the file above are just a snapshot from one past manual run — they will not match your database. Always refresh them from the actual responses before reusing this file.

---

## 📚 Quick Reference

### All Endpoints

| Module | Method | Endpoint | Auth |
|---|---|---|---|
| User | POST | `/api/users` | - |
| User | POST | `/api/users/login` | - |
| User | GET | `/api/users/current` | ✅ |
| User | PATCH | `/api/users/current` | ✅ |
| User | DELETE | `/api/users/logout` | ✅ |
| Contact | POST | `/api/contacts` | ✅ |
| Contact | GET | `/api/contacts/:contactId` | ✅ |
| Contact | PUT | `/api/contacts/:contactId` | ✅ |
| Contact | DELETE | `/api/contacts/:contactId` | ✅ |
| Contact | GET | `/api/contacts` (search) | ✅ |
| Address | POST | `/api/contacts/:contactId/addresses` | ✅ |
| Address | GET | `/api/contacts/:contactId/addresses/:addressId` | ✅ |
| Address | PUT | `/api/contacts/:contactId/addresses/:addressId` | ✅ |
| Address | DELETE | `/api/contacts/:contactId/addresses/:addressId` | ✅ |
| Address | GET | `/api/contacts/:contactId/addresses` (list) | ✅ |

### Folder Structure

```
src/
├── application/     # web.ts, database.ts, logging.ts
├── controller/      # HTTP layer — parse req, call service, shape res
├── service/         # business logic + checkXMustExist helpers
├── model/           # DTO types (Request/Response) + toXResponse mapper
├── validation/       # Zod schema per feature
├── middleware/       # auth-middleware.ts, error-middleware.ts
├── route/            # public-api.ts (no auth), api.ts (auth)
├── type/             # UserRequest (Request + user)
├── error/            # ResponseError
└── test/             # test-util.ts (XTest.create/get/deleteAll) + *.test.ts
```

---

## 💡 Key Takeaways

- **Layered architecture**: `route → controller → service → prisma`. The controller only handles HTTP concerns (parsing the request, shaping the response); all business logic (validation, ownership checks, hashing) lives in the service.
- **The `checkXMustExist` pattern**: each module (`Contact`, `Address`) has this helper — it queries by `id` **and** filters by ownership (`username`/`contact_id`) at the same time, throwing `404` when nothing matches. This is the single place where data authorization happens, so other queries in the service don't need to repeat that logic.
- **Auth via a header, not JWT**: the token is stored as a plain `token` column on the `users` table and matched directly against the `x-api-token` header by `authMiddleware`. Simple for learning purposes, but in production this is usually replaced with JWT/a session store so tokens can expire without a DB lookup on every request.
- **Centralized error handling**: `errorMiddleware` is the only place that turns an error into an HTTP response — `ZodError` → 400, `ResponseError` → a custom status, anything else → 500. Services/controllers just `throw`; they never call `res.json` for an error manually.
- **Generic `Validation.validate<T>` and `Pageable<T>`**: both are generic so they're reused across modules without duplicating validation code or pagination structure.
