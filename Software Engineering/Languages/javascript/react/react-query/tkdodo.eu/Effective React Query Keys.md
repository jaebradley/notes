# [Effective React Query Keys](https://tkdodo.eu/blog/effective-react-query-keys)
* Internally, the query cache is just a JS object
 * Keys are the serialized query keys
 * Values are the query data + metadata
* Keys need to be unique for queries - if React Query finds an entry for a key in the cache, it will use it
* Can't use same key for `useQuery` and `useInfiniteQuery`

## Automatic Refetching
* Most people think about queries and refetching in an imperative way
  * I have a query -> it fetches some data -> I click this button -> I refetch data with different parameters

```javascript
// Example of imperative refetch
function Component() {
  const { data, refetch } = useQuery({
    queryKey: ['todos'],
    queryFn: fetchTodos,

  })

  // ❓ how do I pass parameters to refetch ❓
  return <Filters onApply={() => refetch(???)} />
}
```

* `refetch` is for refetching with the same parameters
* If there is state that changes data, put the state in the query key
  * React Query will trigger a refetch automatically when the key changes
  * So when you want to apply fitlers, change your client state


## Colocate
* Author keeps query keys next to their respective queries

```
- src
  - features
    - Profile
      - index.tsx
      - queries.ts
    - Todos
      - index.tsx
      - queries.ts
```

## Structure
* Structure query keys from most generic to most specific levels of granularity

```
['todos', 'list', { filters: 'all' }]
['todos', 'list', { filters: 'done' }]
['todos', 'detail', 1]
['todos', 'detail', 2]
```

* Can invalidate everything related to todos by invalidating the `todos` key
* Can also target one specific detail, or all specific details

## Use Query Key factories
```javascript
const todoKeys = {
  all: ['todos'] as const,
  lists: () => [...todoKeys.all, 'list'] as const,
  list: (filters: string) => [...todoKeys.lists(), { filters }] as const,
  details: () => [...todoKeys.all, 'detail'] as const,
  detail: (id: number) => [...todoKeys.details(), id] as const,
}
// 🕺 remove everything related
// to the todos feature
queryClient.removeQueries({
  queryKey: todoKeys.all
})

// 🚀 invalidate all the lists
queryClient.invalidateQueries({
  queryKey: todoKeys.lists()
})

// 🙌 prefetch a single todo
queryClient.prefetchQueries({
  queryKey: todoKeys.detail(id),
  queryFn: () => fetchTodo(id),
})
```
