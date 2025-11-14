# Optymalizacja czcionek

Next zawiera moduł 'next/font', który optymalizuje czcionki. Ładowanie czcionek nie zmienia layoutu. 

Przykładowe ładowanie czcionek google poprzez klasę komponentu:

```tsx
import { Geist } from 'next/font/google'
 
const geist = Geist({
  subsets: ['latin'],
})
 
export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en" className={geist.className}>
      <body>{children}</body>
    </html>
  )
}
```

## czcionki google

Czcionki ograniczają się do komponentu. Aby użyć ich w aplikacji, należy włączyć je do głównego układu (layoutu).

Fonty google są pobierane na dysk i serwowane lokalnie (nie ma zapytań do serwera api). Zalecane jest używanie fontów z pełnym zakresem rozmiarów. Inaczej, trzeba podać jej grubość:

```tsx
import { Roboto } from 'next/font/google'
 
const roboto = Roboto({
  weight: '400',
  subsets: ['latin'],
})
```

## czcionki lokalne

Można przypisać plik czcionki lokalnej. Font może być albo w pliku `public` albo w folderze `app`.

```tsx
import localFont from 'next/font/local'
 
const myFont = localFont({
  src: './my-font.woff2',
})

// lub jako array
const roboto = localFont({
  src: [
    {
      path: './Roboto-Regular.woff2',
      weight: '400',
      style: 'normal',
    },
    {
      path: './Roboto-Italic.woff2',
      weight: '400',
      style: 'italic',
    },
    //... more
  ],
})
```