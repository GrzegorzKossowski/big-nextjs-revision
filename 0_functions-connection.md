# Funkcja `connection`

Funkcja connection() pozwala wskazać, czy renderowanie powinno poczekać na żądanie użytkownika przed kontynuacją.

Jest to przydatne, gdy komponent nie korzysta z dynamicznych interfejsów API , ale chcesz, aby był renderowany dynamicznie w czasie wykonywania, a nie statycznie w czasie kompilacji. Dzieje się tak zazwyczaj, gdy uzyskujesz dostęp do informacji zewnętrznych, które celowo chcesz zmienić w wyniku renderowania, takich jak Math.random() lub new Date().

Funkcja ta jest potrzebna wyłącznie wtedy, gdy wymagane jest renderowanie dynamiczne i nie są używane popularne interfejsy API Dynamic.

Funkcja zwraca promise, który nie jest wykorzystywany (coś jak sleep() lub wait() )

```tsx
import { connection } from 'next/server'
 
export default async function Page() {
  
  //... some code here

  await connection() // <------ TU czekamy na żądanie klienta
  
  // wszystko poniżej będzie wyłączone z prerenderingu statycznego
  const rand = Math.random()
  const date = new Date()
  return <span>{rand} {date}</span>
}
```