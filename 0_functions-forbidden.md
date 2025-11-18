# Funkcja `forbidden` 🧪

🧪 [TESTOWA] Pozwala na wyświetlenie strony błędu dla 403 (forbidden). Nie działa w root aplikacji. Używana w komponentach serwerowych, akcjach serwera, trasach.

Aktualnie wymaga włączenia elementów eksperymentalnych w konfigu. Nie zalecana w produkcji.

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
