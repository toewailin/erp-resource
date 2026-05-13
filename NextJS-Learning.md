Next.js ကို professional ဆန်ဆန် အသုံးချနိုင်ဖို့အတွက် Concept အခြေခံကနေ လုပ်ငန်းခွင်သုံး pattern တွေအထိ စနစ်တကျ ရှင်းပြပေးပါ့မယ်။ Professional တစ်ယောက်အနေနဲ့ Framework တစ်ခုကို လေ့လာတဲ့အခါ "ဘယ်လိုသုံးရမလဲ" ဆိုတာထက် "ဘာကြောင့် သုံးရတာလဲ" ဆိုတဲ့ **Architecture** ကို ပိုနားလည်ဖို့ လိုပါတယ်။

---

## ၁။ Next.js ဆိုတာ ဘာလဲ? (The Professional Perspective)

React က Library တစ်ခုဆိုရင် Next.js ကတော့ **Production-ready Framework** တစ်ခုပါ။ Professional engineer တစ်ယောက်အနေနဲ့ Next.js ကို ရွေးချယ်ရတဲ့ အဓိက အကြောင်းရင်း ၃ ခုရှိပါတယ်:

1. **Rendering Strategies:** SSR (Server-Side Rendering) နဲ့ Static Generation တွေကြောင့် SEO ကောင်းပြီး Speed မြန်ပါတယ်။
2. **Full-stack Capability:** API routes တွေပါတဲ့အတွက် Backend server သီးသန့်မလိုဘဲ Full-stack app တစ်ခု ဆောက်လို့ရပါတယ်။
3. **Optimization:** ပုံရိပ်တွေ (Images)၊ စာလုံးအလှတွေ (Fonts) နဲ့ Script တွေကို သူ့အလိုအလျောက် optimize လုပ်ပေးထားပါတယ်။

---

## ၂။ Next.js ရဲ့ အဓိက Pillars (တိုင်ကြီး ၄ တိုင်)

### (က) App Router (The File-based Routing)

Next.js 13/14 နောက်ပိုင်းမှာ `app` directory router ကို သုံးပါတယ်။ `app/dashboard/page.tsx` လို့ folder ဆောက်လိုက်တာနဲ့ `[yourdomain.com/dashboard](https://yourdomain.com/dashboard)` ဆိုတဲ့ URL က အလိုအလျောက် ဖြစ်သွားတာပါ။

* **Layouts:** Dashboard တစ်ခုလုံးမှာ sidebar က တူနေမယ်ဆိုရင် `layout.tsx` ထဲမှာ ထည့်ရေးထားလို့ရပါတယ်။ page တွေ ပြောင်းသွားပေမယ့် layout က re-render မဖြစ်တော့ပါဘူး။

### (ခ) Server vs Client Components

ဒါက အရေးကြီးဆုံးအပိုင်းပါ။

* **Server Components (Default):** Data fetching လုပ်တဲ့အခါ ဒါကို သုံးပါတယ်။ User ရဲ့ browser ဆီကို JavaScript code တွေ အကုန်ပို့စရာမလိုတော့လို့ website က ပေါ့ပါးနေမှာပါ။ (Database ကို တိုက်ရိုက် query လုပ်လို့ရပါတယ်)။
* **Client Components:** User နဲ့ interaction ရှိတဲ့နေရာ (ဥပမာ- Button click, Form input, State change) တွေမှာပဲ `'use client'` ဆိုတာကို ထိပ်ဆုံးမှာ ရေးပြီး သုံးရပါတယ်။

### (ဂ) Data Fetching & Caching

Professional ဆော့ဝဲတွေမှာ data ကို ခဏခဏ fetch မလုပ်အောင် `cache` လုပ်ဖို့ လိုပါတယ်။

```tsx
// fetch ခေါ်တဲ့အခါ professional ဆန်ဆန် ရေးနည်း
const res = await fetch('https://api.example.com/data', { 
  next: { revalidate: 3600 } // ၁ နာရီမှာ တစ်ခါပဲ update လုပ်မယ်
})

```

---

## ၃။ Dashboard တစ်ခုအတွက် Folder Structure (Professional Way)

ပရောဂျက်တစ်ခု ကြီးလာတဲ့အခါ ရှုပ်မသွားအောင် အခုလို စနစ်တကျ ခွဲထားရပါမယ်။

* `app/`: Routing နဲ့ Page တွေ ထားရန်။
* `components/`: UI components တွေ ထားရန်။ (shadcn/ui က components တွေ ဒီထဲရောက်မှာပါ)။
* `lib/`: Utility function တွေ၊ Database configuration တွေ ထားရန်။
* `hooks/`: Custom react hooks တွေ ထားရန်။
* `types/`: TypeScript interface နဲ့ type တွေ ထားရန်။

---

## ၄။ Dashboard ဆောက်ဖို့ လက်တွေ့ Step-by-Step

### အဆင့် (၁) Project Setup

အရင်ဆုံး Terminal မှာ အောက်ပါ code ကို run ပါ။

```bash
npx create-next-app@latest my-admin-portal

```

(TypeScript, Tailwind CSS, App Router တွေကို 'Yes' ရွေးပါ။)

