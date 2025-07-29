
如上所述，您将使用 URL 搜索参数来管理搜索状态。

如果您习惯于使用客户端状态来做这件事，这种模式可能对您来说比较新。

- 可书签和可分享的 URL：由于搜索参数在 URL 中，用户可以保存应用程序的当前状态，包括他们的搜索查询和过滤器，以供将来参考或分享。

- 服务器端渲染：URL 参数可以直接在服务器上使用以渲染初始状态，这使得处理服务器渲染更加容易。

- 分析和跟踪：将搜索查询和过滤器直接放在 URL 中，可以更轻松地跟踪用户行为，而无需额外的客户端逻辑。

这些是 Next.js 客户端钩子，你将使用它们来实现搜索功能：

`useSearchParams` - 允许你访问当前 URL 的参数。

例如，
这个 URL `/dashboard/invoices?page=1&query=pending` 的搜索参数看起来是这样的： `{page: '1', query: 'pending'}` 。

`usePathname` - 让你读取当前 URL 的路径名。
例如，对于路由 `/dashboard/invoices` ， `usePathname` 将返回 `'/dashboard/invoices'` 。

`useRouter` - 允许在客户端组件中以编程方式在路由之间进行导航。

