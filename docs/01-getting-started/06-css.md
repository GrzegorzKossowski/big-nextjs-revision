# CSS

## How to use

Możliwe zastosowania css w next

- CSS Modules
- Global CSS
- External Stylesheets
- Tailwind CSS
- Sass
- CSS-in-JS


## CSS Modules

- moduły css generują lokalne nazwy dla klas
- wymagają nazwy z końcówką `.module.css`

```css
.blog {
  padding: 24px;
}
```

```tsx
import styles from './styles.module.css'
 
export default function Page({ children }: { children: React.ReactNode }) {
  return <main className={styles.blog}>{children}</main>
}
```

## Global CSS

- next używa globalnych klas
- wstaw `/app/global.css`

```css
/* global.css */
body {
  padding: 20px 20px 60px;
  max-width: 680px;
  margin: 0 auto;
}
```

```tsx
// These styles apply to every route in the application
import './global.css'
 
export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  )
}
```

UWAGA: stosowanie plików globalnych we wszystkich layoutach będzie powodować konflikty klas...

## External stylesheets

UWAGA: zewnętrzne pliki css z paczek npm mogą być importowane do dowolnego pliku

```tsx
// import z paczki
import 'bootstrap/dist/css/bootstrap.css'
 
export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body className="container">{children}</body>
    </html>
  )
}
```