### အဆင့် (၂) UI Library (shadcn/ui) ထည့်သွင်းခြင်း

Professional dashboard တွေမှာ UI ကို အစကနေ မဆောက်ပါဘူး။ `shadcn/ui` ကို သုံးတာက standard ဖြစ်နေပါပြီ။

```bash
npx shadcn-ui@latest init

```

### အဆင့် (၃) လိုအပ်တဲ့ UI တွေ ဆွဲထုတ်ခြင်း

Dashboard အတွက် လိုအပ်တဲ့ Table တွေ၊ Chart တွေ၊ Card တွေကို library ထဲကနေ လှမ်းယူပါမယ်။

```bash
npx shadcn-ui@latest add card table button

```

---

## ၅။ Professional Tip: Clean Code & Performance

1. **Skeleton Screens:** Data တက်မလာခင်မှာ ဖြူဖြူကြီး ဖြစ်မနေဘဲ loading animation ပြဖို့ `loading.tsx` ကို သုံးပါ။
2. **Server Actions:** Form input တွေ သိမ်းတဲ့အခါ API သီးသန့်ရေးစရာမလိုဘဲ Server Action နဲ့ တိုက်ရိုက် database ထဲ ထည့်ပါ။
3. **Environment Variables:** Secret key တွေကို `.env.local` ထဲမှာပဲ သိမ်းပါ။

Next.js မှာ ဘယ်အပိုင်းကို အသေးစိတ် ထပ်သိချင်ပါသလဲ? ဥပမာ- Database နဲ့ ချိတ်တာလား ဒါမှမဟုတ် Auth (Login) ပိုင်းလား? တစ်ခုချင်းစီကို ဆက်ဆွေးနွေးလို့ရပါတယ်။

Next.js မှာ Database နဲ့ ချိတ်ဆက်တဲ့အခါ Professional Engineer တစ်ယောက်အနေနဲ့ **ORM (Object-Relational Mapping)** တစ်ခုခုကို သုံးတာက standard ဖြစ်ပါတယ်။ ဒီနေ့ခေတ်မှာတော့ **Prisma** ဒါမှမဟုတ် **Drizzle ORM** ကို အသုံးများကြပါတယ်။

ကျွန်တော်ကတော့ လက်ရှိ industry မှာ အသုံးအများဆုံးနဲ့ Beginner-friendly ဖြစ်တဲ့ **Prisma** ကို သုံးပြီး **PostgreSQL** (သို့မဟုတ် MongoDB/MySQL) နဲ့ ဘယ်လိုချိတ်ရမလဲဆိုတာကို Step-by-step ရှင်းပြပေးပါ့မယ်။

---

## ၁။ Architecture (Professional Approach)

Professional ဆန်တဲ့ Next.js app တစ်ခုမှာ Database ကို direct ချိတ်ရုံတင်မကဘဲ အောက်ပါအချက်တွေကို သတိထားရပါတယ်-

* **Server-side connection:** Database connection ကို Server Components ဒါမှမဟုတ် Server Actions ထဲမှာပဲ လုပ်ရပါတယ်။ (Security အတွက် Client-side မှာ ဘယ်တော့မှ မချိတ်ပါနဲ့)။
* **Singleton Pattern:** Next.js က hot reload ဖြစ်တိုင်း connection အသစ်တွေ ထပ်မဖန်တီးမိအောင် Singleton pattern သုံးရပါတယ်။
* **Type Safety:** Database က ထွက်လာတဲ့ data တွေဟာ TypeScript type-safe ဖြစ်နေရပါမယ်။

---

## ၂။ လက်တွေ့ ချိတ်ဆက်နည်း (Step-by-Step)

### အဆင့် (၁) Prisma ကို Install လုပ်ခြင်း

အရင်ဆုံး သင့် project ထဲမှာ Prisma ကို သွင်းလိုက်ပါ။

```bash
npm install prisma @prisma/client
npx prisma init

```

ဒီအခါ သင့် project ထဲမှာ `prisma/` ဆိုတဲ့ folder တစ်ခုနဲ့ `.env` file တစ်ခု ထွက်လာပါလိမ့်မယ်။

### အဆင့် (၂) Database URL ကို သတ်မှတ်ခြင်း

`.env` file ထဲမှာ သင့်ရဲ့ Database connection string ကို ထည့်ပါ။ (ဥပမာ- Supabase, Railway, ဒါမှမဟုတ် Local Postgres)

```env
DATABASE_URL="postgresql://user:password@localhost:5432/mydb?schema=public"

```

### အဆင့် (၃) Schema သတ်မှတ်ခြင်း

`prisma/schema.prisma` ထဲမှာ သင့်ရဲ့ Table structure ကို ရေးပါ။ ဥပမာ- Dashboard အတွက် User နဲ့ Product table:

```prisma
model User {
  id    Int     @id @default(autoincrement())
  email String  @unique
  name  String?
  posts Post[]
}

model Post {
  id        Int     @id @default(autoincrement())
  title     String
  content   String?
  published Boolean @default(false)
  authorId  Int
  author    User    @relation(fields: [authorId], references: [id])
}

```

ပြီးရင် Database ထဲကို table တွေ ဆောက်ဖို့ ဒီ command ကို run ပါ:

