# Update data

## Creating Server Functions

- funkcja serwerowa może być oznaczona jako `use server` na początku każdej funkcji asynchronicznej

```tsx
export async function createPost(formData: FormData) {
  "use server";
  const title = formData.get("title");
  const content = formData.get("content");

  // Update data
  // Revalidate cache
}
```

### Server Components

### Client Components

- nie można oznaczać funkcji serwerowych w komponentach klienta, ale można je importować.

```tsx
"use server";
export async function createPost() {}

// client component
("use client");
import { createPost } from "@/app/actions";

export function Button() {
  return <button formAction={createPost}>Create</button>;
}
```

## Invoking Server Functions

### Forms

- funkcje serwerowe (np. do pobierania danych) można używać w foms(ach) - jako server actions

```tsx
"use server";

export async function createPost(formData: FormData) {
  const title = formData.get("title");
  const content = formData.get("content");

  // Update data
  // Revalidate cache
}

// client component
import { createPost } from "@/app/actions";

export function Form() {
  return (
    <form action={createPost}>
      <input type="text" name="title" />
      <input type="text" name="content" />
      <button type="submit">Create</button>
    </form>
  );
}
```

### Event Handlers

- funkcje serwerowe można używać jako event-handlery (np. onClick)

```tsx
"use client";

import { incrementLike } from "./actions";
import { useState } from "react";

export default function LikeButton({ initialLikes }: { initialLikes: number }) {
  const [likes, setLikes] = useState(initialLikes);

  return (
    <>
      <p>Total Likes: {likes}</p>
      <button
        onClick={async () => {
          {
            /* użyj funkcji serwerowej do obsługi lajka */
          }
          const updatedLikes = await incrementLike();
          setLikes(updatedLikes);
        }}
      >
        Like
      </button>
    </>
  );
}
```

## Examples
### Showing a pending state

- można użyć `useActionState` do obsługi pending

```tsx
'use client'
 
import { useActionState } from 'react'
import { createPost } from '@/app/actions'
import { LoadingSpinner } from '@/app/ui/loading-spinner'
 
export function Button() {
  const [state, action, pending] = useActionState(createPost, false)
 
  return (
    <button onClick={async () => action()}>
      {pending ? <LoadingSpinner /> : 'Create Post'}
    </button>
  )
}
```

### Revalidating the cache

[docs](https://nextjs.org/docs/app/getting-started/updating-data#revalidating-the-cache)

### Redirecting

- W serwer functions można przekierowywać na inne routy za pomocą `redirect`.

```tsx
'use server'
 
import { redirect } from 'next/navigation'
 
export async function createPost(formData: FormData) {
  // Update data
  // ...
 
  redirect('/posts')
}
```

