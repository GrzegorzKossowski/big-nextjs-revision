# use cache

Dyrektywa `use cache` pozwala oznaczyć trasę, komponent React lub funkcję jako nadającą się do buforowania. Można jej użyć na początku pliku, aby wskazać, że wszystkie eksporty w pliku powinny być buforowane, lub na początku funkcji lub komponentu, aby buforować wartość zwracaną.

## włączanie

`use cache` to funkcja komponentów pamięci podręcznej. Aby ją włączyć, dodaj opcję cacheComponents do next.config.ts:

```ts
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  // dodaj tutaj
  cacheComponents: true,
};

export default nextConfig;
```

**UŻYCIE**

- na poziomie pliku, komponentu, funkcji

```tsx
// File level
'use cache'
 
export default async function Page() {
  // ...
}
 
// Component level
export async function MyComponent() {
  'use cache'
  return <></>
}
 
// Function level
export async function getData() {
  'use cache'
  const data = await fetch('/api/data')
  return data
}
```

