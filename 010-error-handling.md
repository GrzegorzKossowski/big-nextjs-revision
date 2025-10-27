# Obsługa błędów

## błędy oczekiwane

Błędy oczekiwane to błędy, które mogą wystąpić podczas normalnego działania aplikacji, takie jak błędy walidacji formularza po stronie serwera lub nieudane żądania. Błędy te powinny być obsłużone jawnie i zwrócone klientowi.

⚠️ UWAGA: należy unikać try/catch i zwracać wartości (obiekty błędów itp.)

```tsx
'use server'
 
export async function createPost(prevState: any, formData: FormData) {
  const title = formData.get('title')
  const content = formData.get('content')
 
  const res = await fetch('https://api.vercel.app/posts', {
    method: 'POST',
    body: { title, content },
  })
  const json = await res.json()
 
  if (!res.ok) {
    // zwróć obiekt, nie rzucaj błedu!
    return { message: 'Failed to create post' }
  }
}
```

Następnie - użyć hooka, aby wyświetlić komunikat u klienta:

```tsx
'use client'
import { useActionState } from 'react'
import { createPost } from '@/app/actions'
 
const initialState = {
  message: '',
}
 
export function Form() {
  const [state, formAction, pending] = useActionState(createPost, initialState)
  return (
    <form action={formAction}>
        [...]
]       {state?.message && <p aria-live="polite">{state.message}</p>}
    </form>
  )
}
```

W przypadku komponentów serwerowych zwracamy także dane:

```tsx
export default async function Page() {
  const res = await fetch(`https://...`)
  const data = await res.json()
 
  if (!res.ok) {
    // ❗ zwracamy dane, nie rzucamy erroru
    return 'There was an error.'
  }
 
  return '...'
}
```

Można użyć strony notFound:

```tsx
import { getPostBySlug } from '@/lib/posts'
 
export default async function Page({ params }: { params: { slug: string } }) {
  const { slug } = await params
  const post = getPostBySlug(slug)
 
  if (!post) {
    // ❗ przekierowujemy na not-found.tsx
    notFound()
  }
 
  return <div>{post.title}</div>
}
```

## wyjątki

Niewyłapane wyjątki to nieoczekiwane błędy, które wskazują na usterki lub problemy, które nie powinny występować podczas normalnego działania aplikacji. Należy je obsłużyć poprzez zgłaszanie błędów, które następnie zostaną wyłapane przez granice błędów.

Utwórz granicę błędu, dodając error.jsx/tsx plik wewnątrz segmentu trasy i eksportując komponent React:

```tsx
'use client' // Error boundaries must be Client Components
 
import { useEffect } from 'react'
 
export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string }
  reset: () => void
}) {
  useEffect(() => {
    // Log the error to an error reporting service
    console.error(error)
  }, [error])
 
  return (
    <div>
      <h2>Something went wrong!</h2>
      <button
        onClick={
          // Attempt to recover by trying to re-render the segment
          () => reset()
        }
      >
        Try again
      </button>
    </div>
  )
}
```

## Błędy globalne

Choć zdarza się to rzadziej, błędy w układzie głównym można obsłużyć za pomocą global-error.js, pliku znajdującego się w katalogu głównym aplikacji, nawet w przypadku korzystania z internacjonalizacji . Globalny interfejs użytkownika błędów musi definiować własne tagi <html>i <body>, ponieważ zastępuje układ główny lub szablon, gdy jest aktywny.

```tsx
'use client' // Error boundaries must be Client Components
 
export default function GlobalError({
  error,
  reset,
}: {
  error: Error & { digest?: string }
  reset: () => void
}) {
  return (
    // global-error must include html and body tags
    <html>
      <body>
        <h2>Something went wrong!</h2>
        <button onClick={() => reset()}>Try again</button>
      </body>
    </html>
  )
}
```