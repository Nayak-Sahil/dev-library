# React Query (TanStack Query)

A concise guide to using React Query (rebranded to TanStack Query). This file preserves the original content but converts Logseq-style blocks and video embeds into standard, readable Markdown.

## Index
- Quick start
- How to use
- Devtools
- Caching behavior
- isLoading vs isFetching
- Stale Time
- Polling
- Fetching data with onClick
- Mutations (useMutation)
- References

---

## Why this we need?

<iframe width="360" height="203" src="https://www.youtube.com/embed/bYgR8odPEVg" title="React Query overview video" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Video: [Watch on YouTube](https://youtu.be/bYgR8odPEVg?si=KhVOTYEze78gRnBk)

### Originally
The library was called **React Query**; it was later rebranded as **TanStack Query**.

Documentation: https://tanstack.com/query/latest/docs/framework/react/installation

## Single Tutorial
<iframe width="360" height="203" src="https://www.youtube.com/embed/GEeBG7AoQdM" title="React Query tutorial" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Video: [Watch on YouTube](https://www.youtube.com/watch?v=GEeBG7AoQdM)

## Quickly Getting Started
Install:

```bash
npm i @tanstack/react-query
```

Import two things in your main entry (e.g., `main.tsx`):

```tsx
import {
  QueryClient,
  QueryClientProvider,
} from '@tanstack/react-query'

const queryClient = new QueryClient()
function App() {
  return (
    // Provide the client to your App
    <QueryClientProvider client={queryClient}>
      <App />
    </QueryClientProvider>
  )
}
```

## How to use
Refer to this article for understanding `useQuery` and `useMutation`:
- https://medium.com/bina-nusantara-it-division/understanding-react-query-11e56960e90c

See: we still use `fetch` or `axios` together with React Query.

```javascript
async function fetchPosts(){
    return await axios.get('https://jsonplaceholder.typicode.com/posts')    
}
```

```jsx
function Posts(){
  const { data, error, isError, isLoading } = useQuery({
    queryKey: ['posts'],
    queryFn: fetchPosts,
  })

  // first argument is a string to cache and track the query result
  if(isLoading){
      return <div>Loading...</div>
  }
  if(isError){
      return <div>Error: {error.message}</div>
  }
  return(
      <div className='container'>
      <h1>Posts</h1>
      {
          data.map((post, index) => {
              return <li key={index}>{post.title}</li>
          })
      }
      </div>
  )
}
export default Posts
```

The minimum required params to `useQuery()`:

```javascript
// Example options object for useQuery
const options = { queryKey: ['posts'], queryFn: getTodos };
```

`queryKey`: path name of endpoint

Examples:
- `/posts` => `['posts']`
- `/posts/1` => `['posts', 1]` or `['posts', 'post_number']`
- `/posts/1/comments` => `['posts', 1, 'comments']`

`queryFn`: the function that internally uses `fetch` or `axios`

## Devtools to visualize data in React Query
How to setup: https://tanstack.com/query/latest/docs/framework/react/devtools#options

```bash
npm i @tanstack/react-query-devtools
```

Use it simply:

```jsx
import { ReactQueryDevtools } from '@tanstack/react-query-devtools'

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      {/* The rest of your application */}
      <ReactQueryDevtools initialIsOpen={false} />
    </QueryClientProvider>
  )
}
```

### Default Caching Behavior
By default, every query result in TanStack Query is **cached for 5 minutes**.

- First Fetch: When a `useQuery` hook is initially fired for a specific query key, `isLoading` is set to `true`, and a network request is sent.
- Caching Upon Completion: Once the request completes, the fetched data is cached using the `queryKey` and the `queryFn` as identifiers.
- Subsequent Access (from Cache): If you navigate away and then revisit a page that uses the same query, TanStack Query will first check its cache. If the data exists in the cache, it is returned immediately (without `isLoading` being `true`).
- Background Refetching: Even though cached data is returned instantly, TanStack Query triggers a background refetch. If the background fetch succeeds, the UI is updated with the new data.

## isLoading vs isFetching
`isLoading` indicates the initial loading state. `isFetching` indicates a background refetch in progress.

When a page is first loaded, both `isLoading` and `isFetching` are `true`. After the initial fetch they become `false`. When navigating back to a page with cached data, `isLoading` stays `false` but `isFetching` may briefly become `true` during background refetch.

## Stale Time
By default, React Query considers data "stale" immediately (`staleTime: 0`), leading to a background refetch on every re-mount or window focus.

You can set `staleTime` (milliseconds) in the `useQuery` options to keep data fresh for that duration. Example: `staleTime: 30000` (30 seconds).

## Polling
Polling enables repeated network calls at a fixed interval via `refetchInterval` (milliseconds). By default `refetchInterval` is `false`.

To keep polling even when the tab is unfocused, set `refetchIntervalInBackground: true`.

## Fetching Data with onClick
By default, a `useQuery` runs on mount. To fetch on demand (e.g., onClick):
1. Set `enabled: false` in `useQuery` options so it doesn't run on mount.
2. Use the returned `refetch` method to trigger the query manually (e.g., attach to a button `onClick`).

## Mutations (`useMutation`)
`useQuery` is primarily for fetching and caching server state (read-only). `useMutation` is for mutating server-side data (create/update/delete).

```javascript
import { useMutation } from '@tanstack/react-query';

const {
  mutate,
  isPending,
  isError,
  error
} = useMutation({
  mutationFn: yourMutationFunction,
});
```

`useMutation` returns `mutate` (callback-based) and `mutateAsync` (promise-based).

- `mutate`: fire-and-forget, use callbacks (`onSuccess`, `onError`).
- `mutateAsync`: returns a promise; use with `async/await` and `try/catch`.

Example using `mutate`:

```javascript
const mutation = useMutation({
  mutationFn: submitData,
  onSuccess: () => { console.log('Success!'); },
  onError: (error) => { console.error('Error:', error); },
});

mutation.mutate(data);
```

Example using `mutateAsync`:

```javascript
const mutation = useMutation({ mutationFn: submitData });

const handleSubmit = async () => {
  try {
    const result = await mutation.mutateAsync(data);
    console.log('Result:', result);
  } catch (error) {
    console.error('Error:', error);
  }
};
```

### Usage of callbacks
- `onSuccess`: runs after a successful `mutationFn`.
  - Common use: invalidate related queries to refetch fresh data.