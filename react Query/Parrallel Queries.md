网络请求的一个明显原则是，能够并行做的事情越多越好

最简单的方式是用多个useQuery，但它导致不同的组件加载时间可能不同，这可能会导致诡异的布局移动

如果希望它们全部加载完后再显示，有几个选择

1. 组合两个Query，包装在promise对象中

```js
function useReposAndMembers() {
  return useQuery({
    queryKey: ['reposAndMembers'],
    queryFn: () => {
      return Promise.all([fetchRepos(), fetchMembers()])
    }
  })
}
```

缺点如前篇所述，我们真正希望的是既能单独缓存，又能共同调用在一个hook的办法

This is essentially what the `useQueries` hook does.

```js
function useReposAndMembers() {
  return useQueries({
    queries: [
      {
        queryKey: ['repos'],
        queryFn: fetchRepos,
      }, 
      {
        queryKey: ['members'],
        queryFn: fetchMembers,
      }
    ]
  })
}

...

const [repos, members] = useReposAndMembers()
```

我们可以通过js语法，轻松的从数组中获得任何值

```js
const queries = useReposAndMembers()
//判断是否有在fetching的布尔值
const areAnyPending = queries.some(
  query => query.status === 'pending'
)
//判断是否全部在fetching的布尔值
const isAnyPending = queries.every(
  query => query.status === 'pending'
)
```

# 传入给queries数组可以是动态的

```js
function useIssues(repos) {
  return useQueries({
    queries: repos?.map((repo) => ({
      queryKey: ['repos', repo.name, 'issues'],
      queryFn: async () => {
        const issues = await fetchIssues(repo.name)
        return { repo: repo.name, issues }
      }
    })) ?? []  //避免undefined
  })
}
```

通过map参数，可以将数据批量取进Query，这将给我们带来很多便利

例如，假设我们想要更新应用的 UI，以显示所有仓库中的问题（issues）总数。
如果你只是为每个仓库渲染一个单独的组件，并在其中获取问题数据，那么计算所有仓库的问题总数将会很麻烦，因为这些查询是相互独立的。

然而，如果像我们最初那样使用 `useQueries`，那么你就有几种选择来实现这一需求。

1. First, you could just use the **Power of JavaScript™** to derive the total number of issues from the array of queries.

```js
const repos = useRepos()
const issues = useIssues(repos.data)

const totalIssues = issues
  .map(({ data }) => data?.issues.length ?? 0)
  .reduce((a, b) => a + b, 0)
//js魔法
...
```

2. Or if you prefer, `useQueries` also comes with a `combine` option that does the same thing, just built in to the `useQueries` API itself.

其工作原理是，你需要传递一个函数，该函数将查询数组作为其第一个参数，并返回 `useQueries` 的最终结果。

例如，如果我们想要使用 `combine` 方法，在 `useIssues` 返回的对象中添加一个 `totalIssues` 属性，我们可以这样做：

```js nums {10,11,12,13,14,15,22}
function useIssues(repos) {
  return useQueries({
    queries: repos?.map((repo) => ({
      queryKey: ['repos', repo.name, 'issues'],
      queryFn: async () => {
        const issues = await fetchIssues(repo.name)
        return { repo: repo.name, issues }
      },
    })) ?? [],
    combine: (issues) => {
      const totalIssues = issues
        .map(({ data }) => data?.issues.length ?? 0)
        .reduce((a, b) => a + b, 0)
	//combine返回useQueries 的最终结果
      return { issues, totalIssues }
    }
  })
}

export default function App() {
  const repos = useRepos()
  const { issues, totalIssues } = useIssues(repos.data)
  return(
    //...
  );
}
```

`combine` 是 `useQueries` 的一个选项，用于将所有查询的结果进行合并。`useQueries` 本身会执行多个查询，而 `combine` 负责将多个查询的结果进行处理，并返回一个合并后的结果。

无论你选择哪种方式，更重要的一点是，`useQueries` 允许你并行创建任意数量的查询，并且可以从所有查询的结果中提取你需要的任何值。