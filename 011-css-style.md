# Style (CSS)

Next.js oferuje kilka sposobów na nadanie stylu Twojej aplikacji za pomocą CSS, w tym:

- Tailwind CSS
- Moduły CSS
- Globalny CSS
- Zewnętrzne arkusze stylów
- SASS
- CSS-in-JS

## Tailwind CSS

[Tailwind](https://tailwindcss.com/) CSSjest nastawionym na narzędzia frameworkiem CSS, który udostępnia niskopoziomowe klasy narzędziowe do tworzenia niestandardowych projektów.

## Moduły CSS

Moduły CSS lokalnie określają zakres CSS, generując unikalne nazwy klas. Pozwala to na używanie tej samej klasy w różnych plikach bez obawy o kolizje nazw.

Aby rozpocząć korzystanie z modułów CSS, utwórz nowy plik z rozszerzeniem .module.cssi zaimportuj go do dowolnego komponentu w appkatalogu:

```css
/* app/blog/blog.module.css */
.blog {
  padding: 24px;
}
```

```tsx
import styles from "./blog.module.css";

export default function Page() {
  return <main className={styles.blog}></main>;
}
```

## Globalny CSS

Za pomocą globalnego CSS możesz stosować style w całej aplikacji.

Utwórz `app/global.css` i zaimportuj go do układu głównego, aby zastosować style do każdej trasy w aplikacji.

## Zewnętrzne arkusze stylów

Arkusze stylów publikowane przez pakiety zewnętrzne można importować w dowolne miejsce katalogu app, w tym do współlokalizowanych komponentów.

```tsx
import "bootstrap/dist/css/bootstrap.css";
```

## Podsumowanie

- W środowisku produkcyjnym ( next build) wszystkie pliki CSS są automatycznie łączone w wiele zminimalizowanych i podzielonych .css plików, co zapewnia załadowanie minimalnej ilości kodu CSS dla danej trasy.
- W środowisku produkcyjnym arkusze CSS nadal ładują się z wyłączoną obsługą JavaScript, jednak JavaScript jest wymagany w fazie rozwoju w celu umożliwienia szybkiego odświeżania.
- Kolejność CSS może zachowywać się inaczej w trakcie rozwoju, zawsze należy sprawdzić kompilację ( next build), aby zweryfikować ostateczną kolejność CSS.