```bash
npx prisma migrate dev --name init

```

### အဆင့် (၄) Prisma Client ကို Singleton ဖြစ်အောင် လုပ်ခြင်း

Next.js ရဲ့ `lib/` folder ထဲမှာ `db.ts` ဆိုတဲ့ file တစ်ခုဆောက်ပြီး ဒီ code ကို ထည့်ပါ။ ဒါမှ server reload ဖြစ်တိုင်း connection အသစ်တွေ အများကြီး မဖြစ်မှာပါ။

```typescript
import { PrismaClient } from "@prisma/client"

const globalForPrisma = globalThis as unknown as {
  prisma: PrismaClient | undefined
}

export const db = globalForPrisma.prisma ?? new PrismaClient()

if (process.env.NODE_ENV !== "production") globalForPrisma.prisma = db

```

---

## ၃။ Data ကို Fetch လုပ်ပြီး Dashboard မှာ ပြနည်း (Server Component)

အခုဆိုရင် `app/dashboard/page.tsx` ထဲမှာ data ကို အလွယ်တကူ လှမ်းယူလို့ရပါပြီ။ `async/await` ကို သုံးပြီး တိုက်ရိုက် query လုပ်လိုက်ပါ။

```tsx
import { db } from "@/lib/db"

export default async function DashboardPage() {
  // Database ကနေ user တွေကို လှမ်းယူမယ်
  const users = await db.user.findMany();

  return (
    <div className="p-6">
      <h1 className="text-2xl font-bold mb-4">Users List</h1>
      <ul className="space-y-2">
        {users.map((user) => (
          <li key={user.id} className="p-3 border rounded-lg shadow-sm">
            {user.name} - {user.email}
          </li>
        ))}
      </ul>
    </div>
  )
}

```

---

## ၄။ Data အသစ်ထည့်နည်း (Server Actions)

Professional နည်းလမ်းအရ Form data တွေကို သိမ်းချင်ရင် **Server Actions** ကို သုံးပါတယ်။

```tsx
// components/AddUserForm.tsx
import { db } from "@/lib/db"
import { revalidatePath } from "next/cache"

export default function AddUserForm() {
  async function addUser(formData: FormData) {
    "use server" // ဒါက Server Action ဖြစ်ကြောင်း ပြောတာပါ
    
    const name = formData.get("name") as string
    const email = formData.get("email") as string

    await db.user.create({
      data: { name, email },
    })

    revalidatePath("/dashboard") // Dashboard က data ကို ချက်ချင်း update ဖြစ်အောင် လုပ်တာ
  }

  return (
    <form action={addUser}>
      <input name="name" placeholder="Name" className="border p-2" />
      <input name="email" placeholder="Email" className="border p-2" />
      <button type="submit" className="bg-blue-500 text-white p-2">Add User</button>
    </form>
  )
}

```

---

## Summary Checklist

1. **Environment Variable:** `.env` ထဲမှာ connection string ထည့်ပါ။
2. **Schema:** `schema.prisma` မှာ table ပုံစံဆွဲပါ။
3. **Migration:** `npx prisma migrate dev` လုပ်ပါ။
4. **Client:** `lib/db.ts` မှာ singleton client ဆောက်ပါ။
5. **Usage:** Server Components မှာ `db.table.findMany()` နဲ့ သုံးပါ။

ဒီအပိုင်းမှာ ဘယ်နေရာက နားလည်ရ ခက်နေပါသလဲ? ဒါမှမဟုတ် သင့်ရဲ့ Project မှာ သုံးမယ့် တကယ့် database အမျိုးအစား (ဥပမာ- Supabase, MongoDB) ရှိရင် ပြောပြပေးပါ၊ ပိုတိကျအောင် ရှင်းပြပေးပါ့မယ်။

Professional Software Engineer တစ်ယောက်အနေနဲ့ Database ရွေးချယ်မှုက Project ရဲ့ **Scalability** (နောင်တစ်ချိန် လူသုံးများလာရင် ခံနိုင်ရည်ရှိမှု) နဲ့ **Maintainability** (ပြင်ရဆင်ရ လွယ်ကူမှု) အပေါ် အများကြီး မူတည်ပါတယ်။

Database တွေကို အကြမ်းဖျင်းအားဖြင့် **Relational (SQL)** နဲ့ **Non-relational (NoSQL)** ဆိုပြီး နှစ်မျိုးခွဲခြားနိုင်ပါတယ်။

---

## ၁။ ဘယ်လို Data မျိုးကို ဘယ်မှာ သိမ်းမလဲ?

ဒေတာရဲ့ သဘာဝပေါ်မူတည်ပြီး ရွေးချယ်ရမယ့် ပုံစံကို ဇယားနဲ့ ရှင်းပြပေးပါ့မယ်။

