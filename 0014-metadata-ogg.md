# Metadane i obrazy OG

Metadane ułatwiają podanie danych i poprawę SEO. Obejmują

- statyczny obiekt `metadata`
- dynamiczną funkcję `generateMetadata`
- statyczne lub dynamiczne favicony
- statyczne lub dynamiczne obrazy OG

Eksport tych funkcjonalności **tylko w komponentach serwerowych**. Next automatycznie generuje tagi `<meta>` w sekcji `<head>`. 


## metadane

Zawsze dodawane są automatycznie dwa pola meta:

```html
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
```

### statyczne meta

Aby wygenerować statyczne meta, używamy eksportu obiektu klasy Metadata (z layoutu lub page):

```tsx
import type { Metadata } from 'next'
 
export const metadata: Metadata = {
  title: 'My Blog',
  description: '...',
}
 
export default function Layout() {}
```

Można użyć funkcji `generateMetadata` celem dostosowania do załadowanych w locie danych:

```tsx
import type { Metadata, ResolvingMetadata } from 'next'
 
type Props = {
  params: Promise<{ slug: string }>
  searchParams: Promise<{ [key: string]: string | string[] | undefined }>
}
 
export async function generateMetadata(
  { params, searchParams }: Props,
  parent: ResolvingMetadata
): Promise<Metadata> {
  const slug = (await params).slug
 
  // fetch post information
  const post = await fetch(`https://api.vercel.app/blog/${slug}`).then((res) =>
    res.json()
  )
 
  return {
    title: post.title,
    description: post.description,
  }
}
 
export default function Page({ params, searchParams }: Props) {}
```

**Strumieniowanie**

W tym rozwiązaniu next przesyła metadane osobno i wstrzykuje je u klienta, poprawiając wydajność obsługi treści. Jest jednak wyłączone dla botów indeksujących. Są one wykrywane z nagłówka podczas żądania (User Agent). Strumieniowanie można wyłączyć w konfiguracji Next. 

### Zapamiętywanie żądań danych!

W przypadku pobierania tych samych danych (np. tytułu artykułu), należy unikać wielokrotnego pobierania danych (dla metadanych i samej strony). W tym celu używamy React `cache`.


```tsx
// pobieranie danych - data.ts
import { cache } from 'react'
import { db } from '@/app/lib/db'
 
// getPost will be used twice, but execute only once
export const getPost = cache(async (slug: string) => {
  const res = await db.query.posts.findFirst({ where: eq(posts.slug, slug) })
  return res
})
```

```tsx
// użycie danych - page.tsx
import { getPost } from '@/app/lib/data'
 
export async function generateMetadata({
  params,
}: {
  params: { slug: string }
}) {
  const post = await getPost(params.slug)
  return {
    title: post.title,
    description: post.description,
  }
}
 
export default async function Page({ params }: { params: { slug: string } }) {
  const post = await getPost(params.slug)
  return <div>{post.title}</div>
}
```

### Przykład z TRPC, drizzle itp.

```bash
src/
 ├─ app/
 │   └─ articles/
 │       └─ [slug]/
 │            ├─ page.tsx
 │            └─ generateMetadata.ts
 ├─ db/
 │   ├─ db.ts
 │   └─ schema/
 │        └─ articles.ts
 ├─ trpc/
 │   ├─ router/
 │   │    └─ articles.ts
 │   ├─ trpc.ts
 │   └─ trpc-caller.ts
 └─ lib/
      └─ article-cache.ts
```

**TRPC**

```tsx
// trpc/router/articles.ts (procedura)
export const articlesRouter = router({
  getBySlug: publicProcedure
    .input(z.string())
    .query(async ({ input }) => {
      const [article] = await db
        .select()
        .from(articles)
        .where(eq(articles.slug, input))
        .limit(1);

      return article ?? null;
    }),
});
```

**TRPC Server caller**

```ts
// trpc/trpc-caller.ts
import { appRouter } from "./router";
import { db } from "./db";

export const caller = appRouter.createCaller({ db });
```

**CACHE**

```ts
// lib/article-cache.ts
import { cache } from "react";
import { caller } from "@/server/trpc-caller";

// Funkcja cached — będzie współdzielona między page & generateMetadata
export const getArticleCached = cache(async (slug: string) => {
  return caller.articles.getBySlug(slug);
});
```

**METADATA**

```tsx
// generate-metadata.ts
import { getArticleCached } from "@/lib/article-cache";

export async function generateMetadata({ params }) {
  const article = await getArticleCached(params.slug);

  if (!article) {
    return {
      title: "Artykuł nie znaleziony",
    };
  }

  return {
    title: article.title,
    description: article.description ?? "",
  };
}
```

**PAGE**

```tsx
// page.tsx
import { getArticleCached } from "@/lib/article-cache";

export default async function ArticlePage({ params }) {
  const article = await getArticleCached(params.slug);

  if (!article) {
    return <h1>Nie znaleziono artykułu</h1>;
  }

  return (
    <article className="prose">
      <h1>{article.title}</h1>
      <p>{article.content}</p>
    </article>
  );
}
```

## Favicon

Favicony ładowane są automatycznie z pliku favicon.ico (z głównego katalogu `/app/favicon.ico`) lub generowane kodem ([favicons](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/app-icons)).


## OG images

Obrazy Open Graph (OG) to obrazy reprezentujące witrynę w mediach społecznościowych. Aby dodać statyczny obraz OG do aplikacji, utwórz `opengraph-image.png` w folderze głównym aplikacji. Można także dodawać osobne obrazy dla każdej trasy (w danym katalogu trasy, np. `./app/blog/opengraph-image.png`). Możliwe są różne formaty, np. jpeg, png, gif ([różne formaty](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/opengraph-image)).


Możliwe jest automatyczne generowanie obrazów OG dla każdego wpisu, np. artykułu itp. W tym celu należy utworzyć plik opengraph-image.tsx w danym folderze i użyć konstruktora ImageResponse. ImageResponse umożliwa obsługę CSS oraz wstawianie obrazków.

```tsx
// appo/blog/[slug]/opengraph-image.tsx
import { ImageResponse } from 'next/og'
import { getPost } from '@/app/lib/data'
 
// Image metadata
export const size = {
  width: 1200,
  height: 630,
}
 
export const contentType = 'image/png'
 
// Image generation
export default async function Image({ params }: { params: { slug: string } }) {
  const post = await getPost(params.slug)
 
  return new ImageResponse(
    (
      // ImageResponse JSX element
      <div
        style={{
          fontSize: 128,
          background: 'white',
          width: '100%',
          height: '100%',
          display: 'flex',
          alignItems: 'center',
          justifyContent: 'center',
        }}
      >
        {post.title}
      </div>
    )
  )
}
```

## Pola Metadata [docs](https://nextjs.org/docs/app/api-reference/functions/generate-metadata)

Szczegółowe zasady w dokumentacji.
