# Funkcja `forbidden` 🧪

Aktualnie wymaga włączenia elementów eksperymentalnych w konfigu. Nie zalecana w produkcji.

```tsx
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  experimental: {
    authInterrupts: true,
  },
};

export default nextConfig;
```

🧪 [TESTOWA] Pozwala na wyświetlenie strony błędu dla 403 (forbidden). Nie działa w root aplikacji. Używana w komponentach serwerowych, akcjach serwera, trasach.

```tsx
import { verifySession } from "@/app/lib/dal";
import { forbidden } from "next/navigation";

export default async function AdminPage() {
  const session = await verifySession();

  // Check if the user has the 'admin' role
  if (session.role !== "admin") {
    forbidden();
  }

  // Render the admin page for authorized users
  return (
    <main>
      <h1>Admin Dashboard</h1>
      <p>Welcome, {session.user.name}!</p>
    </main>
  );
}
```

## mutacje (akcje serwerowe)

np. Pozwala na aktualizację danych wyłącznie wybranym użytkownikom. (role)

```tsx
'use server'
 
import { verifySession } from '@/app/lib/dal'
import { forbidden } from 'next/navigation'
import db from '@/app/lib/db'
 
export async function updateRole(formData: FormData) {
  const session = await verifySession()
 
  // Ensure only admins can update roles
  if (session.role !== 'admin') {
    forbidden()
  }
 
  // Perform the role update for authorized users
  // ...
}
```