# GraphQL with Node.js

راهنمایی خلاصه، علمی و کاربردی درباره **GraphQL** و ارتباط آن با **Node.js**.  
این مخزن برای توضیح مفاهیم اصلی GraphQL، تفاوت آن با REST، اصول طراحی Schema و نحوه استفاده از آن در پروژه‌های Node.js ساخته شده است.

---

## GraphQL چیست؟

GraphQL یک **زبان پرس‌وجو برای API** و یک **runtime برای اجرای queryها روی داده‌های تایپ‌شده** است.  
در GraphQL، کلاینت می‌تواند دقیقاً همان فیلدهایی را درخواست کند که نیاز دارد. این ویژگی باعث می‌شود:

- از **over-fetching** جلوگیری شود
- ساختار داده شفاف‌تر باشد
- توسعه API برای فرانت‌اند و بک‌اند هماهنگ‌تر شود
- مستندسازی API ساده‌تر و دقیق‌تر باشد

---

## ارتباط GraphQL با Node.js

Node.js یکی از رایج‌ترین بسترها برای پیاده‌سازی GraphQL است.  
در اکوسیستم Node.js معمولاً از کتابخانه‌هایی مثل این‌ها استفاده می‌شود:

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
تابع‌هایی که داده نهایی را برای هر فیلد یا عملیات برمی‌گردانند.

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
app.use("/graphql", cors(), bodyParser.json(), expressMiddleware(server));

app.listen(4000, () => {
  console.log("Server running at http://localhost:4000/graphql");
});

---

## چرا GraphQL برای پروژه‌های مدرن مهم است؟

GraphQL در پروژه‌هایی که این نیازها را دارند بسیار مفید است:

- فرانت‌اندهای پیچیده با نیازهای داده‌ای متنوع
- اپلیکیشن‌های React / Next.js / Vue
- APIهایی که باید قابل توسعه و تایپ‌شده باشند
- سیستم‌هایی که می‌خواهند مصرف داده را بهینه کنند

---

## Best Practices

- طراحی **Schema-first**
- نام‌گذاری دقیق و پایدار
- جدا کردن business logic از resolverها
- اعتبارسنجی ورودی‌ها
- کنترل دسترسی و احراز هویت
- استفاده از pagination
- مدیریت مشکل **N+1** با ابزارهایی مانند `DataLoader`
- استفاده از TypeScript در پروژه‌های جدی

---

## ملاحظات امنیتی

در پروژه‌های واقعی این موارد مهم هستند:

- Authentication
- Authorization
- Rate Limiting
- Query Depth Limiting
- Error Handling مناسب
- غیرفعال کردن introspection در production در صورت نیاز

---

## جمع‌بندی

GraphQL یک راهکار قدرتمند برای طراحی APIهای مدرن است و Node.js یکی از بهترین بسترها برای پیاده‌سازی آن محسوب می‌شود.  
ترکیب **GraphQL + Node.js** به توسعه‌دهنده کمک می‌کند APIهایی بسازد که:

- منعطف باشند
- تایپ‌شده باشند
- برای فرانت‌اند مناسب باشند
- و از نظر توسعه‌پذیری ساختار بهتری داشته باشند

---

## پیشنهاد ساختار مخزن

bash
.
├── README.md
├── examples/
│   └── server.js
└── docs/
└── index.html

> اگر فقط یک فایل می‌خواهی، `README.md` به‌تنهایی هم کافی است.

---

## License

در صورت نیاز، یک فایل `LICENSE` به مخزن اضافه کنید.


---

## جواب کوتاه به سوالت
**نه، الزاماً چیز دیگری نیاز ندارد.**  
اگر فقط می‌خواهی مخزن تمیز و قابل فهم باشد:

- `README.md` کافی است

اگر بخواهی حرفه‌ای‌ترش کنی:

- `LICENSE`
- یک `example` ساده
- یا `docs/index.html`

---

اگر بخواهی، من می‌توانم الان **یک نسخه خیلی حرفه‌ای‌تر و گیت‌هاب‌پسندتر README** هم بدهم که:
- badge داشته باشد
- ساختار رسمی‌تر داشته باشد
- برای portfolio بهتر باشد

اگر خواستی بگو:  
**"نسخه حرفه‌ای‌تر README را بده"**