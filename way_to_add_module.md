## 🔧 1. **Basic Module Definition**

The most common way is by using the `@Module()` decorator to define a class as a module.

### ✅ Example:

```ts
import { Module } from "@nestjs/common";
import { UsersService } from "./users.service";
import { UsersController } from "./users.controller";

@Module({
  controllers: [UsersController],
  providers: [UsersService],
})
export class UsersModule {}
```

- `controllers`: Handle incoming HTTP requests
- `providers`: Services, repositories, and other injectable classes

---

## 📦 2. **Feature Modules**

Feature modules group code by **feature** (e.g., Users, Auth, Products).

You create them in their own folder like this:

```
src/users/
  ├── users.module.ts
  ├── users.service.ts
  ├── users.controller.ts
```

### Usage:

```ts
@Module({
  imports: [UsersModule], // inside AppModule or another module
})
export class AppModule {}
```

---

## 🧩 3. **Shared Modules (Reusing Services)**

Sometimes you want to share a service (like a `LoggerService`) across multiple modules.

### Step-by-step:

#### ✅ shared/logger.module.ts

```ts
import { Module } from "@nestjs/common";
import { LoggerService } from "./logger.service";

@Module({
  providers: [LoggerService],
  exports: [LoggerService], // 👈 export it to be used elsewhere
})
export class LoggerModule {}
```

#### ✅ Anywhere else:

```ts
@Module({
  imports: [LoggerModule],
})
export class UsersModule {}
```

---

## 🌍 4. **Global Modules**

If a service is needed **everywhere** (e.g., config, logger), you can make the module global.

### ✅ Example:

```ts
import { Global, Module } from "@nestjs/common";
import { ConfigService } from "./config.service";

@Global()
@Module({
  providers: [ConfigService],
  exports: [ConfigService],
})
export class ConfigModule {}
```

Now, **no need to import `ConfigModule`** in other modules; `ConfigService` is automatically available.

---

## ♻️ 5. **Dynamic Modules**

Dynamic modules allow you to **customize behavior/configuration** at runtime.

### ✅ Example:

```ts
@Module({})
export class DatabaseModule {
  static register(options: DatabaseOptions): DynamicModule {
    return {
      module: DatabaseModule,
      providers: [
        {
          provide: "DB_OPTIONS",
          useValue: options,
        },
        DatabaseService,
      ],
      exports: [DatabaseService],
    };
  }
}
```

### ✅ Usage:

```ts
@Module({
  imports: [DatabaseModule.register({ uri: "mongodb://..." })],
})
export class AppModule {}
```

Used when you want to **pass options/config** into a module (e.g., `JwtModule.register`, `TypeOrmModule.forRoot`).

---

## ⚙️ 6. **Async Dynamic Modules (`registerAsync`)**

Useful when you need to **load config from another service** or **inject values dynamically**.

### ✅ Example:

```ts
@Module({})
export class EmailModule {
  static registerAsync(): DynamicModule {
    return {
      module: EmailModule,
      imports: [ConfigModule],
      providers: [
        {
          provide: "EMAIL_OPTIONS",
          useFactory: async (config: ConfigService) => ({
            host: config.get("EMAIL_HOST"),
          }),
          inject: [ConfigService],
        },
        EmailService,
      ],
      exports: [EmailService],
    };
  }
}
```

---

## 🧪 7. **Testing Modules**

For unit testing, you can create modules using `Test.createTestingModule`.

### ✅ Example:

```ts
import { Test } from "@nestjs/testing";

const moduleRef = await Test.createTestingModule({
  imports: [UsersModule],
}).compile();

const service = moduleRef.get<UsersService>(UsersService);
```

This allows you to test modules in isolation with mocked providers.

---

## 🚀 8. **CLI-Generated Modules**

NestJS CLI helps you quickly generate modules:

```bash
nest g module users
```

Creates `users/users.module.ts` automatically. You can also chain commands:

```bash
nest g resource users
```

Creates module, service, and controller together, optionally with CRUD.

---

## 🧠 Key Concepts to Remember

| Concept           | Meaning                                    |
| ----------------- | ------------------------------------------ |
| `imports`         | Bring in other modules                     |
| `exports`         | Make a provider available to other modules |
| `providers`       | Services, repositories, etc.               |
| `controllers`     | Handle incoming requests                   |
| `@Global()`       | Make providers app-wide                    |
| `register()`      | Dynamic module for passing config          |
| `registerAsync()` | Dynamic module with async config           |

---

## 📚 Summary

| Type               | When to Use                               |
| ------------------ | ----------------------------------------- |
| **Basic module**   | For any logical feature grouping          |
| **Shared module**  | To reuse services across modules          |
| **Global module**  | For universal services like config/logger |
| **Dynamic module** | When passing options or config at runtime |
| **Async module**   | When config requires async injection      |
| **Testing module** | When writing isolated unit tests          |
| **CLI module**     | To quickly scaffold with Nest CLI         |
