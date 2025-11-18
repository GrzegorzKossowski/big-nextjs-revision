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
