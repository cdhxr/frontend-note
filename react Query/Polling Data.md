
1. React Query will always give us cached data instantly, even if it's not fresh.
2. By default, all queries are instantly considered stale since staleTime defaults to 0.
3. If a query is stale, React Query will refetch the data and update the cache when a trigger occurs
4. There are four triggers: 
	- when a queryKey changes,
	- a new Observer mounts,
	- the window receives a focus event,
	- and the device goes online

如何在特定时间点执行fetch？

假设你正在为公司构建一个分析仪表盘。很可能，你会希望确保数据在一定时间后始终是最新的，无论是否发生了“触发”事件。

为了实现这一点，你需要一种方法，告诉 React Query 无论如何都要在特定的时间间隔内定期调用 `queryFn`。

这个概念叫做轮询（polling），你可以通过在调用 `useQuery` 时传递 `refetchInterval` 属性来实现这一点。

```js
useQuery({
  queryKey: ['repos', { sort }],
  queryFn: () => fetchRepos(sort),
  refetchInterval: 5000 // 5秒
})
```

在这个例子中，设置 `refetchInterval` 为 5000 时，`queryFn` 将每隔 5 秒被调用一次，无论是否有触发事件或者查询是否还有新鲜数据。

因此，`refetchInterval` 最适合那些数据变化频繁并且你希望缓存尽可能保持最新的场景。

需要注意的是，`refetchInterval` 的定时器是智能的。如果在定时器倒计时期间发生了传统的触发事件并更新了缓存，那么定时器将重置。

`refetchInterval` 的另一个有趣之处是，你可以在满足特定条件时继续轮询。这在某些执行昂贵任务的接口中非常有用，你可能希望轮询直到任务完成。

为了解决这个问题，你可以将一个函数传递给 `refetchInterval`。当你这样做时，that function will accept the query as an argument，让你检查the query's state，并决定是否继续轮询。如果你从函数中返回 `false`，则轮询将停止。

例如，在接收到 JSON 响应，并显式设置 `finished` 属性后，我们的 `refetchInterval` 函数会像这样：

```js
useQuery({
  queryKey: ['totalAmount'],
  queryFn: () => fetchTotalAmount(),
  refetchInterval: (query) => {
    if (query.state.data?.finished) {
      return false
    }
    return 3000 // 3秒
  }
})
```

现在，一旦缓存中的数据 `finished` 属性被设置为 `true`，轮询将停止。

