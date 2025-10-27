# Buforowanie i ponowna walidacja

Buforowanie to technika przechowywania wyników pobierania danych i innych obliczeń, dzięki czemu przyszłe żądania dotyczące tych samych danych mogą być obsługiwane szybciej, bez konieczności ponownego wykonywania tej samej pracy. Z kolei ponowna walidacja pozwala na aktualizację wpisów w pamięci podręcznej bez konieczności przebudowywania całej aplikacji.

1. fetch
2. unstable_cache
3. revalidatePath
4. revalidateTag
5. updateTag

## fetch

Domyślnie rządania fetch NIE są buforowane (choć sama trasa jest wstępnie renderowana). Można wymusić to za pomocą opcji force-cache:

```tsx
export default async function Page() {
  const data = await fetch('https://...', { cache: 'force-cache' })
}
```

Można włączyć walidację po konkretnym czasie, np.:

```tsx
// odśwież dane po godzinie
export default async function Page() {
  const data = await fetch('https://...', { next: { revalidate: 3600 } })
}
```


## usntable_cache

```tsx
// data
import { db } from '@/lib/db'
export async function getUserById(id: string) {
  return db
    .select()
    .from(users)
    .where(eq(users.id, id))
    .then((res) => res[0])
}


// page.tsx
import { unstable_cache } from 'next/cache'
import { getUserById } from '@/app/lib/data'
 
export default async function Page({
  params,
}: {
  params: Promise<{ userId: string }>
}) {
  const { userId } = await params
 
  const getCachedUser = unstable_cache(
    async () => {
      return getUserById(userId)
    },
    [userId], // add the user ID to the cache key
    revalidate: 3600, // okres do następnej walidacji
  )
}
```

## revalidatePath

Służy do ponownej walidacji trasy i śledzenia zdarzenia. Aby go użyć, należy go wywołać w programie obsługi trasy lub akcji serwera:


```tsx
import { revalidatePath } from 'next/cache'
 
export async function updateUser(id: string) {
  // Mutate data
  revalidatePath('/profile')
```



## Główne różnice pomiędzy revalidateTagi updateTag:

- updateTag: Tylko w akcjach serwera, natychmiast wygasa pamięć podręczna, w przypadku samodzielnego odczytu i zapisu
- revalidateTag:W akcjach serwera i obsłudze tras obsługuje funkcję stale-while-revalidate zprofile="max"