# Funkcja `cacheTag`

Funkcja `cacheTag` umożliwia oznaczanie danych w pamięci podręcznej w celu ich unieważnienia na żądanie. Dzięki powiązaniu tagów z wpisami w pamięci podręcznej można selektywnie usuwać lub ponownie zatwierdzać określone wpisy bez wpływu na inne dane w pamięci podręcznej.

Wymaga ustawienia flagi w next.config.js

```ts
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  cacheComponents: true,
};

export default nextConfig;
```

1. Oznacz plik, komponent lub funkcję (dane) za pomocą tagu:

```tsx
import { cacheTag } from "next/cache";

export async function getData() {
  "use cache";
  cacheTag("my-data"); // <-------------- TU
  // v--------- uwaga, można przypisać wiele (128 szt.) tagów na raz do tego samego zestawu danych
  // cacheTag('tag-one', 'tag-two')
  const data = await fetch("/api/data");
  return data;
}
```

2. wyczyść pamięć na żądanie z określonych danych w innym miejscu (trasie lub akcji serwera)

```tsx
"use server";

import { revalidateTag } from "next/cache";

export default async function submit() {
  await addPost();
  revalidateTag("my-data"); // <----------------- TU
}
```

UWAGA: do tagowania można użyć pobranych danych!

```tsx
async function getBookingsData() {
  "use cache";
  const data = await fetch(`/api/bookings?type=${encodeURIComponent(type)}`);
  cacheTag("bookings-data", data.id); // <------ TU, tag z ID dla całej funkcji
  return data;
}
```
