# Funkcja `fetch`

Next rozszerza interface `fetch`. Można korzystać z natywnych funkcji oraz rozszerzonych.

```tsx
export default async function Page() {
  let data = await fetch("https://api.vercel.app/blog");
  let posts = await data.json();
  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
}
```

Uwaga: rozszerzenie `fetch` obejmuje operacje na cache (ustawianie opcji) - [więcej](https://nextjs.org/docs/app/api-reference/functions/fetch)

### options.cache

Opcja odpowiedzialna za konfigurację żądania interakcji z pamięcią podręczną danych w next.

```js
fetch(`https://...`, { cache: "force-cache" | "no-store" });
```

- `auto` `no-cache` - w wersji build pobiera raz, o ile nie zostaną wykryte dynamiczne interfejsy api
- `no-store` - pobiera zasób za każdym razem
- `force-cache` - szuka żądania w pamięci, jeśli jest aktualne. Jeśli brak - pobierze z serwera i zapisze w pamięci.

### options.next.revalidate

Ustawia czas życia zasobu w pamięci w sekundach.

```js
fetch(`https://...`, { next: { revalidate: false | 0 | number } })
```

- `false` - buforuje bezterminowo (no revalidate)
- `0` - zapobiega buforowaniu
- `number` - ilość sekund życia w pamięci podręcznej

### options.next.tags

ustawia tagi w pamięci podręcznej

```js
fetch(`https://...`, { next: { tags: ['collection'] } })
```