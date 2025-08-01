
# 格式化设置

创建.vscode/settings.json

```json
{
  "editor.codeActionsOnSave": {
    "source.fixAll.biome": "explicit"
  },
  "[typescript]": {
    "editor.defaultFormatter": "biomejs.biome"
  },
  "[typescriptreact]": {
    "editor.defaultFormatter": "biomejs.biome"
  },
  "[javascript]": {
    "editor.defaultFormatter": "biomejs.biome"
  },
  "[javascriptreact]": {
    "editor.defaultFormatter": "biomejs.biome"
  },
  "typescript.suggest.autoImports": true,
  "javascript.suggest.autoImports": true
}
```

![[使用工作区ts版本.png]]

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
{[...mockImages, ...mockImages, ...mockImages].map((image, index) => (
	<div key={`${image.id}-${index}`} className="w-48">
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


🔹 这个文件会用 `schema.ts` 里的结构去操作数据库。

## 设置drizzle

在vercel的free方案中使用neon来为postgres提供代理

pnpm add 缺失的依赖项

在vercel的项目Storage界面，选择Drizzle，粘贴至

```tsx
// index.ts
// src/db.ts
import { drizzle } from "drizzle-orm/neon-http";
import { neon } from "@neondatabase/serverless";
import { config } from "dotenv";
import * as schema from "./schema";

config({ path: ".env" }); // or .env.local

const sql = neon(process.env.DATABASE_URL);
export const db = drizzle({ client: sql, schema });
```

必要时补充错误处理，解决lint报错

```tsx
if (!process.env.DATABASE_URL) {
  throw new Error('DATABASE_URL is not defined');
}
```

如果项目中使用的是DATABASE_URL（默认），就将.env的 DATABASE_URL 加入vercel上的Environment Variable

否则就将项目中的DATABASE_URL都改为POSTGRES_URL（在创建数据库时设置的前缀）

## drizzle的使用以及校验配置

t3默认存在的scheme.ts，中有posts表

```ts
export const posts = createTable(
	"post",
	(d) => ({
		id: d.integer().primaryKey().generatedByDefaultAsIdentity(),
		name: d.varchar({ length: 256 }),
		createdAt: d
			.timestamp({ withTimezone: true })
			.default(sql`CURRENT_TIMESTAMP`)
			.notNull(),
		updatedAt: d.timestamp({ withTimezone: true }).$onUpdate(() => new Date()),
	}),
	(t) => [index("name_idx").on(t.name)],
);
```


t3已经配置了Drizzle kit，使项目支持Drizzle相关的命令

```
pnpm run db:push  
pnpm run db:studio
```

`db:push` = **同步你的 schema.ts 到真实数据库**
`db:studio`可以打开Drizzle的网页对数据表进行数据的可视化操作

![[drizzleStudio.png]]

在这里的数据能实时反应到本地的localhost中
而部署后，代码和数据库是“静态”的，**不会自己监听你的代码改动**。
需要重新部署以适应更新

为Page设置动态路由，这样不会应用ServerSide的Cache，而是每次访问页面都对数据库重新获取数据，这样部署的版本也可以通过刷新同步数据

```tsx
export const dynamic = "force-dynamic";
```
# 修改数据库

```tsx
export const images = createTable(
	"image",
	(d) => ({
		id: d.integer().primaryKey().generatedByDefaultAsIdentity(),
		name: d.varchar("name", { length: 256 }).notNull(),
		url: d.varchar("url", { length: 1024 }).notNull(),
		createdAt: d
			.timestamp({ withTimezone: true })
			.default(sql`CURRENT_TIMESTAMP`)
			.notNull(),
		updatedAt: d.timestamp({ withTimezone: true }).$onUpdate(() => new Date()),
	}),
	(t) => [index("name_idx").on(t.name)],
);
```

将更改应用至serverSide，然后

```powershell
pnpm run db:push

Is t3gallery_demo_image table created or renamed from another table?
❯ + t3gallery_demo_image                       create table
  ~ t3gallery_demo_post › t3gallery_demo_image rename table

· You're about to delete t3gallery_demo_post table with 2 items

THIS ACTION WILL CAUSE DATA LOSS AND CANNOT BE REVERTED

Do you still want to push changes?
  No, abort
❯ Yes, I want to remove 1 table,
```

这时设置完成后，生产环境会中断出错，这是正常现象

在DrizzleStudio将mockData应用至真正的数据库

```tsx
export default async function HomePage() {
	// 按时间排序
	const images = await db.query.images.findMany({
		orderBy: (model, { desc }) => desc(model.id),
	});

	return (
		<main className="">
			<div className="flex flex-wrap gap-4">
				{[...images, ...images, ...images].map((image, index) => (
					<div key={`${image.id}-${index}`} className="flex w-48 flex-col">
						<img src={image.url} alt="" />
						<p className="text-gray-500 text-sm">{image.name}</p>
					</div>
				))}
			</div>
		</main>
	);
}
```

# 使用Clerk来做Auth

在clerk创建组件，将生成的env内容加入至vercel

pnpm add @clerk/nextjs

src下创建middleware.ts

```ts
import { clerkMiddleware, createRouteMatcher } from '@clerk/nextjs/server'

const isProtectedRoute = createRouteMatcher(['/dashboard(.*)', '/forum(.*)'])

export default clerkMiddleware(async (auth, req) => {
  if (isProtectedRoute(req)) await auth.protect()
})

export const config = {
  matcher: [
    // Skip Next.js internals and all static files, unless found in search params
    '/((?!_next|[^?]*\\.(?:html?|css|js(?!on)|jpe?g|webp|png|gif|svg|ttf|woff2?|ico|csv|docx?|xlsx?|zip|webmanifest)).*)',
    // Always run for API routes
    '/(api|trpc)(.*)',
  ],
}
```

在root layout中导入ClerkProvider，并包裹全局

```tsx
		<ClerkProvider>
		  <html lang="en" className={`${geist.variable}`}>
			<body className="flex flex-col gap-4">
				<TopNav />
				{children}
			</body>
		  </html>
		</ClerkProvider>
```

创建_components文件夹，文件夹名字前有下划线，意味着组件不会被next app router包含在路由中

