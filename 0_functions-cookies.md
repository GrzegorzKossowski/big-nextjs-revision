# Funkcja `cookies`

Funkcja `cookies` jest funkcją asynchroniczną umożliwiającą odczytywanie plików cookie z żądań **przychodzących** HTTP w komponentach serwera oraz odczytywanie/zapisywanie plików cookie dla żądań **wychodzących** w akcjach serwera lub obsłudze tras.

Jest to funkcja asynchroniczna. Wymaga użycia async/await.

```tsx
import { cookies } from "next/headers";

export default async function Page() {
  const cookieStore = await cookies();
  const theme = cookieStore.get("theme");
  return "...";
}
```

## Metody

| Metoda                    | Typ zwracany     | Opis                                                                                                 |
| ------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------- |
| get('name')               | Obiekt           | Akceptuje nazwę pliku cookie i zwraca obiekt z nazwą i wartością.                                    |
| getAll()                  | Tablica obiektów | Zwraca listę wszystkich plików cookie o pasującej nazwie.                                            |
| has('name')               | boolean          | Akceptuje nazwę pliku cookie i zwraca wartość logiczną w oparciu o to, czy plik cookie istnieje.     |
| set(name, value, options) | -                | Akceptuje nazwę, wartość i opcje pliku cookie, a następnie ustawia plik cookie żądania wychodzącego. |
| delete(name)              | -                | Akceptuje nazwę pliku cookie i usuwa go.                                                             |
| clear()                   | -                | Usuwa wszystkie pliki cookie.                                                                        |
| toString()                | string           | Zwraca ciąg znaków reprezentujący pliki cookie.                                                      |

## Opcje

| Opcja       | Typ                             | Opis                                                                                                                      |
| ----------- | ------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| name        | string                          | Określa nazwę pliku cookie.                                                                                               |
| value       | string                          | Określa wartość, która ma zostać zapisana w pliku cookie.                                                                 |
| expires     | Data                            | Definiuje dokładną datę wygaśnięcia pliku cookie.                                                                         |
| maxAge      | Numer                           | Ustawia czas trwania pliku cookie w sekundach.                                                                            |
| domain      | string                          | Określa domenę, w której dostępny jest plik cookie.                                                                       |
| path        | Ciąg, domyślny:'/'              | Ogranicza zakres pliku cookie do określonej ścieżki w obrębie domeny.                                                     |
| secure      | Boole'a                         | Zapewnia, że ​​plik cookie jest wysyłany wyłącznie za pośrednictwem połączeń HTTPS, co zapewnia dodatkowe bezpieczeństwo. |
| httpOnly    | Boole'a                         | Ogranicza pliki cookie do żądań HTTP, uniemożliwiając dostęp po stronie klienta.                                          |
| sameSite    | Boole'a, 'lax', 'strict','none' | Kontroluje zachowanie się pliku cookie w przypadku żądań międzywitrynowych.                                               |
| priority    | Ciąg ( "low", "medium", "high") | Określa priorytet pliku cookie                                                                                            |
| partitioned | Boole'a                         | Wskazuje, czy plik cookie jest podzielony.                                                                                |

Szczegóły na [MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies)


UWAGA: `cookies` to API dynamiczne - powoduje automatyczne przestawienie akcji serwera lub trasy na renderowanie dynamiczne.

1. pliki cookie to dane z klienta - nie są znane przed otrzymaniem żądania
2. dane cookie pobiera serwer z nagłówków HTTP
3. pliki cookie nie mogą być ustawiane w komponencie serwera, bo to przeglądarka je obsługuje (klient)
4. serwer może jedynie wysłać instrukcję (Set-Cookie) do przeglądarki (można to zrobić tylko w obsłudze trasy lub akcji, gdzie da sięustawić nagłówki odpowiedzi (headers))

Zmiany cookie po stronie serwera wymagają odświeżenia pamięci (revalidatePath, revalidateTag)

## Pobranie cookie

```tsx
import { cookies } from 'next/headers'
 
export default async function Page() {
  const cookieStore = await cookies()
  const theme = cookieStore.get('theme')
  return '...'
}
```

## Pobranie wszystkich cookie

```tsx
import { cookies } from 'next/headers'
 
export default async function Page() {
  const cookieStore = await cookies()
  // TU ----------------v
  return cookieStore.getAll().map((cookie) => (
    <div key={cookie.name}>
      <p>Name: {cookie.name}</p>
      <p>Value: {cookie.value}</p>
    </div>
  ))
}
```

## Ustawianie cookie

Uwaga: możesz użyć tej metody `(await cookies()).set(name, value, options)` w akcji serwera lub programie obsługi trasy, aby ustawić plik cookie.

```tsx
'use server'
 
import { cookies } from 'next/headers'
 
export async function create(data) {
  const cookieStore = await cookies()
 
  cookieStore.set('name', 'lee')
  // or
  cookieStore.set('name', 'lee', { secure: true })
  // or
  cookieStore.set({
    name: 'name',
    value: 'lee',
    httpOnly: true,
    path: '/',
  })
}
```

## Sprawdzanie istnienia cookie

```tsx
import { cookies } from 'next/headers'
 
export default async function Page() {
  const cookieStore = await cookies()
  const hasCookie = cookieStore.has('theme') // <----------- TU 
  return '...'
}
```

## Usuwanie cookies

1. delete

```tsx
'use server'
 
import { cookies } from 'next/headers'
 
export async function deleteCookie(data) {
  const cookieStore = await cookies()
  cookieStore.delete('name') // <----------- TU
}
```

2. "" - empty string

```tsx
'use server'
 
import { cookies } from 'next/headers'
 
export async function deleteCookie(data) {
  const cookieStore = await cookies()
  cookieStore.set('name', '') // <----------- TU
}
```

3. maxAge=0

```tsx
'use server'
 
import { cookies } from 'next/headers'
 
export async function deleteCookie(data) {
  const cookieStore = await cookies()
  cookieStore.set('name', 'value', { maxAge: 0 }) // <---------- TU
}
```
