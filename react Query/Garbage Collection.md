在 React Query 中，原因是它的缓存存储在内存中，而内存是有限的。如果不定期清理缓存，它会无限增长，导致低端设备的内存问题。

但它使用了一种基于时间的垃圾回收器，叫做 `gcTime`。这个设置决定了查询数据应该何时从缓存中移除，默认值是 5 分钟。

你可能会想：“那么这是不是意味着 React Query 会在数据被添加到缓存后 5 分钟就把它移除？” 其实不是。

只要数据仍在被积极使用，它就不会被垃圾回收。当然，这就引出了另一个明显的问题，那到底什么是“积极使用”呢？

记得我们之前提到的每次组件挂载时，都会为每个 `useQuery` 调用创建一个观察者吗？这就是让查询变得“活跃”的原因。按照这个定义，任何没有观察者的查询被认为是“非活跃的”。很方便的是，当组件卸载并从 DOM 中移除时，观察者会被销毁。如果没有剩下的观察者，React Query 就可以确定它应该启动该缓存条目的垃圾回收计时器。

每次搜索都会生成一个新的缓存条目，并且只要我们搜索新的内容，之前的条目就变得非活跃（因为观察者会切换到观察新的 `queryKey`）。

如果在接下来的 5 分钟内，我们再次搜索相同的内容，数据会从缓存中返回（如果数据过期，也可能会触发后台重新获取）。

但是如果我们在将来某个超过 5 分钟的时间点再次搜索该内容，初始观察者已经被移除，缓存条目也已经被删除，用户就会看到加载指示器。

Of course, `gcTime` is customizable and can be set to any value you see fit when you invoke `useQuery`.

```js{7}
function useIssues(search) {
  return useQuery({
    queryKey: ['issues', search],
    queryFn: () =>  fetchIssues(search),
    enabled: search !== '',
    staleTime: 5000, // 5 seconds
    gcTime: 3000, // 3 seconds
  })
}
```