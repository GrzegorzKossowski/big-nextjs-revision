# Layouts and pages

## pages

Strony generują wygląd, uczestniczą w routingu.

## layouts

Layout odpowiada za strukturę strony. Jest wrapperem dla page.tsx

- layout przechowuje stan w czasie nawigacji
- layout pozostaje interaktywny
- layout nie rerenderuje w czasie nawigacji

Root layout ma tagi html i body.

W NextJS foldery tworzą ścieżkę, pliki renderują wygląd (GUI).

## Nawigacja między stronami

Używaj `<Link>` do nawigacji.

Programowo można też używać hooka `useRouter()` (client?).



