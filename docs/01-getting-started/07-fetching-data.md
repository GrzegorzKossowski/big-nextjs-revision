# Ładowanie danych

## Fetching data

### Server Components

Dane po stronie serwera można ładować

- za pomocą `fetch`
- za pomocą `orm` lub bezpośrednio bazy danych

**With the fetch API**

Ładowanie `fetch` wymaga zastosowania asynchronicznej funkcji.

```tsx
// użyj async
export default async function Page() {
  // użyj await
  const data = await fetch("https://api.vercel.app/blog");
  const posts = await data.json();
  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
}
```

**With an ORM or database**

- komponenty serwerowe pozwalają na bezpośrednie odpytywanie bazy, ponieważ nie są wysyłane do klienta (są niewidoczne)
- komponenty są asynchroniczne

```tsx
import { db, posts } from "@/lib/db";

export default async function Page() {
  const allPosts = await db.select().from(posts);
  return (
    <ul>
      {allPosts.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
}
```

### Client Components

**With the use hook**

- klient może użyć _nowego_ (use hook)[https://react.dev/reference/react/use]

```tsx
// pobierz na serwerze (bez Promisa)
import Posts from '@/app/ui/posts'
import { Suspense } from "react"

export default function Page() {
  // NO await the data fetching function
  const posts = getPosts()

// uwaga, wymaga <Suspense> na czas ladowania danych
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Posts posts={posts} />
    </Suspense>
  )
}
```

```tsx
// wyświetl na kliencie
'use client'
import { use } from 'react'

export default function Posts({
  posts,
}: {
  posts: Promise<{ id: string; title: string }[]>
}) {
  const allPosts = use(posts)

  return (
    <ul>
      {allPosts.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  )
}
```

**(use hook)[https://react.dev/reference/react/use]**

### Use hook

`const value = use(resource);`

pozwala na czytanie promisów.

**use(resource)**

- Pozwala na czytanie promisów lub contextu
- można używać w pętlach i kodzie
- można w hookach lub komponentach
- element jest 'pending', więc warto używać Suspense
- jeśli Promis jest odrzucony, Error Boundary jest uruchamiane

```jsx
import { use } from 'react';

function MessageComponent({ messagePromise }) {
  const message = use(messagePromise);
  const theme = use(ThemeContext);
  // ...
```

- uwaga, preferuj async/await po stronie serwera (stabilne, nie rerenderuje)
- use rerenderuje komponent po rozwiązaniu promisa

**Usage**

- Reading context with use

- Streaming data from the server to 
the client
- Dealing with rejected Promises

- Troubleshooting

- “Suspense Exception: This is not a 
real error!”


**Community libraries**

Można zastosować biblioteki społeczności, np. Tanstack Query

## Streaming

[streaming](https://nextjs.org/docs/app/getting-started/fetching-data#streaming)

Streaming wymaga ustawienia opcji `dynamicIO` (v15)

### With loading.js

### With <Suspense>

### Creating meaningful loading states

## API Reference
