# Project structure

## Foldery

- app, pages - główne katalogi aplikacji
- public - ogólnodostępne assety, w kodzie jako root `/`

## pliki głównego poziomu

- `next.config.js` - plik konfiguracyjny
- `middleware.ts` - plik przechwytujący ruch serwer-klient


## pliki routingu

- `layout` - plik layoutu, odpowiada za strukturę strony, przechowuje dane, nie rerenderuje się
- `page` - główny plik strony, defaultowa nazwa, obsługuje zawartość strony
- `loading` - Suspens do pojemnika na ładowanie
- `not-found` - error 404
- `error` - obsługuje ErrorBoundary
- `global-error` - obsługuje główny error strony, zastępuje głóny root layout
- `route` - obsługuje request dla danej ścieżki w app
- `template` - podobny do `layout`, ale nie przechowuje stanu dzieci
- `default` - wykorzystywane w Paralel Routes

## dynamiczne routy

- `[folder]` - dynamiczny segment bez znanej nazwy, np [id], [slug].
Wartość przesyłana w paramsach, np. `params.slug`, trafia do plików w katalogu (layout, page, route). Uwag, params to _Promise_.
- `[...folder]` - segment przechwytujący wszystkie strzały, tzn. dowolną nazwę na tym poziomie i wszystkie poziomy niżej, np. `./slug/a`, `./slug/a/b`, 
- `[[...folder]]` - opcjonalny segment przechwytujący wszystkie strzałytzn. dowolną nazwę na tym poziomie i wszystkie poziomy niżej, np. `./slug/a`, `./slug/a/b`, ale także poziom sibling, tzn. `./slug`

## grupy i prywatne

- `(folder)` - tworzy grupę nie uwzględnianą w routingu, 
    - uwaga - layouty się rerenderują, jeśli są wewnątrz jako rooty
    - wewnętrzne routy nie mogą się nakładać
- `_folder` - foldery prywatne, do sortowania, wykluczania z rotungu, przechowywania UI, unikania konfliktów nazw, itp.

## foldery współbieżne i przechwytywanie rotów

- `@folder` - nazwany slot - folder tworzący sekcję, którą parent przechwytuje jako osobny fragment
    - slot przesyłany jest w propsach do parenta pod swoją nazwą: @analytics => props.analytics
    - sloty nie tworzą ścieżki, nazwa jest pomijana
    - na tym samym poziomie nie może być folderów statycznych i dynamicznych

## SEO

[SEO](https://nextjs.org/docs/app/getting-started/project-structure#seo)


## Organizajca projektu

### główny katalog

```
/app
/components
/lib
```

`/app` dopowiada tylko za przechowywanie tras

### katalog app jako root

```
/app
    /components
    /lib
    /some-path
    page.tsx
```

`/app` jest także pojemnikiem na inne katlogi


### katalogi grupowane (poza rotuowe)

```
/app
    (group_1)
        /some-path
    (group_2)
        /other-path
    page.tsx
```

`/app` jest także pojemnikiem na inne katlogi

### różne root layouty

usuń layout z katalogu root i dodaj osobne layouty w grupach (muszą zawierać html, body tags)