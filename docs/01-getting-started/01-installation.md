# Instalacja

## instalacja automatyczna

```bash
#  instalacja automatyczna
npx create-next-app@latest


What is your project named? my-app
Would you like to use TypeScript? No / Yes
Would you like to use ESLint? No / Yes
Would you like to use Tailwind CSS? No / Yes
Would you like your code inside a `src/` directory? No / Yes
Would you like to use App Router? (recommended) No / Yes
Would you like to use Turbopack for `next dev`?  No / Yes
Would you like to customize the import alias (`@/*` by default)? No / Yes
What import alias would you like configured? @/*
```



```bash
# instalacja ręczna
npm install next@latest react@latest react-dom@latest
```



```json
//skrypty
{
  "scripts": {
    // dev server
    "dev": "next dev",
    // build for prod
    "build": "next build",
    // starts prod server
    "start": "next start",
    // runs eslint
    "lint": "next lint"
  }
}
```

## app dir

Głównym katalogiem aplikacji jest `/app`

- layout.tsx - zawiera layout strony, załącza page.tsx
- brak layoutu - tworzony automatycznie

- katalog `public` przechowuje statyczne assety. W kodzie dostępne z poziomu root `/`

## Ścieżki absolutne

Skrócenie ścieżek:

```tsx
// Before
import { Button } from '../../../components/button'
 
// After
import { Button } from '@/components/button'
```

```json
// konfiguracja
{
  "compilerOptions": {
    // zmienia ../../../xxx na @/xxx 
    "baseUrl": "src/",
    // zmienia dowolne ścieżki, np.
    // @/styles/* na styles/*
    "paths": {
      "@/styles/*": ["styles/*"],
      "@/components/*": ["components/*"]
    }
  }
}
```