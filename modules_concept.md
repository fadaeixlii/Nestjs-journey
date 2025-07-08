### 📦 Modules in NestJS – Explained

In NestJS, **modules** are the fundamental building blocks of your application. Every NestJS app has at least one module — the **root module**, usually `AppModule`.

---

## 🧱 What is a Module?

A **module** in NestJS is a **class** annotated with the `@Module()` decorator. It groups related components:

- **Providers** (services)
- **Controllers**
- **Imports** (other modules)
- **Exports** (to share providers with other modules)

---

### 🧭 Why use Modules?

NestJS follows a **modular architecture**, meaning:

- Each feature or domain (e.g., `User`, `Auth`, `Blog`) can live in its own module.
- This makes the app easier to organize, maintain, and scale.

---

## 🔧 Module Example

```ts
// users.module.ts
import { Module } from "@nestjs/common";
import { UsersController } from "./users.controller";
import { UsersService } from "./users.service";

@Module({
  controllers: [UsersController],
  providers: [UsersService],
})
export class UsersModule {}
```

This creates a module for handling user-related functionality.

---

## 🧩 AppModule Example

```ts
import { Module } from "@nestjs/common";
import { UsersModule } from "./users/users.module";

@Module({
  imports: [UsersModule], // <- importing feature modules
})
export class AppModule {}
```

The `AppModule` is the root module, and it imports feature modules like `UsersModule`.

---

## 🔁 Module Properties

| Property      | Description                                                           |
| ------------- | --------------------------------------------------------------------- |
| `imports`     | Other modules this one depends on                                     |
| `controllers` | Controllers that handle HTTP requests                                 |
| `providers`   | Services and other providers (e.g., custom classes, database clients) |
| `exports`     | Services or providers this module wants to share with other modules   |

---

## 🧠 Module Scope

- **Scoped**: Providers inside a module are only available **within** that module, unless exported.
- **Global Modules**: You can make a module global using `@Global()` so its providers are available app-wide without importing.

```ts
import { Global, Module } from "@nestjs/common";

@Global()
@Module({
  providers: [ConfigService],
  exports: [ConfigService],
})
export class ConfigModule {}
```

---

## 📚 Summary

- **Modules** are classes decorated with `@Module()`, grouping related controllers and providers.
- Each app has a **root module** (`AppModule`).
- You can import and export modules to structure and share functionality.
- This modular approach allows better organization, scalability, and testing.

Let me know if you want an example with multiple interconnected modules (e.g., `AuthModule` using `UsersModule`).
