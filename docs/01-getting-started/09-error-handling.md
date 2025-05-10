# Error handling

- expected errors
- uncautht exceptions

## Handling expected errors

Błędy oczekiwane, takie jak

- walidacja formularzy
- odrzucone requesty

Obłsugujemy je bezpośrednio w kodzie i wysyłamy do klienta informację.

### Server Functions

- nie używamy try/catch
- obsługujemy raczej w kodzie
- samodzielnie budujemy wiadomości błędu

```tsx
if (!res.ok) {
  return { message: "Failed to create post" };
}
```

- przesyłamy do klienta
- obsługujemy hookiem [useActionState()](https://react.dev/reference/react/useActionState)

```tsx
"use client";

import { useActionState } from "react";
import { createPost } from "@/app/actions";

const initialState = {
  message: "",
};

export function Form() {
  const [state, formAction, pending] = useActionState(createPost, initialState);

  return (
    <form action={formAction}>
      {/* ... */}
      {state?.message && <p aria-live="polite">{state.message}</p>}
      {/* ... */}
    </form>
  );
}
```

### Server Components

- w przypadku błędów na komponentach serwerowych możemy je obsłużyć od razu

```tsx
export default async function Page() {
  const res = await fetch(`https://...`);
  const data = await res.json();

  if (!res.ok) {
    return "There was an error.";
  }

  return "...";
}
```

- przekierować

```tsx
import { getPostBySlug } from "@/lib/posts";

export default async function Page({ params }: { params: { slug: string } }) {
  const { slug } = await params;
  const post = getPostBySlug(slug);

  if (!post) {
    redirect("/error-page");
  }

  return <div>{post.title}</div>;
}
```

### Not found

- wywołać `notFound()`

```tsx
import { getPostBySlug } from "@/lib/posts";

export default async function Page({ params }: { params: { slug: string } }) {
  const { slug } = await params;
  const post = getPostBySlug(slug);

  if (!post) {
    notFound();
  }

  return <div>{post.title}</div>;
}
```

## Handling uncaught exceptions

Błędy nieoczekiwane należy obsłużyć za pomocą Error Boundaries czyli `error.jsx/tsx` w danym katalogu ścieżki.

UWAGA: są to komponenty klienta!

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

### Nested error boundaries

### Global errors

Na poziomie roota jest `global-eroor.jsx/tsx`, który obsługuje całe drzewo.

UWAGA: plik ma własne `html` i `body`.

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