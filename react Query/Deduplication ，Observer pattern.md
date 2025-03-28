
invocation 调用

cache中同一个key，一旦被赋予初值，当再次调用Fn时，数据不会更新，
因为cache中这个key已经有值在内部了，即便是不同组件也是如此

这个特性就是deduplication

```js
function useLuckyNumber() {
  return useQuery({
    queryKey: ['luckyNumber'],
    queryFn: () => {
      return Promise.resolve(Math.random())
    }
  })
}
```

多组件使用时，可以把Query逻辑放入自定义hook方便复用

这体现观察者模式Observer pattern（一种设计模式）
使用useQuery创建一个观察者，观察Querykey，
当它观察到数据变化时，观察者会启动重新渲染，以保持UI和cache中数据的同步



- **Deduplication 只是防止短时间内相同请求重复执行，不影响缓存更新。**
- **缓存的数据仍然会更新，取决于 staleTime、refetch、mutation 等机制。**
	- **组件重新挂载，React Query 可能会自动重新获取数据**。
	- 你可以用 `refetch()` 让数据手动更新
	- 如果使用 `useMutation` 进行数据修改，可以手动更新缓存
	- 垃圾回收（GC）会清理缓存
- **如果 `staleTime` 设为 `Infinity`，则数据不会被自动认为过时，除非手动触发刷新。**
- **如果 `cacheTime` 到期，数据会被清理，下一次查询时会重新获取。**