| Data အမျိုးအစား | Database အမျိုးအစား | နမူနာ (Example) | ဘာကြောင့်သုံးတာလဲ |
| --- | --- | --- | --- |
| **Structured Data** | **Relational (SQL)** | PostgreSQL, MySQL | Data တွေက တစ်ခုနဲ့တစ်ခု ချိတ်ဆက်မှု (Relationships) ရှိနေရင် သုံးပါတယ်။ (ဥပမာ- User တစ်ယောက်မှာ Order တွေအများကြီးရှိတာမျိုး) |
| **Unstructured / Flexible Data** | **NoSQL (Document)** | MongoDB | Data structure က ခဏခဏ ပြောင်းလဲနိုင်ရင် ဒါမှမဟုတ် Schema မသေချာရင် သုံးပါတယ်။ |
| **Temporary / Cache Data** | **In-Memory** | Redis | ခဏခဏ လှမ်းဖတ်နေရတဲ့ data တွေကို ခဏသိမ်းထားပြီး Speed မြန်အောင် လုပ်ဖို့ သုံးပါတယ်။ |
| **Search Data** | **Search Engine** | Elasticsearch | စာသားအရှည်ကြီးတွေကို အမြန်ဆုံး ရှာဖွေနိုင်ဖို့ (Full-text search) အတွက် သုံးပါတယ်။ |

---

## ၂။ Next.js အတွက် ဘယ် Database က အကောင်းဆုံးလဲ?

လက်ရှိ Next.js ecosystem မှာ Professional အကျဆုံးနဲ့ အလိုက်ဖက်ဆုံး ရွေးချယ်မှုတွေကတော့ အောက်ပါအတိုင်း ဖြစ်ပါတယ်။

### (က) PostgreSQL (အကောင်းဆုံး All-rounder)

Project တော်တော်များများအတွက် **PostgreSQL** က နံပါတ် ၁ ရွေးချယ်မှုပါ။

* **ဘာကြောင့်လဲ:** Complex ဖြစ်တဲ့ query တွေကို ကောင်းကောင်း handle လုပ်နိုင်သလို၊ JSON data တွေကိုလည်း NoSQL လိုမျိုး သိမ်းလို့ရလို့ အရမ်း flexible ဖြစ်ပါတယ်။
* **Next.js နဲ့ တွဲသုံးပုံ:** **Supabase** ဒါမှမဟုတ် **Neon** လိုမျိုး Serverless Postgres service တွေကို သုံးရင် အဆင်ပြေဆုံးပါ။

### (ခ) MongoDB (Flexible ဖြစ်ချင်ရင်)

Data structure က ပုံသေမရှိဘဲ စာမျက်နှာတစ်ခုချင်းစီမှာ data ပုံစံတွေ ကွဲပြားနေမယ်ဆိုရင် NoSQL က ပိုကောင်းပါတယ်။

* **Next.js နဲ့ တွဲသုံးပုံ:** **MongoDB Atlas** ကို သုံးပြီး Mongoose ဒါမှမဟုတ် Prisma နဲ့ ချိတ်ဆက်နိုင်ပါတယ်။

---

## ၃။ ဘယ်လိုချိတ်ဆက်မှုမျိုးက အကောင်းဆုံးလဲ? (Connection Strategy)

Professional တစ်ယောက်အနေနဲ့ ချိတ်ဆက်တဲ့အခါ အောက်ပါ နည်းလမ်း ၃ ခုကို သတိထားရပါမယ်။

### ၁။ Serverless Environment ကို သတိပြုပါ

Next.js ကို Vercel လိုမျိုးမှာ တင်တဲ့အခါ သင့်ရဲ့ app က Serverless function အနေနဲ့ အလုပ်လုပ်ပါတယ်။ Function တွေက ခဏခဏ ပွင့်လိုက် ပိတ်လိုက် ဖြစ်နေတဲ့အတွက် Database connection တွေ အများကြီး ဖြစ်သွားပြီး database "crash" ဖြစ်တတ်ပါတယ်။

* **ဖြေရှင်းနည်း:** **Connection Pooling** ကို သုံးရပါတယ်။ (Supabase နဲ့ Neon မှာ ဒါတွေ အလိုအလျောက် ပါပြီးသားပါ)။

### ၂။ ORM (Object-Relational Mapping) ကို သုံးပါ

Database query တွေကို string တွေနဲ့ ရေးမယ့်အစား **Prisma** ဒါမှမဟုတ် **Drizzle ORM** ကို သုံးပါ။

* ဒါမှ TypeScript ရဲ့ Type-safety ကို ရမှာဖြစ်ပြီး coding error တွေကို သိသိသာသာ လျှော့ချနိုင်မှာပါ။

### ၃။ Environment Variables (Security)

Database ရဲ့ password နဲ့ URL တွေကို code ထဲမှာ တိုက်ရိုက်မရေးဘဲ `.env.local` ထဲမှာပဲ သိမ်းဆည်းပါ။

---

## Summary Recommendation

အကယ်၍ သင်က **Professional Dashboard** တစ်ခု ဆောက်မယ်ဆိုရင် ကျွန်တော့်ရဲ့ အကြံပြုချက်က-

1. **Database:** **PostgreSQL** (Managed hosting အနေနဲ့ **Supabase** ကို သုံးပါ)။
2. **ORM:** **Prisma** (Type-safety နဲ့ Developer experience အရမ်းကောင်းပါတယ်)။

ဒီထဲကမှ သင်စိတ်ဝင်စားတဲ့ Database အမျိုးအစား ရှိပါသလား? အဲဒါကို Next.js နဲ့ လက်တွေ့ ဘယ်လို configure လုပ်ရမလဲဆိုတာ ဆက်ပြောပြပေးနိုင်ပါတယ်။

