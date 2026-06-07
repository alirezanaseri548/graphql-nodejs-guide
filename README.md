# GraphQL with Node.js

راهنمایی خلاصه، علمی و کاربردی درباره **GraphQL** و ارتباط آن با **Node.js**.

این مخزن برای توضیح مفاهیم اصلی GraphQL، تفاوت آن با REST، اصول طراحی Schema و نحوه استفاده از آن در پروژه‌های Node.js ساخته شده است.

---

## GraphQL چیست؟

GraphQL یک **زبان پرس‌وجو برای API** و یک **runtime برای اجرای queryها روی داده‌های تایپ‌شده** است.

در GraphQL، کلاینت می‌تواند دقیقاً همان فیلدهایی را درخواست کند که نیاز دارد. این ویژگی باعث می‌شود:

- از **over-fetching** جلوگیری شود
- ساختار داده شفاف‌تر باشد
- هماهنگی بین فرانت‌اند و بک‌اند بهتر شود
- مستندسازی API دقیق‌تر و ساده‌تر باشد
- توسعه و نگهداری API ساختارمندتر انجام شود

---

## ارتباط GraphQL با Node.js

Node.js یکی از رایج‌ترین بسترها برای پیاده‌سازی GraphQL است.

در اکوسیستم Node.js معمولاً از کتابخانه‌ها و ابزارهای زیر استفاده می‌شود:

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
| مستندسازی | معمولاً جداگانه | از طریق Schema |
| توسعه‌پذیری | وابسته به طراحی endpointها | وابسته به طراحی Schema |

---

## مثال REST
```http
GET /users
GET /users/1
GET /users/1/posts

در REST معمولاً برای دریافت داده‌های مرتبط، چند endpoint مختلف استفاده می‌شود.

---

## مثال GraphQL

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

در GraphQL، کلاینت فقط فیلدهایی را درخواست می‌کند که واقعاً نیاز دارد.

---

## مفاهیم اصلی GraphQL

### 1. Schema

Schema ساختار رسمی API را تعریف می‌کند.

در واقع Schema مشخص می‌کند:

- چه داده‌هایی در API وجود دارند
- چه queryهایی قابل اجرا هستند
- چه mutationهایی برای تغییر داده وجود دارند
- نوع هر فیلد چیست

---

### 2. Type

Type مدل داده را مشخص می‌کند.

برای مثال:

- `User`
- `Post`
- `Comment`
- `Product`

هر Type شامل فیلدهایی است که نوع داده آن‌ها مشخص است.

---

### 3. Query

Query برای خواندن و دریافت داده استفاده می‌شود.

مثلاً:

- دریافت لیست کاربران
- دریافت اطلاعات یک پست
- دریافت محصولات
- دریافت کامنت‌ها

---

### 4. Mutation

Mutation برای تغییر داده استفاده می‌شود.

عملیات‌هایی مثل:

- ایجاد داده
- ویرایش داده
- حذف داده
- ثبت‌نام کاربر
- ورود کاربر

نمونه‌هایی از Mutation:

graphql
mutation {
  createUser(name: "Ali", email: "ali@example.com") {
id
name
email
  }
}

---

### 5. Resolver

Resolver تابعی است که منطق دریافت یا تغییر داده را اجرا می‌کند.

هر Query یا Mutation معمولاً به یک Resolver متصل است.

Resolver می‌تواند داده را از منابع مختلف دریافت کند، مثل:

- Database
- API خارجی
- فایل
- حافظه موقت
- سرویس‌های دیگر

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

## توضیح علامت‌های مهم در Schema

### علامت `!`

علامت `!` یعنی مقدار آن فیلد اجباری است و نباید `null` باشد.

مثال:

graphql
name: String!

یعنی فیلد `name` حتماً باید مقدار داشته باشد.

---

### نوع `ID`

نوع `ID` برای شناسه‌ها استفاده می‌شود.

مثال:

graphql
id: ID!

---

### آرایه‌ها

برای تعریف لیست یا آرایه از `[]` استفاده می‌شود.

مثال:

graphql
posts: [Post!]!

یعنی کاربر می‌تواند چند پست داشته باشد.

---

## نمونه استفاده در Node.js

### نصب پکیج‌ها

bash
npm init -y
npm install @apollo/server graphql express cors body-parser
npm install @as-integrations/express5

---

## نمونه سرور ساده با Apollo Server و Express

js
import express from "express";
import cors from "cors";
import bodyParser from "body-parser";
import { ApolloServer } from "@apollo/server";
import { expressMiddleware } from "@as-integrations/express5";

const users = [
  { id: "1", name: "Ali", email: "ali@example.com" },
  { id: "2", name: "Sara", email: "sara@example.com" }
];

const typeDefs = `#graphql
  type User {
id: ID!
name: String!
email: String!
  }

  type Query {
users: [User!]!
user(id: ID!): User
  }
