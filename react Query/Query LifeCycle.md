
# 通过`status`确定数据状态

Let's say, using the browser's [MediaDevices API](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/enumerateDevices) API, we wanted to show the user a list of their currently available

```js
function MediaDevices() {
  const { data } = useQuery({
    queryKey: ['mediaDevices'],
    queryFn: () => {
      return navigator.mediaDevices.enumerateDevices()
    }
  })

  return (
    <ul>
      {data.map((device) => (
        <li key={device.deviceId}>{device.label}</li>
      ))}
    </ul>
  )
}
```

Because `enumerateDevices` returns a `Promise`, we can return that from our `queryFn` and React Query will handle the rest for us.

Yes, React Query allows us to treat asynchronous code as if it were synchronous, but it doesn't change the fact that it's still asynchronous. 我们将有一段时间无法使用我们的数据

If we were to run this code, we'd get this error.

`Cannot read properties of undefined (reading 'map')`

We're trying to access `map` on `data`, but until the async request has resolved, `data` is going to be `undefined`.

# `status`的状态与promise的状态对应

Of course, we could always use a conditional to check if `data` is `undefined` before accessing `map`, but React Query comes with an even better option – exposing its internal _Query States_ to let you know which `status` the query is currently in:

- `pending` - the Query has not yet completed, so you don't have `data` yet.
- `success` - the Query has finished successfully, and `data` is available.
- `error` - the Query has failed, and you have an `error`.

Those statuses directly correspond to the state a `Promise` can be in - `pending`, `fulfilled` or `rejected`, which hopefully makes sense as React Query is entirely Promise based.

# 访问`status`的方法

You have two options for accessing the `status` of the query.

The first, 通过属性, is via the `status` property that `useQuery` returns.

```js
function MediaDevices() {
  const { data, status } = useQuery({
	//...
  })

  if (status === 'pending') {
    return <div>...</div>
  }

  if (status === 'error') {
    return <div>We were unable to access your media devices</div>
  }

  return (
    <ul>
      {data.map((device) => (
        <li key={device.deviceId}>{device.label}</li>
      ))}
    </ul>
  )
}
```

The second, 用布尔函数标记status is via the derived boolean flags, `isPending`, `isSuccess` and `isError` that are also available on the object returned by `useQuery`.

```js
function MediaDevices() {
  const { data, status } = useQuery({
	//...
  })
  
  if (isPending === true) {
    return <div>...</div>
  }

  if (isError === true) {
    return <div>We were unable to access your media devices</div>
  }

  return (
     //...
  )
}
```