Professional Engineer တစ်ယောက်အနေနဲ့ **Supabase (PostgreSQL)** နဲ့ **Prisma (ORM)** ကို တွဲသုံးတာဟာ အလွန်ကောင်းမွန်တဲ့ ရွေးချယ်မှုပါ။ ဒါဟာ "Type-safety" လည်းရသလို Database ကို UI ကနေ လွယ်လွယ်ကူကူ စီမံခန့်ခွဲနိုင်တဲ့ (Managed Service) အားသာချက်ကိုလည်း ရရှိစေပါတယ်။

လက်တွေ့ လုပ်ဆောင်ရမယ့် အဆင့်ဆင့်ကို ရှင်းပြပေးပါ့မယ်။

---

### ၁။ Supabase မှာ Database တည်ဆောက်ခြင်း

အရင်ဆုံး [Supabase.com](https://supabase.com) မှာ project အသစ်တစ်ခု ဆောက်ပါ။

1. **Project Settings > Database** ကိုသွားပါ။
2. **Connection String** ထဲက `Transaction` mode (Port 6543) ရဲ့ URL ကို ကူးထားပါ။
* *မှတ်ချက်:* Next.js ကဲ့သို့ serverless ပတ်ဝန်းကျင်မှာ Connection Pooling အတွက် **Transaction Mode** URL ကို သုံးတာ အကောင်းဆုံးပါ။



---

### ၂။ Project ထဲမှာ Prisma Setup လုပ်ခြင်း

Terminal မှာ အောက်ပါ library တွေကို သွင်းပါ-

```bash
npm install prisma @prisma/client
npx prisma init

```

အခု `.env` file ထဲမှာ Supabase က ရလာတဲ့ URL ကို ထည့်ပါ-

```env
# Supabase Transaction URL
DATABASE_URL="postgres://postgres.[YOUR-PROJECT-ID]:[PASSWORD]@aws-0-ap-southeast-1.pooler.supabase.com:6543/postgres?pgbouncer=true"

# Direct connection (migration လုပ်ဖို့အတွက်)
DIRECT_URL="postgres://postgres.[YOUR-PROJECT-ID]:[PASSWORD]@aws-0-ap-southeast-1.pooler.supabase.com:5432/postgres"

```

---

### ၃။ Schema ကို Professional ဆန်ဆန် ရေးသားခြင်း

`prisma/schema.prisma` file ထဲမှာ အောက်ပါအတိုင်း ပြင်ရေးပါ။ `directUrl` ထည့်ပေးရတာကို သတိပြုပါ။

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider  = "postgresql"
  url       = env("DATABASE_URL")
  directUrl = env("DIRECT_URL")
}

// ဥပမာ: User Table
model User {
  id        String   @id @default(uuid())
  email     String   @unique
  name      String?
  role      Role     @default(USER)
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}

enum Role {
  USER
  ADMIN
}

```

ပြီးနောက် Database ထဲကို table တွေ တကယ်သွားဆောက်ဖို့ Terminal မှာ run ပါ:

```bash
npx prisma migrate dev --name init

```

---

### ၄။ Prisma Client ကို Singleton Pattern ဖြင့် တည်ဆောက်ခြင်း

Next.js ရဲ့ Hot Reload ကြောင့် connection တွေ အများကြီး မပွင့်သွားအောင် `lib/db.ts` မှာ ဒါကို ရေးထားရပါမယ်။

```typescript
import { PrismaClient } from "@prisma/client";

const globalForPrisma = globalThis as unknown as {
  prisma: PrismaClient | undefined;
};

export const db = globalForPrisma.prisma ?? new PrismaClient();

if (process.env.NODE_ENV !== "production") globalForPrisma.prisma = db;

```

---

### ၅။ Next.js ထဲမှာ လက်တွေ့ အသုံးပြုခြင်း (CRUD)

#### (က) Data ဖတ်ခြင်း (Read)

Dashboard page (`app/dashboard/page.tsx`) မှာ server component သုံးပြီး data ဖတ်ပါမယ်။

```tsx
import { db } from "@/lib/db";

export default async function Dashboard() {
  // DB ကနေ user တွေအကုန် ဖတ်ယူမယ်
  const users = await db.user.findMany({
    orderBy: { createdAt: 'desc' }
  });

  return (
    <div>
      {users.map(user => (
        <p key={user.id}>{user.email}</p>
      ))}
    </div>
  );
}

```

#### (ခ) Data အသစ်ထည့်ခြင်း (Create)

**Server Actions** ကို သုံးပြီး user အသစ် ထည့်ပါမယ်။

```tsx
// actions/user-actions.ts
"use server"
import { db } from "@/lib/db";
import { revalidatePath } from "next/cache";

export async function createNewUser(formData: FormData) {
  const email = formData.get("email") as string;

  await db.user.create({
    data: { email }
  });

  revalidatePath("/dashboard"); // Page ကို data အသစ်နဲ့ update ဖြစ်အောင်လုပ်တာ
}

