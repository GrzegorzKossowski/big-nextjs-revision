# Komponenty serwera i klienta

## Kiedy?

- komponenty klienckie (interaktywność, hooki, przeglądarka API)
- komponenty serwerowe (backend, bazy danych, tokeny)

## 'use client'

Po oznaczeniu pliku dyrektywą "use client", **wszystkie jego importy i komponenty podrzędne** są uznawane za część pakietu klienta.

## Miksowanie komponentów serwerowych i klienckich

Jeśli podajemy komponent serwerowy jako children komponentu klienckiego, pozostaje on serwerowy.

```tsx
async function Page() {
  <Serwer>
    <Client>
      <Serwer />
    </Client>
  </Serwer>;
}
```

Jednak - wszelkie importy w komponentach klienckich i ich dzieciach stają się klienckie!

Warto zainstalować pakiet `server-only`.

```bash
npm install server-only
```