`;

const resolvers = {
  Query: {
users: () => users,
user: (_, args) => {
return users.find((user) => user.id === args.id);
}
  }
};

const server = new ApolloServer({
  typeDefs,
  resolvers
});

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

## اجرای Query در GraphQL

بعد از اجرای سرور، می‌توان query زیر را اجرا کرد:

graphql
query {
  users {
id
name
email
  }
}

خروجی نمونه:

json
{
  "data": {
"users": [
{
"id": "1",
"name": "Ali",
"email": "ali@example.com"
},
{
"id": "2",
"name": "Sara",
"email": "sara@example.com"
}
]
  }
}

---

## نمونه Query برای دریافت یک کاربر

graphql
query {
  user(id: "1") {
id
name
email
  }
}

---

## چرا GraphQL در پروژه‌های مدرن مهم است؟

GraphQL در پروژه‌هایی که نیازهای داده‌ای پیچیده دارند بسیار کاربردی است.

موارد استفاده رایج:

- فرانت‌اندهای پیچیده با داده‌های متنوع
- اپلیکیشن‌های React
- اپلیکیشن‌های Next.js
- اپلیکیشن‌های Vue
- APIهای تایپ‌شده و توسعه‌پذیر
- سیستم‌هایی که نیاز به کنترل دقیق روی داده خروجی دارند

---

## مزایای GraphQL

- دریافت دقیق داده‌های مورد نیاز
- کاهش over-fetching
- کاهش under-fetching
- ساختار تایپ‌شده
- مستندسازی بهتر از طریق Schema
- مناسب برای فرانت‌اندهای مدرن
- امکان توسعه بهتر API در پروژه‌های بزرگ

---

## چالش‌های GraphQL

GraphQL در کنار مزایا، چالش‌هایی هم دارد:

- نیاز به طراحی دقیق Schema
- پیچیدگی بیشتر نسبت به REST در پروژه‌های ساده
- نیاز به کنترل queryهای سنگین
- مدیریت امنیت پیچیده‌تر
- احتمال ایجاد مشکل N+1 در دریافت داده‌ها

---

## مشکل N+1 چیست؟

مشکل N+1 زمانی رخ می‌دهد که برای دریافت داده‌های مرتبط، تعداد زیادی query جداگانه به دیتابیس ارسال شود.

برای مدیریت این مشکل معمولاً از ابزارهایی مثل `DataLoader` استفاده می‌شود.

---

## Best Practices

برای طراحی بهتر GraphQL API رعایت موارد زیر پیشنهاد می‌شود:

- طراحی **Schema-first**
- نام‌گذاری دقیق و پایدار
- جدا کردن business logic از resolverها
- اعتبارسنجی ورودی‌ها
- پیاده‌سازی authentication
- پیاده‌سازی authorization
- استفاده از pagination
- مدیریت مشکل **N+1**
- استفاده از TypeScript در پروژه‌های بزرگ‌تر
- مدیریت خطاها به شکل استاندارد
- مستندسازی مناسب Schema

---

## ملاحظات امنیتی

در پروژه‌های واقعی GraphQL باید به موارد امنیتی زیر توجه شود:

- Authentication
- Authorization
- Rate Limiting
- Query Depth Limiting
- Query Complexity Limiting
- Error Handling مناسب
- Validation ورودی‌ها
- محدود کردن دسترسی کاربران بر اساس نقش
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

## نقش فایل‌ها

### `README.md`

فایل اصلی توضیحات پروژه است و مفاهیم، مثال‌ها و ساختار کلی پروژه را توضیح می‌دهد.

### `examples/`

برای نگهداری نمونه کدهای اجرایی استفاده می‌شود.

### `docs/`

برای نگهداری مستندات تکمیلی یا نسخه HTML استفاده می‌شود.

---

## جمع‌بندی

GraphQL یک راهکار قدرتمند برای طراحی APIهای مدرن است و Node.js یکی از بهترین بسترها برای پیاده‌سازی آن محسوب می‌شود.

ترکیب **GraphQL + Node.js** به توسعه‌دهنده کمک می‌کند APIهایی بسازد که:

- منعطف باشند
- تایپ‌شده باشند
- برای فرانت‌اند مناسب باشند
- ساختار بهتری برای توسعه داشته باشند
- نگهداری و گسترش آن‌ها ساده‌تر باشد