```

---

### ၆။ Professional Tips for Supabase & Prisma

1. **Supabase Studio:** Prisma နဲ့ migrate လုပ်ပြီးတာနဲ့ Supabase Dashboard ထဲက "Table Editor" မှာ သင့် data တွေကို spreadsheet ပုံစံမျိုးနဲ့ အလွယ်တကူ ကြည့်လို့၊ ပြင်လို့ ရသွားပါပြီ။
2. **Prisma Studio:** ကိုယ့်စက်ထဲမှာတင် data တွေ ကြည့်ချင်ရင် `npx prisma studio` ကို run ပါ။ Browser မှာ database GUI ပွင့်လာပါလိမ့်မယ်။
3. **Data Consistency:** `updatedAt DateTime @updatedAt` ကို သုံးပေးခြင်းအားဖြင့် data ပြင်တိုင်း အချိန်ကို အလိုအလျောက် မှတ်ပေးသွားမှာ ဖြစ်ပါတယ်။

ဒါကတော့ PostgreSQL (Supabase) နဲ့ Prisma ကို Next.js မှာ professional ကျကျ ချိတ်ဆက်တဲ့ ပုံစံပါပဲ။ သင့်ရဲ့ Dashboard မှာ ဘယ်လို feature မျိုး (ဥပမာ- User Management လား၊ Product Inventory လား) ကို အဓိက ထည့်ချင်တာလဲဗျ? အဲဒါသိရင် schema ကို အဲဒါနဲ့ ကိုက်ညီအောင် ကူညီရေးပေးပါ့မယ်။

Next.js မှာ Auth (Authentication) အတွက် Professional အကျဆုံး Standard က **Auth.js (ယခင် NextAuth.js)** ကို သုံးတာ ဖြစ်ပါတယ်။

Professional Engineer တစ်ယောက်အနေနဲ့ Auth ကို စဉ်းစားတဲ့အခါ "Login ဝင်ရုံတင်မကဘဲ Database ထဲက User Profile နဲ့ ဘယ်လို ချိတ်ဆက်မလဲ" ဆိုတဲ့ **Session Strategy** က အရေးကြီးဆုံးပါ။

---

### ၁။ Auth.js + Prisma + Supabase (The Setup)

ဒီ Layer မှာ Auth.js က User ကို Login ပေးဝင်မယ်၊ Prisma Adapter က Login ဝင်လာတဲ့ User ရဲ့ အချက်အလက်ကို Supabase Database ထဲမှာ အလိုအလျောက် သိမ်းပေးမှာ ဖြစ်ပါတယ်။

**Installation:**

```bash
npm install next-auth@beta @auth/prisma-adapter

```

---

### ၂။ Database Schema ပြင်ဆင်ခြင်း

Auth.js အတွက် လိုအပ်တဲ့ Table တွေ (User, Account, Session, VerificationToken) ကို `prisma/schema.prisma` မှာ ထည့်ပေးရပါမယ်။

```prisma
model User {
  id            String    @id @default(cuid())
  name          String?
  email         String?   @unique
  emailVerified DateTime?
  image         String?
  role          Role      @default(USER) // Professional ဆန်ဆန် Role ခွဲမယ်
  accounts      Account[]
  sessions      Session[]
  
  // Custom Profile fields
  bio           String?
  phoneNumber   String?
  createdAt     DateTime  @default(now())
}

enum Role {
  USER
  ADMIN
}

// NextAuth အတွက် လိုအပ်သော အခြား Table များ (Account, Session စသည်...)
// (Prisma Adapter Documentation ရှိ standard schema ကို ကူးထည့်ပေးရပါမယ်)

```

---

### ၃။ Auth Configuration (The Logic)

`auth.ts` file တစ်ခုဆောက်ပြီး Login logic ကို ရေးပါမယ်။ Professional ဆန်ဖို့အတွက် **Callbacks** တွေကို သုံးပြီး Session ထဲမှာ `user id` နဲ့ `role` ကို ထည့်ပေးထားရပါမယ်။

```typescript
// auth.ts
import NextAuth from "next-auth"
import { PrismaAdapter } from "@auth/prisma-adapter"
import { db } from "@/lib/db"
import Google from "next-auth/providers/google"

export const { handlers, auth, signIn, signOut } = NextAuth({
  adapter: PrismaAdapter(db),
  providers: [Google],
  callbacks: {
    // Session ထဲမှာ User ID နဲ့ Role ကို ထည့်ပေးခြင်း (ဒါမှ ခဏခဏ DB ခေါ်စရာမလိုတော့မှာ)
    async session({ session, user }) {
      if (session.user) {
        session.user.id = user.id;
        session.user.role = user.role; 
      }
      return session;
    },
  },
})

```

---

### ၄။ Logined User ရဲ့ Profile ကို Management လုပ်ပုံ

User က Login ဝင်ပြီးသွားရင် သူ့ရဲ့ Profile Info ကို Update လုပ်တာ ဒါမှမဟုတ် Dashboard မှာ ပြတာတွေကို **Server Actions** သုံးပြီး လုပ်ရပါမယ်။

#### (က) လက်ရှိ Login ဝင်ထားသော User ကို စစ်ဆေးခြင်း

```tsx
// app/dashboard/profile/page.tsx
import { auth } from "@/auth"
import { db } from "@/lib/db"

