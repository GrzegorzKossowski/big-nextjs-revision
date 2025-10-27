# Instalacja

Utworzenie nowego projektu

```bash
npx create-next-app@latest my-app --yes
# --yes pomija monity instalacyjne (defaultowy install)

# ----------------------
# lub
# npx create-next-app@latest

# ----------------------
# lub ręcznie
# npm i next@latest react@latest react-dom@latest
# i dodaj w package.json skrypty:
# {
#   "scripts": {
#     "dev": "next dev",
#     "build": "next build",
#     "start": "next start",
#     "lint": "eslint",
#     "lint:fix": "eslint --fix"
#   }
# }

cd my-app
npm run dev
```

`next run dev` - uruchamia serwer deweloperski

`next run lint` - uruchamia eslint

`next run bulild` - buduje aplikację produkcyjną

`next start` - uruchamia serwer produkcyjny

Turbopack jest defaultowym pakietem do pakowania aplikacji

## katalog app

System routingu oparty jest o system katalogów w katalogu ./app

Podstawowym plikiem dostępu jest layout.tsx. Wymaga podstawowej struktury html:

```html
<!-- dla routu ./ -->
export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="pl">
      <body>{children}</body>
    </html>
  )
}
```

Wewnątrz layoutu jest importowana strona z treścią: page.tsx:

```tsx
export default function Page() {
  return <h1>Hello, Next.js!</h1>
}
```

`./ ==> ./app <= layout.tsx <= page.tsx`

## ./public

Katalogi public do przechowywania obrazów statycznych. Dostępny w kodzie jako base url: "./", np. `./public/image.jpg` to w kodzie `/image.jpg`.

## TS

nextjs automatycznie wykrywa i doinstalowuje wtyczki TS przy użyciu rozszerzeń ts/tsx.

## ESLint

```json
{
  "scripts": {
    "lint": "eslint",
    "lint:fix": "eslint --fix"
  }
}
```

Zalecane jest konfigurowanie w pliku `eslint.config.mjs`.

## importy absolutne @/.

Aby skrócić importy, należy ustawić w `tsconfig.json`:

```json
{
  "compilerOptions": {
    "baseUrl": "src/"
  }
}
```
Można także ustawić ścieżki do konkretnych katalogów:
```json
{
  "compilerOptions": {
    "baseUrl": "src/",
    "paths": {
      "@/styles/*": ["styles/*"],
      "@/components/*": ["components/*"]
    }
  }
}
```


