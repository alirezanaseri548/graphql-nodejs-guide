# GraphQL with Node.js

<p align="center">
  <strong>فارسی</strong> | <a href="#english-version">English</a>
</p>

---

## فارسی

این مخزن یک راهنمای خلاصه، کاربردی و ساختاریافته درباره **GraphQL** و ارتباط آن با **Node.js** است.  
هدف آن توضیح مفاهیم اصلی GraphQL، تفاوت آن با REST، اصول طراحی Schema و نحوه استفاده از آن در پروژه‌های Node.js است.

---

## GraphQL چیست؟

GraphQL یک **زبان پرس‌وجو برای API** و یک **runtime برای اجرای queryها روی داده‌های تایپ‌شده** است.

در GraphQL، کلاینت می‌تواند دقیقاً همان فیلدهایی را درخواست کند که نیاز دارد. این ویژگی باعث می‌شود:

- از **over-fetching** جلوگیری شود
- ساختار داده شفاف‌تر باشد
- هماهنگی بین فرانت‌اند و بک‌اند بهتر شود
- مستندسازی API دقیق‌تر و ساده‌تر باشد

---

## ارتباط GraphQL با Node.js

Node.js یکی از رایج‌ترین بسترها برای پیاده‌سازی GraphQL است.

در اکوسیستم Node.js معمولاً از کتابخانه‌های زیر استفاده می‌شود:

- `graphql`
- `@apollo/server`
- `graphql-yoga`
- `express`

در این معماری:

- **Schema** قرارداد رسمی API را مشخص می‌کند
- **Resolver** منطق دریافت یا تغییر داده را اجرا می‌کند
- **Node.js** محیط اجرای سرور GraphQL است
- **Database** یا سرویس‌های دیگر منبع اصلی داده هستند

---

## GraphQL در برابر REST

| معیار | REST | GraphQL |
|------|------|---------|
| ساختار API | چند endpoint | معمولاً یک endpoint |
| کنترل داده خروجی | بیشتر در اختیار سرور | بیشتر در اختیار کلاینت |
| انعطاف در انتخاب فیلدها | محدودتر | بسیار بالا |
| مستندسازی | معمولاً جداگانه | از طریق schema |

### مثال REST
```http
GET /users
GET /users/1
GET /users/1/posts

### مثال GraphQL

graphql
query {
  user(id: "1") {
id
name
posts {
title
}
  }
}

---

## مفاهیم اصلی GraphQL

### 1. Schema

ساختار رسمی API را تعریف می‌کند.

### 2. Type

مدل داده را مشخص می‌کند؛ مثل `User` یا `Post`.

### 3. Query

برای خواندن داده استفاده می‌شود.

### 4. Mutation

برای ایجاد، ویرایش یا حذف داده استفاده می‌شود.

### 5. Resolver

تابع‌هایی هستند که داده نهایی را برای هر فیلد یا عملیات برمی‌گردانند.

---

## نمونه Schema

graphql
type User {
  id: ID!
  name: String!
  email: String!
  posts: [Post!]!
}

type Post {
  id: ID!
  title: String!
  content: String
  author: User!
}

type Query {
  users: [User!]!
  user(id: ID!): User
  posts: [Post!]!
}

type Mutation {
  createUser(name: String!, email: String!): User!
  createPost(title: String!, content: String, authorId: ID!): Post!
}

---

## نمونه استفاده در Node.js

### نصب

bash
npm init -y
npm install @apollo/server graphql express cors body-parser
npm install @as-integrations/express5

### نمونه سرور ساده

js
import express from "express";
import cors from "cors";
import bodyParser from "body-parser";
import { ApolloServer } from "@apollo/server";
import { expressMiddleware } from "@as-integrations/express5";

const users = [
  { id: "1", name: "Ali", email: "ali@example.com" }
];

const typeDefs = `#graphql
  type User {
id: ID!
name: String!
email: String!
  }

  type Query {
users: [User!]!
  }
