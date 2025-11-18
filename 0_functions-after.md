# Funkcja `after`

Pozwala na zaplanowanie zadania i wykonanie go po wstępnym renderowaniu lub/i wysłaniu odpowiedzi. Do zastosowania w komponentach serwerowych, akcjach, trasach i proxy.

```tsx
import { after } from 'next/server'
// Custom logging function
import { log } from '@/app/utils'
 
export default function Layout({ children }: { children: React.ReactNode }) {
  after(() => {
    // Execute after the layout is rendered and sent to the user
    log()
  })
  return <>{children}</>
}
```

UWAGA: `after` zostanie wykonane, nawet jeśli zostanie rzucony błąd, notFound lub redirect. Przypomina z jednej strony useEffect? z drugiej - finally (try-catch)

## interfejsy API

Pozwala na wykonanie operacji po response (np. do rejestrowania usera itp.)

UWAGA: działa tylko w trasach, nie działa w komponentach serwera

```tsx
import { after } from 'next/server'
import { cookies, headers } from 'next/headers'
import { logUserAction } from '@/app/utils'
 
export async function POST(request: Request) {
  // Perform mutation
  // ...
 
  // Log user activity for analytics
  after(async () => {
    const userAgent = (await headers().get('user-agent')) || 'unknown'
    const sessionCookie =
      (await cookies().get('session-id'))?.value || 'anonymous'
 
    logUserAction({ sessionCookie, userAgent })
  })
 
  return new Response(JSON.stringify({ status: 'success' }), {
    status: 200,
    headers: { 'Content-Type': 'application/json' },
  })
}
```