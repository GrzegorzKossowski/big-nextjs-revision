# Funkcja `cacheLive`

Funkcja `cacheLife` służy do ustawiania czasu życia pamięci podręcznej funkcji lub komponentu. Powinna być używana razem z `use cache` i w zakresie funkcji lub komponentu.

1. Włącz flagę w next.config.js

```ts
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  cacheComponents: true, // <================ TU
};

export default nextConfig;
```

2. Next proponuje defaultowe profile określające takie elementy jak:
   - `stale` - czas widoczności po stronie kliena
   - `revalidate` - odstęp generowania nowej zawartości po serwerze
   - `expire` - termin wygaśnięcia

- seconds - Dane w czasie rzeczywistym (ceny akcji, wyniki na żywo)
- minutes - Często aktualizowane (kanały społecznościowe, wiadomości)
- hours - Wiele codziennych aktualizacji (stan produktów, pogoda)
- days - Codzienne aktualizacje (posty na blogu, artykuły)
- weeks - Tygodniowe aktualizacje (podcasty, newslettery)
- max - Rzadkie zmiany (strony prawne, treść archiwalna)

```tsx
"use cache";
import { cacheLife } from "next/cache";

export default async function BlogPage() {
  cacheLife("days"); // Blog content updated daily

  const posts = await getBlogPosts();
  return <div>{/* render posts */}</div>;
}
```

UWAGA: `use cache` można umieścić na poziomie pliku lub na górze funkcji lub komponentu i `cacheLife` musi być wywołana w ich zakresie.

Profile pamięci podręcznej kontrolują zachowanie pamięci podręcznej za pomocą trzech właściwości czasowych:

- `stale`: Jak długo klient może korzystać z danych w pamięci podręcznej bez sprawdzania serwera

```tsx
cacheLife({ stale: 300 }); // 5 minutes
```

- `revalidate`: Po tym czasie kolejne żądanie spowoduje odświeżenie tła

```tsx
cacheLife({ revalidate: 900 }); // 15 minutes
```

- `expire`: Po tym czasie bez żadnych żądań, następny czeka na nową zawartość

```tsx
cacheLife({ expire: 3600 }); // 1 hour
```

## Standardowe profile

| Profil  | Przypadek użycia                               | stale     | revalidate | expire    |
| ------- | ---------------------------------------------- | --------- | ---------- | --------- |
| default | Standardowa treść                              | 5 minut   | 15 minut   | 1 rok     |
| seconds | Dane w czasie rzeczywistym                     | 30 sekund | 1 sekunda  | 1 minuta  |
| minutes | Często aktualizowana treść                     | 5 minut   | 1 minuta   | 1 godzina |
| hours   | Treść aktualizowana kilka razy dziennie        | 5 minut   | 1 godzina  | 1 dzień   |
| days    | Treść aktualizowana codziennie                 | 5 minut   | 1 dzień    | 1 tydzień |
| weeks   | Treść aktualizowana co tydzień                 | 5 minut   | 1 tydzień  | 30 dni    |
| max     | Stabilna zawartość, która rzadko ulega zmianom | 5 minut   | 30 dni     | 1 rok     |


## Niestandardowe profile

Zdefiniuj w next.config.ts profile pamięci podręcznej wielokrotnego użytku:

```tsx
import type { NextConfig } from 'next'
 
const nextConfig: NextConfig = {
  cacheComponents: true,
  cacheLife: {
    biweekly: {
      stale: 60 * 60 * 24 * 14, // 14 days
      revalidate: 60 * 60 * 24, // 1 day
      expire: 60 * 60 * 24 * 14, // 14 days
    },
  },
}
 
export default nextConfig
```

Powyższy przykład buforuje pamięć podręczną przez 14 dni, codziennie sprawdza dostępność aktualizacji i wygasa po 14 dniach. Możesz następnie odwołać się do tego profilu w całej aplikacji, używając jego nazwy:

```tsx
'use cache'
import { cacheLife } from 'next/cache'
 
export default async function Page() {
  cacheLife('biweekly')
  return <div>Page</div>
}
```

Uwaga: można nadpisać standardowe profile, używając ich nazw w configu.