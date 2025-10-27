# Pobieranie danych

## fetch

Pobieranie danych wymaga przerobienia strony na asynchroniczną (jest to możliwe dla serwera)

Odpowiedzi fetch nie są buforowane, choć next wstępnie renderuje trasę.

```tsx
export default async function Page() {
  const data = await fetch('https://api.vercel.app/blog')
  const posts = await data.json()
  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  )
}
```


## ORM

Zapytania do bazy z komponentów serwerowch są bezpieczne (nie są udostępniane klientowi, lecz renderowane na serwerze). Można wręcz używać ich bezpośrenio.

```tsx
import { db, posts } from '@/lib/db'
 
export default async function Page() {
  const allPosts = await db.select().from(posts)
  return (
    <ul>
      {allPosts.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  )
}
```

## komponenty klienckie

1. hooki reacta (przesyłanie strumieniowe)
2. biblioteki jak SWR lub ReactQuery


```tsx
// komponent na serwerze + przekazanie Promisa do komponentu klienckiego
import { db, posts } from '@/lib/db'
 
export default async function Page() {
  const allPosts = await db.select().from(posts)
  return (
    <ul>
      {allPosts.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  )
}

// komponent klienta + hook use()
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

## cacheowanie przychodzących danych

Zapamiętywanie danych z tego samego url - automatyczne łączenie rządań. W przypadku ORM można użyć [cache (react)](https://react.dev/reference/react/cache).

```tsx
import { cache } from 'react'
import { db, posts, eq } from '@/lib/db'
 
export const getPost = cache(async (id: string) => {
  const post = await db.query.posts.findFirst({
    where: eq(posts.id, parseInt(id)),
  })
})
```

## Strumieniowanie danych

⚠️ Wymaga włączenia w konfiguracji opcji [cacheComponents](https://nextjs.org/docs/app/api-reference/config/next-config-js/cacheComponents).

1. użycie strony loading.tsx w katalogu danej strony page.tsx
2. użycie komponentu `<Suspense>`.

`<Suspense>` pozwala na szczegółowe określenie, który fragment strony ma być strumieniowany.

```tsx
import { Suspense } from 'react'
import BlogList from '@/components/BlogList'
import BlogListSkeleton from '@/components/BlogListSkeleton'
 
export default function BlogPage() {
  return (
    <div>
      {/* This content will be sent to the client immediately */}
      <header>
        <h1>Welcome to the Blog</h1>
        <p>Read the latest posts below.</p>
      </header>
      <main>
        {/* Any content wrapped in a <Suspense> boundary will be streamed */}
        <Suspense fallback={<BlogListSkeleton />}>
          <BlogList />
        </Suspense>
      </main>
    </div>
  )
}
```

## Sekwencyjne pobieranie danych

Zdarza się, że jedne dane zależą od drugich. Należy wtedy używać `<Suspense>` celem zwiększenia komfortu użytkownika na czas pobierania danych.

```tsx
export default async function Page({
  params,
}: {
  params: Promise<{ username: string }>
}) {
  const { username } = await params
  // Get artist information
  const artist = await getArtist(username)
 
  return (
    <>
      <h1>{artist.name}</h1>
      {/* Show fallback UI while the Playlists component is loading */}
      <Suspense fallback={<div>Loading...</div>}>
        {/* Pass the artist ID to the Playlists component */}
        <Playlists artistID={artist.id} />
      </Suspense>
    </>
  )
}
```

## Równoległe pobieranie danych

Można używać Promise.all lub Promise.allSettled celem równoległego pobierania danych:

