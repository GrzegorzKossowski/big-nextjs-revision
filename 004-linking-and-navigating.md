# Łączenie i nawigacja

W Next.js trasy są domyślnie renderowane na serwerze. Często oznacza to, że klient musi czekać na odpowiedź serwera, zanim wyświetli nową trasę. Next.js oferuje wbudowane funkcje wstępnego pobierania , strumieniowania i przejść po stronie klienta, zapewniając szybką i responsywną nawigację.

## Renderowanie po stronie serwera

W next layouty są komponentami serwerowymi. Są generowane przed wysłaniem do klienta.

- renderowanie statyczne - podczas kompilacji aplikacji
- renderowanie dynamiczne - na żądanie klienta (np. z nowymi danymi)

Aby uniknąć czekania, next pobiera trasy, które klient prawdopodobnie wyświetli (keszowanie) i nawiguje lokalnie u klienta.

Pobieranie tras następuje w momencie, kiedy `<Link>` pojawi się w obszarze widoku przeglądarki.

```tsx
import Link from "next/link";

export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <html>
      <body>
        <nav>
          {/* Prefetched when the link is hovered or enters the viewport */}
          <Link href="/blog">Blog</Link>
          {/* No prefetching */}
          <a href="/contact">Contact</a>
        </nav>
        {children}
      </body>
    </html>
  );
}
```

UWAGA: trasy statyczne są pobierane całe. Trasy dynamiczne - częściowo (layout.tsx) lub wcale. Możliwe jest przesyłanie strumieniowe.

## Przesyłanie strumieniowe

Przesyłanie strumieniowe pozwala serwerowi wysyłać fragmenty dynamicznej trasy do klienta, gdy tylko będą gotowe, zamiast czekać na wyrenderowanie całej trasy. Oznacza to, że użytkownicy widzą coś szybciej, nawet jeśli fragmenty strony wciąż się ładują.

W tym celu wykorzystuje w tle `<Suspense>` (przez dodanie loading.tsx). Pobrane układy pokazują się wcześniej, są aktywne.

```tsx
// app/some/loading.tsx
export default function Loading() {
  // Add fallback UI that will be shown while the route is loading.
  return <LoadingSkeleton />;
}
```

## Nawigacja u klienta

Tradycyjnie, przejście do strony renderowanej przez serwer powoduje pełne załadowanie strony. To wyczyści stan, zresetuje pozycję przewijania i zablokuje interaktywność.

Next.js pozwala uniknąć tego problemu dzięki przejściom po stronie klienta z wykorzystaniem komponentu `<Link>`. Zamiast przeładowywać stronę, aktualizuje zawartość dynamicznie poprzez:

- Zachowując wszelkie współdzielone układy i interfejsy użytkownika.
- Zastąpienie bieżącej strony wstępnie pobranym stanem ładowania lub nową stroną, jeśli jest dostępna.

## Troubleshooting

- brak `loading.tsx` może spowolnić wrażenie przeładowania
- brak `generateStaticParams()`. Użycie tej funkcji pozwala prerenderować częściowo trasy dynamiczne.
- powolne sieci. Klient kliknie link, zanim ten zdąży się pobrać.

## Ograniczenie wstępnego ładowania

W przypadku nieskończonych list z linkami, należy wyłączyć w nich prefetching (oszczędność zasobów):

```tsx
// infinite list
<Link prefetch={false} href="/blog">
  Blog
</Link>
```

- trasy statyczne nie zostaną wstępnie pobrane
- trasy dynamiczne będą renderowane w locie (czas!)

Można robić prefeach na hover:

```tsx
"use client";

import Link from "next/link";
import { useState } from "react";

function HoverPrefetchLink({
  href,
  children,
}: {
  href: string;
  children: React.ReactNode;
}) {
  const [active, setActive] = useState(false);

  return (
    <Link
      href={href}
      prefetch={active ? null : false}
      onMouseEnter={() => setActive(true)}
    >
      {children}
    </Link>
  );
}
```