`;

const resolvers = {
  Query: {
users: () => users
  }
};

const server = new ApolloServer({ typeDefs, resolvers });
await server.start();

const app = express();

app.use(
  "/graphql",
  cors(),
  bodyParser.json(),
  expressMiddleware(server)
);

app.listen(4000, () => {
  console.log("Server running at http://localhost:4000/graphql");
});

---

## چرا GraphQL در پروژه‌های مدرن مهم است؟

GraphQL برای پروژه‌هایی که این نیازها را دارند بسیار مفید است:

- فرانت‌اندهای پیچیده با نیازهای داده‌ای متنوع
- اپلیکیشن‌های React / Next.js / Vue
- APIهای تایپ‌شده و توسعه‌پذیر
- کنترل بهتر روی مصرف داده
- هماهنگی بهتر بین تیم فرانت‌اند و بک‌اند

---

## Best Practices

- طراحی **Schema-first**
- نام‌گذاری دقیق و پایدار
- جدا کردن business logic از resolverها
- اعتبارسنجی ورودی‌ها
- پیاده‌سازی authentication و authorization
- استفاده از pagination
- مدیریت مشکل **N+1** با ابزارهایی مانند `DataLoader`
- استفاده از TypeScript در پروژه‌های بزرگ‌تر

---

## ملاحظات امنیتی

در پروژه‌های واقعی این موارد مهم هستند:

- Authentication
- Authorization
- Rate Limiting
- Query Depth Limiting
- Query Complexity Limiting
- Error Handling مناسب
- غیرفعال کردن introspection در production در صورت نیاز

---

## ساختار پیشنهادی مخزن

bash
.
├── README.md
├── examples/
│   └── server.js
└── docs/
└── index.html

---

## جمع‌بندی

GraphQL یک راهکار قدرتمند برای طراحی APIهای مدرن است و Node.js یکی از بهترین بسترها برای پیاده‌سازی آن محسوب می‌شود.

ترکیب **GraphQL + Node.js** به توسعه‌دهنده کمک می‌کند APIهایی بسازد که:

- منعطف باشند
- تایپ‌شده باشند
- برای فرانت‌اند مناسب باشند
- از نظر توسعه‌پذیری ساختار بهتری داشته باشند

---

<details id="english-version">
<summary><strong>English Version</strong></summary>

<br>

# GraphQL with Node.js

A concise, practical, and structured guide to **GraphQL** and its relationship with **Node.js**.  
This repository explains the core concepts of GraphQL, its differences from REST, schema design principles, and how to use it in Node.js projects.

---

## What is GraphQL?

GraphQL is a **query language for APIs** and a **runtime for executing queries against typed data**.

It allows clients to request exactly the fields they need. This helps with:

- Reducing **over-fetching**
- Improving data clarity
- Creating better coordination between frontend and backend
- Making API documentation more precise and maintainable

---

## GraphQL and Node.js

Node.js is one of the most common platforms for implementing GraphQL servers.

In the Node.js ecosystem, GraphQL is often used with libraries such as:

- `graphql`
- `@apollo/server`
- `graphql-yoga`
- `express`

In this architecture:

- **Schema** defines the API contract
- **Resolver** contains the logic for fetching or modifying data
- **Node.js** runs the GraphQL server
- **Database** or external services provide the actual data

---

## GraphQL vs REST

| Criteria | REST | GraphQL |
|----------|------|---------|
| API structure | Multiple endpoints | Usually a single endpoint |
| Response control | Mostly server-driven | Mostly client-driven |
| Field selection flexibility | Limited | High |
| Documentation | Usually separate | Built into the schema |

### REST Example

http
GET /users
GET /users/1
GET /users/1/posts

### GraphQL Example

graphql
query {
  user(id: "1") {
id
name
posts {
title
}
  }
}

---

## Core GraphQL Concepts

### 1. Schema

Defines the formal structure of the API.

### 2. Type

Represents a data model such as `User` or `Post`.

### 3. Query

Used for reading data.

### 4. Mutation

Used for creating, updating, or deleting data.

### 5. Resolver

Functions that return the final data for fields and operations.

---

## Example Schema

graphql
type User {
  id: ID!
  name: String!
  email: String!
  posts: [Post!]!
}

type Post {
  id: ID!
  title: String!
  content: String
  author: User!
}

type Query {
  users: [User!]!
  user(id: ID!): User
  posts: [Post!]!
}

type Mutation {
  createUser(name: String!, email: String!): User!
  createPost(title: String!, content: String, authorId: ID!): Post!
}

---

## Example Usage in Node.js

### Installation

bash
npm init -y
npm install @apollo/server graphql express cors body-parser
npm install @as-integrations/express5

### Simple Server Example

js
import express from "express";
import cors from "cors";
import bodyParser from "body-parser";
import { ApolloServer } from "@apollo/server";
import { expressMiddleware } from "@as-integrations/express5";

const users = [
  { id: "1", name: "Ali", email: "ali@example.com" }
];

const typeDefs = `#graphql
  type User {
id: ID!
name: String!
email: String!
  }

  type Query {
users: [User!]!
  }
`;

const resolvers = {
  Query: {
users: () => users
  }
};

const server = new ApolloServer({ typeDefs, resolvers });
await server.start();

const app = express();

app.use(
  "/graphql",
  cors(),
  bodyParser.json(),
  expressMiddleware(server)
);

app.listen(4000, () => {
  console.log("Server running at http://localhost:4000/graphql");
});

---

## Why GraphQL Matters in Modern Projects

GraphQL is especially useful for projects that need:

- Complex frontend data requirements
- React / Next.js / Vue applications
- Typed and scalable APIs
- Better control over data consumption
- Better coordination between frontend and backend teams

---

## Best Practices

- Use a **schema-first** approach
- Keep naming clear and consistent
- Separate business logic from resolvers
- Validate inputs carefully
- Implement authentication and authorization
- Use pagination where needed
- Handle the **N+1 problem** with tools like `DataLoader`
- Prefer TypeScript for larger projects

---

## Security Considerations

Important concerns in real-world GraphQL systems:

- Authentication
- Authorization
- Rate limiting
- Query depth limiting
- Query complexity limiting
- Proper error handling
- Disabling introspection in production when necessary

---

## Suggested Repository Structure

bash
.
├── README.md
├── examples/
│   └── server.js
└── docs/
└── index.html

---

## Summary

GraphQL is a powerful approach for building modern APIs, and Node.js is one of the best environments for implementing it.

The combination of **GraphQL + Node.js** helps developers build APIs that are:

- Flexible
- Typed
- Frontend-friendly
- Easier to scale and maintain

</details>