export default async function ProfilePage() {
  const session = await auth(); // Server-side session ယူခြင်း

  if (!session?.user) return <div>Please Login</div>;

  // DB ကနေ အသေးစိတ် Profile ပြန်ဖတ်ခြင်း
  const userProfile = await db.user.findUnique({
    where: { id: session.user.id }
  });

  return (
    <div className="p-6">
      <h1>Welcome, {userProfile?.name}</h1>
      <p>Role: {userProfile?.role}</p>
      {/* Profile Edit Form အောက်မှာ ဆက်ရေးမယ် */}
    </div>
  );
}

```

#### (ခ) Profile Update လုပ်ခြင်း (Server Action)

Professional ဆန်တဲ့နည်းက API ထိုင်ဆောက်မနေဘဲ `useActionState` ဒါမှမဟုတ် ရိုးရိုး Server Action နဲ့ DB ကို တိုက်ရိုက် Update လုပ်တာပါ။

```typescript
// actions/update-profile.ts
"use server"
import { auth } from "@/auth"
import { db } from "@/lib/db"
import { revalidatePath } from "next/cache"

export async function updateProfile(formData: FormData) {
  const session = await auth();
  if (!session?.user?.id) throw new Error("Unauthorized");

  const name = formData.get("name") as string;
  const bio = formData.get("bio") as string;

  await db.user.update({
    where: { id: session.user.id },
    data: { name, bio }
  });

  revalidatePath("/dashboard/profile");
}

