
# 通过URL参数实现搜索

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

1. 创建搜索框Search组件，
	1. 在input内获取用户输入
	2. 默认是 Server Component 所以先声明use client
	3. input内加入onChange事件监听，捕获用户输入
2. 更新URL
	1. `useSearchParams` - 允许你访问当前 URL 的参数。
	2. `const params = new URLSearchParams(searchParams);` 创建一个新的 `URLSearchParams` 实例
	3. 在onchange绑定的函数上，利用此params实例设置query属性的参数
	4. 将查询的更新，同步在URL上
		1. 在useRouter中导出replace成员函数
		2. 通过usePathname获取当前路径，作为replace的参数
		3. 在onchange绑定的函数最后，调用`replace(${pathname}?${params.toString()});`完成用户输入到URL的更新
3. 保持输入和URL同步
	1. 即进入链接时，通过URL向input的填充，以保证双向的确认，也能使分享链接等操作包含URL信息
	2. 在input中设置`defaultValue={searchParams.get('query')?.toString()}`即可
4. 根据查询，更新数据
	1. Page组件通过searchParams获取URL参数，并作为props传递给子组件
	2. 子组件利用Props的数据作为fetch的参数
	  用useSearchParams Hooks获取URL参数和用searchParams Props获取URL参数，区别是在客户端组件还是在服务端组件，
	
	- `<Search>` 是一个客户端组件，所以你使用了 `useSearchParams()` 钩子从客户端访问参数。
	- `<Table>` 是一个从服务器获取数据的组件，所以你可以将 `searchParams` 属性从页面传递给组件。
	- 一般来说，如果你想从客户端读取参数，使用 `useSearchParams()` 钩子，这样可以避免回到服务器。
5. 防抖，防止频繁请求
	1. import { useDebouncedCallback } from 'use-debounce';
	2. const handleSearch = useDebouncedCallback((term) => {···}, 300);

# 通过URL参数实现分页

