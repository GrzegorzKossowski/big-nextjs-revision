# Images

Obrazy osadzamy za pomocą komponentu `<Image>`.

- optymizajca rozmiaru
- stabilizacja
- szybsze ładowanie (lazy loading)
- resize on demand

## local

Obrazy lokalne z katalogu `/public`.
Z poziomu kodu dostęp jako root: `/`.

## remote

Obrazy zdalne przez podanie url.
Muszą być podane rozmiary. 

UWAGA: należy podać listę bezpiecznych adresów w next.config.ts

```ts
import { NextConfig } from 'next'
 
const config: NextConfig = {
  images: {
    remotePatterns: [
      {
        protocol: 'https',
        hostname: 's3.amazonaws.com',
        port: '',
        pathname: '/my-bucket/**',
        search: '',
      },
    ],
  },
}
 
export default config
```


