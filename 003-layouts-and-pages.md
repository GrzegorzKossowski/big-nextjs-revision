# Układ strony

## strony (page.tsx)

Strony to komponenty reactowe eksportowane z pliku liścia `page.tsx`.

```tsx
export default function Page() {
  return <h1>Hello Next.js!</h1>;
}
```

## layouty (layout.tsx)

Układ to interfejs użytkownika współdzielony przez wiele stron. W nawigacji układy zachowują stan, pozostają interaktywne i nie są renderowane ponownie.

Layouty to komponenty reactowe przyjmując props `children` i zagnieżdżające go.

```tsx
export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>
        {/* Layout UI */}
        {/* Place children where you want to render a page or nested layout */}
        <main>{children}</main>
      </body>
    </html>
  );
}
```

## trasy zagnieżdżone

Foldery służą budowaniu trasy. Pliki layout i page służą budowaniu interfejsu, który jest wyświetlany na tej trasie. Strony zazwyczaj są asynchroniczne.

```tsx
// app/blog/page.tsx
import { getPosts } from "@/lib/posts";
import { Post } from "@/ui/post";

export default async function Page() {
  const posts = await getPosts();

  return (
    <ul>
      {posts.map((post) => (
        <Post key={post.id} post={post} />
      ))}
    </ul>
  );
}
```

Można tworzyć trasy przechwytujące, np.

```tsx
// app/blog/[slug]/page.tsx
export default async function Page({ params }: params: Promise<{ slug: string }>) {
  return <h1>Hello, Blog Post Page! {slug}</h1>;
}
```

## układy zagnieżdżone

Domyślnie układy w hierarchii folderów są również zagnieżdżone, co oznacza, że ​​zagnieżdżają układy podrzędne za pośrednictwem swojej właściwości `children`. Układy można zagnieżdżać, dodając je do określonych segmentów trasy (folderów).

```txt
./app/layout.tsx
./app/user/layout.tsx
./app/user/[id]/layout.tsx
./app/user/[id]/page.tsx
```

## Semgenty dynamiczne

Segmenty dynamiczne umożliwiają tworzenie tras generowanych na podstawie danych. Na przykład, zamiast ręcznie tworzyć trasę dla każdego wpisu na blogu, możesz utworzyć segment dynamiczny, aby generować trasy na podstawie danych z wpisu.

```tsx
export default async function BlogPostPage({
  params,
}: {
  params: Promise<{ slug: string }>;
}) {
  const { slug } = await params;
  const post = await getPost(slug);

  return (
    <div>
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </div>
  );
}
```

## Parametry wyszukiwania (searchParams)

Dostęp do parametrów zależnie od miejsca.

### dla strony page.tsx

użyj `searchParams` z propsów:

```tsx
// page.tsx (serwer, async)
export default async function Page({
  searchParams,
}: {
  searchParams: Promise<{ [key: string]: string | string[] | undefined }>;
}) {
  const filters = (await searchParams).filters;
}
```

### dla komponentu klienta

Użyj hooka `useSearchParams()`

```tsx
"use client";

import { useSearchParams } from "next/navigation";

// URL -> `/dashboard?search=my-project`
export default function SearchBar() {
  // `search` -> 'my-project'
  const searchParams = useSearchParams();
  const search = searchParams.get("search");
  return <>Search: {search}</>;
}
```

## Łączenie stron

Do nawigacji można używać komponentu `<Link>`. Rozszerza on znacznik `<a>` dodanjąc m.in. preloading i nawigację po stronie klienta. To podstawowy sposób. W przypadkach zaawansowanych można użyć hooka `useRouter`.

```tsx
import Link from "next/link";

export default async function Post({ post }) {
  return <Link href={`/blog`}>Blog</Link>;
}
```

## Podsumowanie

```tsx
export default async function Page({
  params, 
  searchParams,
}: {
  params: Promise<{ slug: string }>;
  searchParams: Promise<{ [key: string]: string | string[] | undefined }>;
}) {
  //...
}
```

```tsx
'use client'
 
import { useSearchParams } from 'next/navigation'
 
export default function SearchBar() {
  const searchParams = useSearchParams()
  const search = searchParams.get('search')
 
  // URL -> `/dashboard?search=my-project`
  // `search` -> 'my-project'
  return <>Search: {search}</>
}
```
