# Struktura projektu

## foldery i pliki

### foldery najwyższego poziomu

katalogi porządkujące strukturę aplikacji

| Katalog | funkcja                     |
| ------- | --------------------------- |
| app     | katalog routingu            |
| api     | katalog api serwera         |
| public  | katalog assetów statycznych |
| src     | katalog organizacyjny       |

### pliki najwyższego poziomu

pliki konfiguracyjne aplikacji

| plik               | funkcja                                |
| ------------------ | -------------------------------------- |
| next.config.js     | Plik konfiguracyjny dla Next.js        |
| package.json       | Zależności i skrypty projektu          |
| instrumentation.ts | Plik OpenTelemetry i Instrumentation   |
| proxy.ts           | Serwer proxy żądania Next.js           |
| .env               | Zmienne środowiskowe                   |
| .env.local         | Lokalne zmienne środowiskowe           |
| .env.production    | Zmienne środowiska produkcyjnego       |
| .env.development   | Zmienne środowiska programistycznego   |
| .eslintrc.json     | Plik konfiguracyjny dla ESLint         |
| .gitignore         | Pliki i foldery Git do zignorowania    |
| next-env.d.ts      | Plik deklaracji TypeScript dla Next.js |
| tsconfig.json      | Plik konfiguracyjny dla TypeScript     |
| jsconfig.json      | Plik konfiguracyjny dla JavaScript     |

### pliki tras

pliki obsługujące segmenty url

| plik             | funkcja                               |
| ---------------- | ------------------------------------- |
| layout.tsx       | Układ, layout strony                  |
| page.tsx         | Strona                                |
| loading.tsx      | Ładowanie interfejsu użytkownika      |
| not-found.tsx    | Nie znaleziono interfejsu użytkownika |
| error.tsx        | Błąd interfejsu użytkownika           |
| global-error.tsx | Globalny interfejs użytkownika błędu  |
| routets          | Punkt końcowy API                     |
| template.tsx     | Ponownie renderowany układ            |
| default.tsx      | Strona zapasowa trasy równoległej     |

## zagnieżdżanie tras

struktura katalogowa pozwala budować trasy zagnieżdżone, np. `app/blog/authors/page.tsx`.

## trasy dynamiczne [slug], [...slut], [[...slug]]

| ścieżka                       | wzorzec url                                                    |
| ----------------------------- | -------------------------------------------------------------- |
| app/blog/[slug]/page.tsx      | /blog/my-first-post                                            |
| app/shop/[...slug]/page.tsx   | /shop/clothing, /shop/clothing/shirts                          |
| app/docs/[[...slug]]/page.tsx | /docs, /docs/layouts-and-pages, /docs/api-reference/use-router |

## grupowanie tras (folder)

Nawiasy okrągłe tworzą folder grupujący, pomijany w trasowaniu. Grupuje on subfoldery. Pozwalają na organizację według sekcji logicznych, np. marketingowe czy administracyjne, itp. 
UWAGA: w różnych grupach strony (`page.tsx`) nie mogą się nakładać! Nie dotyczy to layoutów! __MOŻNA grupować layouty na tym samym poziomie__. Uwaga, układy główne muszą też mieć znaczniki html (<body>, <html>), wtedy należy usunąć głwóny layout.

❌ - źle: `./app/(one)/page.tsx`

❌ - źle: `./app/(two)/page.tsx`

✅ - dobrze: `./app/(one)/layout.tsx`

✅ - dobrze: `./app/(one)/page.tsx`

✅ - dobrze: `./app/(two)/layout.tsx`

✅ - dobrze: `./app/(two)/`

lub page:

✅ - dobrze `./app/(one)/user/page.tsx`

✅ - dobrze `./app/(one)/data/page.tsx`

✅ - dobrze `./app/(two)/info/page.tsx`

## trasy równoległe i przechwycone

Stosowane do wyświetlania wielu tras w slotach na jednym layoucie lub do obsługi routingu modalnego.

