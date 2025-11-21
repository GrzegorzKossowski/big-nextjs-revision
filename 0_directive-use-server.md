# use server

Dyrektywa use serverwskazuje funkcję lub plik, który ma zostać wykonany po stronie serwera. Można jej użyć na początku pliku, aby wskazać, że wszystkie funkcje w pliku są wykonywane po stronie serwera, lub w linii na początku funkcji, aby oznaczyć ją jako funkcję [serwerową](https://react.dev/reference/rsc/server-functions). To jest funkcja React.

React: Funkcje serwerowe umożliwiają komponentom klienckim wywoływanie funkcji asynchronicznych wykonywanych na serwerze.

## Poziom pliku

Wszystkie funkcje w pliku są wykonywane na serwerze.

```tsx
"use server";
import { db } from "@/lib/db";

export async function createUser(data: { name: string; email: string }) {
  const user = await db.user.create({ data });
  return user;
}
```

UWAGA: Aby korzystać z funkcji serwera w komponentach klienckich, należy utworzyć funkcje serwera w **dedykowanym, osobnym pliku**, korzystając z 'use server' na początku pliku. Funkcje serwera można następnie zaimportować do komponentów klienckich i serwerowych i uruchomić.

```tsx
"use client";
import { fetchUsers } from "../actions"; // <--- 'use server'

export default function MyButton() {
  return <button onClick={() => fetchUsers()}>Fetch Users</button>;
}
```

## Poziom funkcji

Dodaj `'use server'` na górze ciała funkcji **asynchronicznej**, aby oznaczyć funkcję jako wywoływalną przez klienta. Funkcje te nazywamy funkcjami serwerowymi .

```tsx
import { EditPost } from "./edit-post";
import { revalidatePath } from "next/cache";

export default async function PostPage({ params }: { params: { id: string } }) {
  const post = await getPost(params.id);

  async function updatePost(formData: FormData) {
    "use server"; // <------------- funkcja serwerowa
    await savePost(params.id, formData);
    revalidatePath(`/posts/${params.id}`);
  }

  return <EditPost updatePostAction={updatePost} post={post} />;
}
```

## SECURITY

UWAGA: Zawsze uwierzytelniaj i autoryzuj użytkowników przed wykonywaniem poufnych operacji po stronie serwera.

Argumenty funkcji serwera są w pełni kontrolowane przez klienta. Ze względów bezpieczeństwa zawsze traktuj je jako niezaufane dane wejściowe i upewnij się, że odpowiednio sprawdzasz walidację i stosujesz znaki ucieczki argumentów.

W przypadku dowolnej funkcji serwera upewnij się, że zalogowany użytkownik ma uprawnienia do wykonania danej czynności.

```tsx
"use server"; // <---------- funkcja serwerowa

import { db } from "@/lib/db"; // Your database client
import { authenticate } from "@/lib/auth"; // Your authentication library

export async function createUser(
  data: { name: string; email: string },
  token: string
) {
  const user = authenticate(token);
  //   autoryzuj uzera!
  if (!user) {
    throw new Error("Unauthorized");
  }
  // dopiero wykonuj działanie!
  const newUser = await db.user.create({ data });
  return newUser;
}
```
