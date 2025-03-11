---
date: 2025-03-03
tags:
  - js
  - vue
---

| 暴露方式                     | 语法                                      | 导入方式                                           | 适用场景        |
| ------------------------ | --------------------------------------- | ---------------------------------------------- | ----------- |
| **分别暴露（Named Export）**   | `export const name = 'Alice'`           | `import { name } from './module.js'`           | 适用于多个功能点    |
| **统一暴露（Bulk Export）**    | `export { name, sayHello }`             | `import { name, sayHello } from './module.js'` | 代码较多时统一管理导出 |
| **默认暴露（Default Export）** | `export default function sayHello() {}` | `import saySomething from './module.js'`       | 每个模块的主要功能   |

| 导出方式         | 语法                                 | 导入方式                         | 备注                  |
| ------------ | ---------------------------------- | ---------------------------- | ------------------- |
| **默认导出**     | `export default function`          | `import 任意名称 from '文件路径'`    | 每个文件只能有一个 `default` |
| **命名导出**     | `export function` / `export const` | `import { 名称 } from '文件路径'`  | 需要使用 `{}`           |
| **别名导出**     | `export { 名称 as 新名称 }`             | `import { 新名称 } from '文件路径'` | 改变导入时的名称            |
| **导出所有内容**   | `export * from '文件路径'`             | `import { 名称 } from '文件路径'`  | 重新导出另一个模块           |
| **重新导出默认导出** | `export { default } from '文件路径'`   | `import 任意名称 from '文件路径'`    | 用于代理导出              |

如果你想让代码更清晰易维护，一般推荐：

- **如果只有一个主功能，使用 `export default`**
- **如果有多个功能，使用 `export` 命名导出**
- **如果需要组合多个模块，使用 `export * from`**