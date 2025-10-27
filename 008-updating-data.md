# Aktualizacja danych

Funkcje serwerowe pozwalają na aktualizację danych od strony klienta. Funkcja serwera to funkcja asynchroniczna działająca na serwerze. Można ją wywołać z poziomu klienta poprzez żądanie sieciowe, dlatego musi działać asynchronicznie.

⚠️ UWAGA: Nie można definiować funkcji serwera w komponentach klienta. Można je jednak wywołać w komponentach klienta, importując je z pliku, który zawiera dyrektywę "use server" na początku.

```tsx
'use server'
export async function createPost() {}

// import z zewnętrznego pliku
'use client'
import { createPost } from '@/app/actions'
export function Button() {
  return <button formAction={createPost}>Create</button>
}


// przekazanie jako props
'use client'
export default function ClientComponent({
  createPost,
}: {
  createPost: (formData: FormData) => void
}) {
  return <form action={createPost}>{/* ... */}</form>
}

// Nastętpnie:
<ClientComponent createPost={createPost} />
```

## wywoływanie funkcji serwerowych

1. w formularzach
2. w zdarzeniach oraz useEffect

⚠️ UWAGA: funkcje serwera służą do mutacji, ale klient wysyła je pojedynczo (aktualnie).


React pozwala użyć funkcji serwerowej w formularzu za pomocą action:

```tsx
// action
'use server'
export async function createPost(formData: FormData) {
  const title = formData.get('title')
  const content = formData.get('content')
  // Update data
  // Revalidate cache
}

// Następnie klient
import { createPost } from '@/app/actions'
export function Form() {
  return (
    <form action={createPost}>
      <input type="text" name="title" />
      <input type="text" name="content" />
      <button type="submit">Create</button>
    </form>
  )
}
```

Użycie w zdarzeniach:

```tsx
'use client'
 
import { incrementLike } from './actions'
import { useState } from 'react'
 
export default function LikeButton({ initialLikes }: { initialLikes: number }) {
  const [likes, setLikes] = useState(initialLikes)
 
  return (
    <>
      <p>Total Likes: {likes}</p>
      <button
        onClick={async () => {
          const updatedLikes = await incrementLike()
          setLikes(updatedLikes)
        }}
      >
        Like
      </button>
    </>
  )
}
```


## Przykłady

Wyświetlanie stanu oczekującego:

```tsx
'use client'
 
import { useActionState, startTransition } from 'react'
import { createPost } from '@/app/actions'
import { LoadingSpinner } from '@/app/ui/loading-spinner'
 
export function Button() {
  const [state, action, pending] = useActionState(createPost, false)
 
  return (
    <button onClick={() => startTransition(action)}>
      {pending ? <LoadingSpinner /> : 'Create Post'}
    </button>
  )
}
```

