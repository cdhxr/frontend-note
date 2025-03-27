# Why we need react query

## effect获取数据的狼狈case
直接使用fetch in Effect得到数据：
 let me tell you that I immediately spotted 🐛 5 bugs 🪲 hiding in these 10 lines of code.

```jsx nums 
function Bookmarks({ category }) {
  const [data, setData] = useState([])
  const [error, setError] = useState()

  useEffect(() => {
    fetch(`${endpoint}/${category}`)
      .then(res => res.json())
      .then(d => setData(d))
      .catch(e => setError(e))
  }, [category])

  // Return JSX based on data and error state
}
```

1. [[移除不必要的Effect]] 提到的race condition，网络等因素可能导致the wrong order of data——这可以通过添加ignore flag解决

```jsx nums{6,10,11,12,15,16,17,19,20,21}
function Bookmarks({ category }) {
  const [data, setData] = useState([])
  const [error, setError] = useState()

  useEffect(() => {
    let ignore = false
    fetch(`${endpoint}/${category}`)
      .then(res => res.json())
      .then(d => {
        if (!ignore) {
          setData(d)
        }
      })
      .catch(e => {
        if (!ignore) {
          setError(e)
        }
      })
      return () => {
        ignore = true
      }
  }, [category])

  // Return JSX based on data and error state
}
```


2. loading state, it is not there at all. 在请求发生时，我们需要添加一个状态来展现加载UI，所以我们需要loading state，不妨加上。

```jsx {2,8,21,22,23,24,25}
function Bookmarks({ category }) {
  const [isLoading, setIsLoading] = useState(true)
  const [data, setData] = useState([])
  const [error, setError] = useState()

  useEffect(() => {
    let ignore = false
    setIsLoading(true)
    fetch(`${endpoint}/${category}`)
      .then(res => res.json())
      .then(d => {
        if (!ignore) {
          setData(d)
        }
      })
      .catch(e => {
        if (!ignore) {
          setError(e)
        }
      })
      .finally(() => {
        if (!ignore) {
          setIsLoading(false)
        }
      })
      return () => {
        ignore = true
      }
  }, [category])

  // Return JSX based on data and error state
}
```

3. 用空数组初始化数据似乎是个好主意可以避免类型检查

这样做的好处是：
- 访问 `data` 时不需要检查 `undefined`，可以直接使用 `.map()`、`.length` 等数组方法，避免额外的判断逻辑，例如 `if (data) { ... }`。
- 代码更简洁，避免 `null` 或 `undefined` 可能带来的错误。

但是，如果我们为一个还没有条目的类别获取数据，而实际返回的是一个空数组，该怎么办？它不是一个好方案，因为它将混淆以下两种情况：
- “数据还没有加载” (`undefined` 或 `null`)，表示请求还未发生或失败。 
- “数据已加载，但确实是空的” (`[]`)，表示该类别确实没有内容。

可以修改为：`const [data, setData] = useState()`

4. Data & Error are not reset when category changes，这会导致之前类别的数据被渲染到当前类别

```jsx {14,20}
function Bookmarks({ category }) {
  const [isLoading, setIsLoading] = useState(true)
  const [data, setData] = useState()
  const [error, setError] = useState()

  useEffect(() => {
    let ignore = false
    setIsLoading(true)
    fetch(`${endpoint}/${category}`)
      .then(res => res.json())
      .then(d => {
        if (!ignore) {
          setData(d)
          setError(undefined)
        }
      })
      .catch(e => {
        if (!ignore) {
          setError(e)
          setData(undefined)
        }
      })
      .finally(() => {
        if (!ignore) {
          setIsLoading(false)
        }
      })
      return () => {
        ignore = true
      }
  }, [category])

  // Return JSX based on data and error state
}
```

5. 严格模式下，会执行两次
6. 没有把它列入最初的错误列表，因为 React Query 也会遇到同样的问题：fetch 不会拒绝 HTTP 错误，所以你必须检查 res.ok，然后自己抛出一个错误。

```jsx {10,11,12,13,14}
function Bookmarks({ category }) {
  const [isLoading, setIsLoading] = useState(true)
  const [data, setData] = useState()
  const [error, setError] = useState()

  useEffect(() => {
    let ignore = false
    setIsLoading(true)
    fetch(`${endpoint}/${category}`)
      .then(res => {
        if (!res.ok) {
          throw new Error('Failed to fetch')
        }
        return res.json()
      })
      .then(d => {
        if (!ignore) {
          setData(d)
          setError(undefined)
        }
      })
      .catch(e => {
        if (!ignore) {
          setError(e)
          setData(undefined)
        }
      })
      .finally(() => {
        if (!ignore) {
          setIsLoading(false)
        }
      })
      return () => {
        ignore = true
      }
  }, [category])

  // Return JSX based on data and error state
}
```

## react query

Data Fetching is simple.  
Async State Management is not.

And this is where React Query comes in, 
because React Query is NOT a data fetching library - it's an async state manager.

即便在react query中，我们依然需要写一样的fetch代码，我们不需要react query来做fetch data这样简单的事，但是需要它使状态变得可控

采用react query时，可以避免上面一大串复杂的代码，并规避这些BUG，只需要11行

```jsx nums
function Bookmarks({ category }) {
  const { isLoading, data, error } = useQuery({
    queryKey: ['bookmarks', category],
    queryFn: () =>
      fetch(`${endpoint}/${category}`).then((res) => {
        if (!res.ok) {
          throw new Error('Failed to fetch')
        }
        return res.json()
      }),
  })

  // Return JSX based on data and error state
}
```

