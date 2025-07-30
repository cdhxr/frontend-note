---
date: 2025-07-22
---
使用数据库中的数据的过程：
- 在主组件中导入获取数据的函数，
- 存入临时变量中，
- 然后传参至需要数据的子组件中

关键是第一步获取数据的函数，即在nextjs中，如何获取数据

# **前提**：

客户端环境不能直接写数据查询，因为会暴露数据库secrets，带来安全风险
直接的查询只能在服务端存在

# **方案一：使用API层获取数据**：

从客户端获取数据时，你希望有一个在服务器上运行的 API 层，以避免将你的数据库秘密暴露给客户端

# **方案二：使用React Server Component** 直接进行Data Query

是一种仅仅在服务端运行的组件

如果你使用的是 React Server Components（在服务器上获取数据），你可以跳过 API 层，直接查询你的数据库，而不用担心将数据库秘密暴露给客户端。

默认情况下，Next.js 使用 **React Server Components** ，

并有以下优点：

**安全**：由于服务器组件在服务器上运行，你可以直接查询数据库，无需额外的 API 层。这让你免于编写和维护额外的代码。

**性能**： 服务器组件在服务器上运行，因此你可以将昂贵的请求数据和逻辑保留在服务器上，仅将结果发送到客户端。

**原生适配性**：服务器组件支持 JavaScript Promise，为像数据获取这样的异步任务提供了原生解决方案。你可以使用 `async/await` 语法，而无需 `useEffect` 、 `useState` 或其他数据获取库。

# 如何编写查询语句

写查询的方式主要是 SQL、ORM 和 Query Builder，它们只是“怎么写查询”的不同工具，最终目标都是操作数据库。
## 直接使用SQL

手动写查询语句，最贴近数据库底层，控制最强。

使用postgres.js来写查询的例子

```ts
import postgres from 'postgres';

const sql = postgres(process.env.POSTGRES_URL!, { ssl: 'require' });

export async function fetchRevenue() {
  try {
    const data = await sql<Revenue[]>`SELECT * FROM revenue`;
    return data;
  } catch (error) {
    console.error('Database Error:', error);
    throw new Error('Failed to fetch revenue data.');
  }
}

```

## 使用ORM

用代码对象来操作数据库，让查询像写普通逻辑一样。

gpt4：Prisma的例子

```ts
// lib/data.ts
import { prisma } from './prisma';

export async function fetchRevenue() {
  try {
    const data = await prisma.revenue.findMany();
    return data;
  } catch (error) {
    console.error('Database Error:', error);
    throw new Error('Failed to fetch revenue data.');
  }
}
```

```ts
model Revenue {
  id        Int      @id @default(autoincrement())
  amount    Float
  date      DateTime
  source    String
}
```

```ts
// lib/prisma.ts
import { PrismaClient } from '@prisma/client';

export const prisma = new PrismaClient();
```

# 请求瀑布

是指请求执行之间需要等待一个请求执行完才能执行下一个请求，、
这样会对数据进行阻塞，降低了页面渲染的速度

应该出现请求瀑布：即一个请求依赖于另一个请求的完成的场景
在没有逻辑关系的请求间不应该出现请求瀑布

在 JavaScript 中，你可以使用 `Promise.all()` 或 `Promise.allSettled()` 函数同时发起所有 promises。

同时开始执行所有数据获取操作，这比在瀑布流中等待每个请求依次完成要快。

然而，完全依赖这种 JavaScript 模式有一个缺点：如果有一个数据请求比其他所有请求都慢怎么办？让我们在下一章中继续探讨。
