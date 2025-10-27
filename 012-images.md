# Optymalizacja obrazu

Komponent Image rozszerza znacznik img. Zapewnia:

- Optymalizacja rozmiaru: automatyczne wyświetlanie obrazów o prawidłowym rozmiarze dla każdego urządzenia przy użyciu nowoczesnych formatów obrazów, takich jak WebP.
- Stabilność wizualna: zapobieganie przesunięciom układuautomatycznie podczas ładowania obrazów.
- Szybsze ładowanie stron: obrazy są ładowane tylko wtedy, gdy pojawiają się w obszarze widoku, za pomocą natywnego leniwego ładowania przeglądarki, z opcjonalnymi symbolami zastępczymi rozmycia.
- Elastyczność zasobów: zmiana rozmiaru obrazów na żądanie, nawet obrazów przechowywanych na zdalnych serwerach.

## obrazy lokalne /public

Dane obrazów statycznych są określane automatycznie. Wystarczy zatem podanie niezbędnych danych:

```tsx
import Image from "next/image";
import ProfileImage from "./profile.png";

export default function Page() {
  return (
    <Image
      src={ProfileImage}
      alt="Picture of the author"
      // width={500} automatically provided
      // height={500} automatically provided
      // blurDataURL="data:..." automatically provided
      // placeholder="blur" // Optional blur-up while loading
    />
  );
}
```

## obrazy zdalne (url zewnętrzny)

```tsx
import Image from "next/image";

export default function Page() {
  return (
    <Image
      src="https://s3.amazonaws.com/my-bucket/profile.png"
      alt="Picture of the author"
      //   wymaga podania wymiarów celem obliczenia proporcji i uniknięcia przesunięć layoutu
      width={500}
      height={500}
    />
  );
}
```

Jeżeli nie znamy rozmiarów obrazu, ale mamy gotowy placeholder rodzica, możemy użyć opcji `fill`.

```tsx
import Image from "next/image";

export default function Page() {
  return (
    <div className="grid-element">
      <Image
        //   użyj fill, aby pobraćobraz
        fill
        src="/example.png"
        // zaplanuj rozmiary dla różnych wartości ekranu
        sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
      />
    </div>
  );
}
```

⚠️ UWAGA: Aby bezpiecznie zezwolić na obrazy z serwerów zdalnych, należy zdefiniować listę obsługiwanych wzorców adresów URL w pliku next.config.js.

```ts
// przykładowe użycie źródła danych obrazów
import type { NextConfig } from "next";
const config: NextConfig = {
  images: {
    remotePatterns: [
      {
        protocol: "https",
        hostname: "s3.amazonaws.com",
        port: "",
        pathname: "/my-bucket/**",
        search: "",
      },
    ],
  },
};
export default config;
```
