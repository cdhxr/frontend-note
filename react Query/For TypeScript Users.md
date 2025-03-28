
# fetch:

Make sure your `queryFn` always has a return type specified. Otherwise, `data` will default to `any`.

`async function fetchRepos(): Promise<Array<RepoData>>`

或者可以使用类型断言

`return response.json() as Array<RepoData>`

Please **do not** provide 泛型 to the useQuery call-side.