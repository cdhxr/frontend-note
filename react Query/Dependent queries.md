
获取另一个请求结果的数据

可以将两个Query耦合在一起，当做一个Query来看待：

```js
function useMovieWithDirectorDetails(title) {
  return useQuery({
    queryKey: ['movie', title],
    queryFn: async () => {
      const movie = await fetchMovie(title)
      const director = await fetchDirector(movie.director)

      return { movie, director }
    },
  })
}
```

### 1. 它们总是一起获取和重新获取数据

因为这两个请求属于同一个查询，即使我们想要，也无法只重新获取部分数据。例如，我们无法仅重新获取电影信息，而不重新获取导演信息。

出于同样的原因，我们也无法为每个请求设置不同的配置（如 `staleTime` 或 `refetchInterval`）。

在 React Query 看来，这只是一个资源。即便数据来自两个不同的网络请求，它们仍然被视为一个整体。

### 2. 它们会一起进入错误状态

即使只有其中一个请求失败，整个查询也会进入错误状态。

这可能符合你的需求，但有时候你可能希望即使导演的信息获取失败，仍然能够显示电影的信息。

### 3. 请求不会去重

这可能是最大的缺点。由于这两个请求共享同一个 `queryKey`，你无法判断某些数据是否已经被获取并缓存。

例如，如果有第二部电影由同一位导演执导，你无法复用之前缓存的数据，并且不会进行请求去重。相反，系统会再次发出相同的请求，并将数据存储在缓存的另一个位置。

如果你打开开发者工具并查看缓存条目，你会发现相同的导演数据被存储了两次，同时也发送了两次相同的请求。

# dependent queries

在这种特定的使用场景下，最好采取不同的方法，以避免上述权衡。原因在于，**导演和电影是完全不同的实体**。如果我们将它们分别缓存，我们在应用程序中使用它们时就会更加灵活。

从某种意义上来说，**依赖查询（dependent queries）** 可以被视为一种特殊形式的“按需获取”（fetching on demand）。不过，不同之处在于，**它不是等到某个事件发生才触发查询，而是等到另一个查询完成后再进行查询**。

依旧分成两个Query，但等待另一个Query拿完了，才那另一个Query(通过enable参数实现)
所以一个Query依赖于另一个Query，顾名思义即可知其为dependency query

```js
function useMovie(title) {
  return useQuery({
    queryKey: ['movie', title],
    queryFn: async () => fetchMovie(title),
  })
}

function useDirector(id) {
  return useQuery({
    queryKey: ['director', id],
    queryFn: async () => fetchDirector(id),
    enabled: id !== undefined
  })
}
```

我们也可以使用hooks抽象这个逻辑

```js
function useMovie(title) {
  return useQuery({
    queryKey: ['movie', title],
    queryFn: async () => fetchMovie(title),
  })
}

function useDirector(id) {
  return useQuery({
    queryKey: ['director', id],
    queryFn: async () => fetchDirector(id),
    enabled: id !== undefined
  })
}

function useMovieWithDirectorDetails(title) {
  const movie = useMovie(title)
  const directorId = movie.data?.director
  const director = useDirector(directorId)

  return {
    movie,
    director
  }
}
```

请注意，我们传递给 `useDirector` 的 `id` 来自电影查询（movie query）。当 `id` 为 `undefined` 时（即电影查询仍处于 `pending` 状态），导演查询（director query）将被**禁用**。

movie.director?.name 的含义是：
- 如果 movie.director 存在，则访问 name。
- 如果 movie.director 为空或 undefined，整个表达式返回 undefined，而不会出错。

做出这些更改后，我们需要分别处理两个**独立的加载和错误状态**。但通常来说，这是一种正确的取舍，因为这样可以提供更大的灵活性。**具体到这个例子，它使我们能够仅基于电影查询来显示加载指示器和错误消息，而不受导演查询的影响**。

```js nums {4,5,6,7,8,9,10,16,17,18,19}
function Movie({ title }) {
  const { movie, director } = useMovieWithDirectorDetails(title)

  if (movie.status === 'pending') {
    return <div>...</div>
  }

  if (movie.status === 'error') {
    return <div>Error fetching {title}</div>
  }

  return (
    <p>
      Title: {movie.data.title} ({movie.data.year})
      <br />
      { director?.data
        ? <> Director: {director.data.name}</>
        : null
      }
    </p>
  )
}
```

