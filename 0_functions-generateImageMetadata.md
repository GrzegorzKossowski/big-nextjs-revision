# Funkcja `generateImageMetadata`

Możesz użyć tej funkcji generateImageMetadata, aby wygenerować różne wersje jednego obrazu lub zwrócić wiele obrazów dla jednego segmentu trasy. Jest to przydatne, gdy chcesz uniknąć sztywnego kodowania wartości metadanych, na przykład dla ikon.

## parametry

- `params` - fakultatywny - obiekt dynamicznych parametrów trasy z segmentu głównego w dół do segmentu generateImageMetadata

```tsx
export function generateImageMetadata({
  params,
}: {
  params: { slug: string | string[] };
}) {
  // ...
}
```

| Trasa                         | Adres URL | params                  |
| ----------------------------- | --------- | ----------------------- |
| app/shop/icon.js              | /shop     | undefined               |
| app/shop/[slug]/icon.js       | /shop/1   | { slug: '1' }           |
| app/shop/[tag]/[item]/icon.js | /shop/1/2 | { tag: '1', item: '2' } |

## return

Funkcja zwraca tablicę danych obrazu, jak `alt`, `size` itp.

| Obiekt metadanych obrazu | Typ                               |
| ------------------------ | --------------------------------- |
| id                       | string(wymagany)                  |
| alt                      | string                            |
| size                     | { width: number; height: number } |
| contentType              | string                            |

```tsx
// icon.tsx
import { ImageResponse } from "next/og";

export function generateImageMetadata() {
  return [
    {
      contentType: "image/png",
      size: { width: 48, height: 48 },
      id: "small",
    },
    {
      contentType: "image/png",
      size: { width: 72, height: 72 },
      id: "medium",
    },
  ];
}

export default async function Icon({ id }: { id: Promise<string | number> }) {
  const iconId = await id;
  return new ImageResponse(
    (
      <div
        style={{
          width: "100%",
          height: "100%",
          display: "flex",
          alignItems: "center",
          justifyContent: "center",
          fontSize: 88,
          background: "#000",
          color: "#fafafa",
        }}
      >
        Icon {iconId}
      </div>
    )
  );
}
```

## parametry

**`id`**

Promise jako wartość elemnentu wzwróconego przez generateImageMetadata do komponentu Image.

```tsx
export default async function Icon({ id }: { id: Promise<string | number> }) {
  const iconId = await id;
  // Use iconId to generate the image
}
```

**`params`**

Promise fakultatywny, zawierający dynamiczne elementy trasy

```tsx
export default async function Icon({
  params,
}: {
  params: Promise<{ slug: string }>;
}) {
  const { slug } = await params;
  // Use slug to generate the image
}
```

CDN - https://nextjs.org/docs/app/api-reference/functions/generate-image-metadata#params-optional-1
