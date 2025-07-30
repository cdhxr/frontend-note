https://nextjs.org/learn/dashboard-app/error-handling

## [Handling all errors with `error.tsx`  
使用 `error.tsx` 处理所有错误](https://nextjs.org/learn/dashboard-app/error-handling#handling-all-errors-with-errortsx)

The `error.tsx` file can be used to define a UI boundary for a route segment. It serves as a **catch-all** for unexpected errors and allows you to display a fallback UI to your users.  
`error.tsx` 文件可用于为路由片段定义一个 UI 边界。它作为捕获所有意外错误的容器，并允许你向用户展示备用 UI。

Inside your `/dashboard/invoices` folder, create a new file called `error.tsx` and paste the following code:  

```tsx
'use client';
 
import { useEffect } from 'react';
 
export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  useEffect(() => {
    // Optionally log the error to an error reporting service
    console.error(error);
  }, [error]);
 
  return (
    <main className="flex h-full flex-col items-center justify-center">
      <h2 className="text-center">Something went wrong!</h2>
      <button
        className="mt-4 rounded-md bg-blue-500 px-4 py-2 text-sm text-white transition-colors hover:bg-blue-400"
        onClick={
          // Attempt to recover by trying to re-render the invoices route
          () => reset()
        }
      >
        Try again
      </button>
    </main>
  );
}
```

There are a few things you'll notice about the code above:  
关于上述代码，有几个地方值得注意：

- **"use client"** - `error.tsx` needs to be a Client Component.  
    "使用客户端组件" - `error.tsx` 需要是一个客户端组件。
- It accepts two props:  它接受两个属性：
    - `error`: This object is an instance of JavaScript's native [`Error`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error) object.  
        `error` : 这个对象是 JavaScript 原生 `Error` 对象的实例。
    - `reset`: This is a function to reset the error boundary. When executed, the function will try to re-render the route segment.  
        `reset` : 这是一个用于重置错误边界的函数。当执行时，该函数会尝试重新渲染路由片段。