```

---

### ၅။ Professional Security Considerations (Engineer တစ်ယောက် သိထားသင့်သမျှ)

1. **Server-Side Protection:** Client-side (`useEffect`) မှာ user ကို စစ်တာထက် Server Components ဒါမှမဟုတ် Middleware မှာတင် `auth()` ကို သုံးပြီး Unauthorized user တွေကို `redirect('/')` လုပ်ပစ်ပါ။
2. **Role-Based Access Control (RBAC):** Admin တွေပဲ ကြည့်ရမယ့် page ဆိုရင် `session.user.role !== 'ADMIN'` ဖြစ်နေရင် ဝင်ခွင့်မပေးတဲ့ logic ကို ထည့်သွင်းပါ။
3. **Data Validation:** Form ကနေ တက်လာတဲ့ data တွေကို database ထဲမထည့်ခင် **Zod** လို library မျိုးသုံးပြီး validate အရင်လုပ်ပါ။ (ဥပမာ- Email ဟုတ်မဟုတ်၊ စာလုံးအရေအတွက် ပြည့်မပြည့်)။

---

### ရှေ့ဆက်ရမယ့် အပိုင်း (Next Step)

အခုဆိုရင် Login ဝင်လို့ရပြီ၊ Database ထဲမှာ User တွေသိမ်းလို့ရပြီ၊ Profile တွေ update လုပ်လို့ရပါပြီ။

သင်အဓိက ဆောက်ချင်တဲ့ Dashboard က **SaaS ပုံစံမျိုး (ဥပမာ- Subscription ပါမယ်၊ Multi-tenant ဖြစ်မယ်)** လား? ဒါမှမဟုတ် **Personal/Internal Tool** လား? အဲဒါပေါ်မူတည်ပြီး Architecture ကို ထပ်ပြီး Optimize လုပ်ပေးလို့ရပါတယ်။

ကားအငှားလုပ်ငန်း (Car Rental Service) အတွက် Professional Dashboard တစ်ခုကို တည်ဆောက်တဲ့အခါ **Role-based Access Control (RBAC)** အရ Admin (Owner), Fleet Manager နဲ့ Operator ဆိုပြီး ခွဲခြားစဉ်းစားရပါမယ်။

သင့်ရဲ့ Dashboard မှာ ပါဝင်သင့်တဲ့ အဓိက Modules တွေနဲ့ Functions တွေကို Professional Software Architecture အတိုင်း အောက်ပါအတိုင်း ခွဲခြားပြပေးပါ့မယ်။

---

## ၁။ Dashboard Overview (The Nerve Center)

ဒါက Admin ဝင်လိုက်တာနဲ့ မြင်ရမယ့် High-level data တွေပါ။

* **Real-time Analytics:** လက်ရှိ ငှားထားသောကား (Rented)၊ အားနေသောကား (Available) နှင့် ပြင်ဆင်ဆဲကား (In-service) အရေအတွက်။
* **Revenue Graph:** နေ့စဉ်/အပတ်စဉ် ဝင်ငွေပြဇယား။
* **Active Maps:** လက်ရှိ လမ်းပေါ်ရောက်နေတဲ့ ကားအားလုံးရဲ့ Live GPS အကျဉ်းချုပ်။

---

## ၂။ Fleet Management Module (The Assets)

ကားတွေကို စီမံခန့်ခွဲတဲ့ အပိုင်းပါ။

* **Car Inventory:** ကားအမျိုးအစား၊ မော်ဒယ်၊ ဆီစားနှုန်း၊ Bluetooth Key ID နှင့် Status (Active/Inactive)။
* **Telematics Hub:** ကားတစ်စီးချင်းစီရဲ့ Battery Level၊ Fuel/EV Charge Level နှင့် အင်ဂျင်ကျန်းမာရေးကို Remote ကြည့်ရှုခြင်း။
* **Geofence Settings:** ကားတစ်စီးချင်းစီ သွားလာခွင့်ရှိတဲ့ Zone (Polygon) သတ်မှတ်ခြင်းနှင့် Zone ကျော်ပါက Alert ပေးခြင်း။
* **Digital Key Management:** Bluetooth Key တွေကို Reset ချခြင်း သို့မဟုတ် အဝေးမှ Lock/Unlock လုပ်ခြင်း။

---

## ၃။ Booking & Reservation Module (The Operations)

* **Live Booking Calendar:** ဘယ်ကားက ဘယ်နေ့မှာ အားမလဲဆိုတာကို Grid view နဲ့ ကြည့်ခြင်း။
* **Dynamic Pricing Engine:**
* *Rules Setup:* ရုံးပိတ်ရက်များတွင် ဈေးမြှင့်ရန် သို့မဟုတ် ကားနည်းနေချိန်တွင် ဈေးတင်ရန် Rule များ သတ်မှတ်ခြင်း။


* **Channel Manager:** Booking.com သို့မဟုတ် အခြား Third-party များမှ လာသော booking များကို Dashboard တစ်ခုတည်းမှ Sync လုပ်ခြင်း။

---

## ၄။ User & KYC Management (Trust & Safety)

* **Driver Verification Queue:** User တင်ထားသော လိုင်စင်နှင့် မှတ်ပုံတင်များကို AI-scan ဖတ်ထားသော ရလဒ်အား Admin က အတည်ပြုပေးခြင်း (Manual Override)။
* **Fraud Detection:** User တစ်ယောက်က ကားကို ကြမ်းတမ်းစွာ မောင်းနှင်ခြင်း သို့မဟုတ် ငွေပေးချေမှု ပြဿနာရှိခြင်းတို့ကို "Blacklist" မှတ်ခြင်း။
* **Booking History:** User တစ်ယောက်ချင်းစီရဲ့ ယခင်ငှားရမ်းမှု မှတ်တမ်း။

---

## ၅။ Payment & Billing Module (The Finance)

* **Auto-billing Dashboard:** Stripe/PayPal မှတစ်ဆင့် ငွေဝင်မှုများကို စောင့်ကြည့်ခြင်း။
* **Invoicing:** Tax invoice များကို အလိုအလျောက် ထုတ်ပေးခြင်း။
* **Deposit Management:** ကားမငှားခင် ကြိုတင်ဖြတ်ထားသော Deposit (Security Bond) များကို ကားပြန်အပ်ချိန်တွင် ပြန်အမ်းခြင်း (Refund) သို့မဟုတ် ဒဏ်ကြေးဖြတ်ခြင်း။

---

## ၆။ System Settings & Logs (The Infrastructure)

* **Audit Logs:** Dashboard ထဲမှာ ဘယ်သူက ဘာပြင်သွားလဲ (ဥပမာ- ဘယ် manager က ဈေးလျှော့ပေးလိုက်လဲ) ဆိုတာကို မှတ်တမ်းတင်ခြင်း။
* **API Configuration:** GPS Device တွေနဲ့ Bluetooth Key Server တွေရဲ့ API Key များကို စီမံခြင်း။

---

### Suggested Navigation Menu Structure (Sidebar)

Professional Dashboard တစ်ခုရဲ့ Sidebar က ဒီလို ပုံစံမျိုး ဖြစ်သင့်ပါတယ်-

| Level 1 Menu | Level 2 (Sub-menus) |
| --- | --- |
| **Dashboard** | Overview, Live Maps |
| **Fleet** | All Vehicles, Maintenance, Geofencing |
| **Bookings** | New Bookings, Active Rentals, Schedule |
| **Customers** | KYC Verification, User Directory, Blacklist |
| **Finances** | Transactions, Pricing Rules, Refunds |
| **External Sync** | OTA Sync (Booking.com), Google Ads |
| **Settings** | Admin Roles, API Config, System Logs |

---

### Professional Implementation Tip:

သင်ပြောထားတဲ့ **Digital Key (Bluetooth Unlock)** အတွက် Dashboard ထဲမှာ "Emergency Unlock" ခလုတ်တစ်ခု ပါရပါမယ်။ User ရဲ့ ဖုန်းက Bluetooth အလုပ်မလုပ်တော့တဲ့အခါ Manager က Dashboard ကနေ ကားကို လှမ်းဖွင့်ပေးနိုင်ဖို့ ဖြစ်ပါတယ်။

ဒါ့အပြင် **Dynamic Pricing** အတွက် "Algorithm Simulator" တစ်ခု Dashboard ထဲမှာ ထည့်ထားရင် ပိုကောင်းပါတယ်။ ဈေးနှုန်းတွေ ပြောင်းလိုက်ရင် Booking ဘယ်လောက် တက်လာနိုင်မလဲဆိုတာကို ခန့်မှန်းကြည့်လို့ရမှာ ဖြစ်ပါတယ်။

ဒီ Module တွေထဲက ဘယ်တစ်ခုကို Coding အပိုင်း (ဥပမာ- Schema ပိုင်း သို့မဟုတ် UI ပိုင်း) အသေးစိတ် သိချင်ပါသလဲ?






