# Fonts

[Fonty](https://nextjs.org/docs/app/getting-started/fonts)

## How to use fonts

- next optymalizuje fonty, preloaduje je
- można używać fontów zarówno google, jak i lokalnych (`/next/font/local`, `/next/font/google`)
- font jest przypisany do scope elementu i jego dzieci (dla całej aplikacji - użyj w root layout)

```tsx
import { Geist } from "next/font/google";

const geist = Geist({
  subsets: ["latin"],
});

export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en" className={geist.className}>
      <body>{children}</body>
    </html>
  );
}
```

## Google fonts

- fonty google są preloadowane, tzn. nie są wykonywane osobne requesty do google.fonts
- preferowane są [variable fonts](https://fonts.google.com/variablefonts)
- jeśli nie, należy podać wagę fontu:

```tsx
const roboto = Roboto({
  weight: "400", // waga fontu
  subsets: ["latin"],
});
```

## Local fonts

- można używać fontów lokalnych
- fonty przechowujemy w katalogu `/public`
- należy podać ścieżkę `src`

```tsx
import localFont from "next/font/local";

const myFont = localFont({
  src: "./my-font.woff2",
});
```

- w przypadku wielu plików z danej rodziny, podać tablicę:

```tsx
const roboto = localFont({
  src: [
    {
      path: "./Roboto-Regular.woff2",
      weight: "400",
      style: "normal",
    },
    {
      path: "./Roboto-Italic.woff2",
      weight: "400",
      style: "italic",
    },
    //...
  ],
});
```

## API Reference

[font api](https://nextjs.org/docs/app/api-reference/components/font)
