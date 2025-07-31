

#  deploy pur repo to Vercel

在Vervcel中导入项目，尝试deploy，但是会失败，因为在Environment Variable中必须一个数据库的设置

可以通过env.example的内容先填充，暂时性的保证部署的成功

具体在t3 app的项目页，setting，environment Variable

完成后再跳出的弹窗中点击redeploy即可部署成功

![[设置环境变量.png]]

现在push到main上时，就会进行自动的部署

# uploadthing托管图片资源

## why

> 💭“既然我可以直接把图片放在本地项目（比如 `public` 文件夹）里，为什么还需要像 UploadThing 这种文件上传服务？”

|需求|选用|
|---|---|
|固定图片、静态展示|✅ VSCode 本地图|
|用户上传、动态内容|✅ UploadThing 等|

上传服务的核心价值之一，就是帮你把静态资源转成「可公开访问的 URL」。

### 🧠 本质：
把用户上传的文件存储到云端，并返回 URL 给前端或后端使用。

### ✅ 图片有 URL 的好处

| 好处               | 解释                                          |
| ---------------- | ------------------------------------------- |
| 🌍 **可被任何设备访问**  | 有 URL 的图片可以在网页、App、远程文档中直接访问，不受本地文件限制       |
| 🔁 **易于复用**      | 同一张图可以被多处引用，节省空间                            |
| 📱 **跨平台支持**     | 无论手机、电脑、平板，只要有网络都能访问                        |
| 📤 **方便用户上传与展示** | 用户上传的图片生成 URL 后，直接存数据库、用于展示                 |
| 🚀 **支持 CDN 加速** | URL 通常指向 CDN（如 Cloudflare、Vercel Blob），加载更快 |
| 🧩 **更好集成数据库**   | 在数据库中只需要保存图片的 URL 字符串，不用存整个图片内容             |
| 🔒 **可以设置访问权限**  | 一些 URL 可设置临时访问、签名验证，提升安全性                   |

| 场景     | 没有上传服务                     | 有上传服务                              |
| ------ | -------------------------- | ---------------------------------- |
| 用户上传头像 | 你只能拿到一张图片，得手动存文件           | 上传后返回一个 URL，直接存数据库                 |
| 展示图片   | 只能引用本地路径，如 `/images/a.png` | 可用远程 URL，如 `https://cdn.com/a.png` |
| 跨设备访问  | 无法跨设备                      | 任何设备都能加载这个 URL                     |


## how

https://uploadthing.com/dashboard/

在官网创建项目名，然后在File页进行上传即可

# 参考的UI基本布局的搭建

## 设置global.css

设置主题，字体，背景颜色，文字颜色等等

```css
body {
    @apply bg-gray-900 text-white;
}
```

## 准备一些模拟的数据（mockData）

```tsx
const mockUrls = [...];

const mockImages = mockUrls.map((url, index) => ({
	id: index + 1,
	url,
}));

//一个让数据翻倍的小技巧
{[...mockImages, ...mockImages, ...mockImages].map((image) => (
	<div key={image.id} className="w-48">
		<img src={image.url} alt="" />
	</div>
))}

```

## 定义一个顶部导航栏

```tsx
			...
			<body className="flex flex-col gap-4">
				<TopNav />
				{children}
			</body>
```

使用flex flex-col gap-4，而不是直接使用margin，因为这样更加符合其原本含义


# 优化构建过程

在构建时忽略eslint错误和typescript错误，加快构建速度，

取而代之的是在github Action上检查它们，只让他们不要阻塞build的过程

```tsx
//next.config.js
const config = {
    typescript: {
        ignoreBuildErrors: true,
    },
    eslint: {
        ignoreDuringBuilds: true,
    },
};
```

# 设置数据库

![[数据库设置.png]]

在项目页的storage中，根据引导创建数据库
Advanced option的URL前缀使用默认的POSTGRES

![[env配置.png]]

复制env.local的内容，进入代码中的.env进行粘贴，设置好数据库连接

# 设置drizzle

## ✅ 1. `schema.ts` 

> 定义数据库的**表结构**，告诉 ORM（如 drizzle）你的数据库长什么样。

### 举例：

```ts
// schema.ts
import { pgTable, serial, text } from "drizzle-orm/pg-core";

export const users = pgTable("users", {
  id: serial("id").primaryKey(),
  name: text("name").notNull(),
});
```

🔹 这相当于你在数据库里执行：

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  name TEXT NOT NULL
);
```

---

## ✅ 2. `index.ts` 

> 通常作为**项目的入口点**，用于执行实际的逻辑，比如：连接数据库、执行查询、运行程序等。

### 举例：

```ts
// index.ts
import { db } from "./db";
import { users } from "./schema";

async function main() {
  const result = await db.select().from(users);
  console.log(result);
}

main();
```

🔹 这个文件会用 `schema.ts` 里的结构去操作数据库。

## 设置drizzle

在vercel的free方案中使用neon来为postgres提供代理

```tsx
// index.ts
// src/db.ts
import { drizzle } from "drizzle-orm/neon-http";
import { neon } from "@neondatabase/serverless";
import { config } from "dotenv";
import * as schema from "./schema";

config({ path: ".env" }); // or .env.local

if (!process.env.DATABASE_URL) {
  throw new Error('DATABASE_URL is not defined');
}
const sql = neon(process.env.DATABASE_URL);
export const db = drizzle({ client: sql, schema });
```
