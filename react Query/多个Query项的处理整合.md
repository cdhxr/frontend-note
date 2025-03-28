
```js
function useBookDetails(bookId) {
  return useQueries({
    queries: [
      {
        queryKey: ["book", { bookId }],
        queryFn: () => getBook(bookId),
      },
      {
        queryKey: ["reviews", { bookId }],
        queryFn: () => getReviewsForBook(bookId),
      },
    ],
    combine: (queries) => {
      // you can combine data from your queries here and return a single object.
      const isPending = queries.some((query) => query.status === "pending");
      const isError = queries.some((query) => query.status === "error");
      const [book, reviews] = queries.map((query) => query.data);
      
      return {
        isPending,
        isError,
        book,
        reviews
      };
    }
  })
}

```