| Wzór (dokumenty) | Oznaczający                           | Typowy przypadek użycia                   |
| ---------------- | ------------------------------------- | ----------------------------------------- |
| @folder          | Nazwany slot                          | Pasek boczny + treść główna               |
| (.)folder        | Przechwytywanie na tym samym poziomie | Podgląd trasy podrzędnej w oknie modalnym |
| (..)folder       | Przechwyć rodzica                     | Otwórz dziecko rodzica jako nakładkę      |
| (..)(..)folder   | Przecięcie dwóch poziomów             | Głęboko zagnieżdżona nakładka             |
| (...)folder      | Przechwytywanie z korzenia            | Pokaż dowolną trasę w bieżącym widoku     |

## Pliki metadanych

### ikony

| nazwa      | rozszerzenie              | znaczenie                          |
| ---------- | ------------------------- | ---------------------------------- |
| favicon    | .ico                      | Plik Favicon                       |
| icon       | .ico .jpg .jpeg .png .svg | Plik ikony aplikacji               |
| icon       | .js .ts .tsx              | Wygenerowana ikona aplikacji       |
| apple-icon | .jpg .jpeg,.png           | Plik ikony aplikacji Apple         |
| apple-icon | .js .ts .tsx              | Wygenerowana ikona aplikacji Apple |

### Obrazy Open Graph i Twittera

| nazwa           | rozszerzenie         | znaczenie                       |
| --------------- | -------------------- | ------------------------------- |
| opengraph-image | .jpg .jpeg .png .gif | Plik obrazu Open Graph          |
| opengraph-image | .js .ts .tsx         | Wygenerowany obraz Open Graph   |
| twitter-image   | .jpg .jpeg .png .gif | Plik obrazu Twittera            |
| twitter-image   | .js .ts .tsx         | Wygenerowany obraz na Twitterze |

### SEO

| nazwa   | rozszerzenie | znaczenie                 |
| ------- | ------------ | ------------------------- |
| sitemap | .xml         | Plik mapy witryny         |
| sitemap | .js .ts      | Wygenerowana mapa witryny |
| robots  | .txt         | Plik robotów              |
| robots  | .js .ts      | Wygenerowany plik robotów |

## struktura komponentów ws pliki

```tsx
// - layout.js
<Layout>
  // - template.js
  <Template>
    // - error.js(Granica błędu reakcji)
    <ErrorBoundary fallback={<Error />}>
      // - loading.js(Granica napięcia reakcji)
      <Suspense fallback={<Loading />}>
        // - not-found.js(Granica błędu React dla interfejsu użytkownika „nie
        znaleziono”)
        <ErrorBoundary fallback={<NotFound />}>
          // - page.jslub zagnieżdżonelayout.js
          <Page />
        </ErrorBoundary>
      </Suspense>
    </ErrorBoundary>
  </Template>
</Layout>
```

UWAGA: trasa nie jest dostępna, jeśli liściem nie będzie plik `page.tsx` lub `route.ts`. Wszystkie inne pliki mogą być umieszczane w katalogach routingu bez strachu o udostępnienie ich.

## foldery prywatne _folder

Nie są brane pod uwagę (stają się 'niewidoczne' dla routingu). Można w nich umieszczać dodatkowy kod.


## Strategie organizacji kodu

### pliki poza app

Ta strategia polega na przechowywaniu całego kodu aplikacji w folderach współdzielonych w katalogu głównym projektu i wykorzystywaniu katalogu `/app` wyłącznie do celów routingu.

### pliki w app

Ta strategia polega na przechowywaniu całego kodu aplikacji w folderach współdzielonych w katalogu głównym `/app`.

### podział wg. funkcji lub trasy

Strategia ta polega na przechowywaniu globalnie współdzielonego kodu aplikacji w katalogu głównym `/app` i rozdzielaniu bardziej szczegółowego kodu aplikacji na segmenty tras, które go używają.