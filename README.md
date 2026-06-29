# Tracker Matematyki Konkursowej

To jest statyczna mini‑webappka gotowa do publikacji przez GitHub Pages.

## Jak opublikować przez GitHub Pages

1. Utwórz nowe repozytorium na GitHubie, np. `tracker-matematyka`.
2. Wrzuć do repozytorium pliki z tej paczki:
   - `index.html`
   - `.nojekyll`
   - `README.md` opcjonalnie
3. Wejdź w repozytorium → **Settings** → **Pages**.
4. W sekcji **Build and deployment** wybierz:
   - Source: **Deploy from a branch**
   - Branch: **main**
   - Folder: **/(root)**
5. Kliknij **Save**.
6. Po chwili strona powinna być dostępna pod adresem:
   `https://TWOJ_LOGIN.github.io/tracker-matematyka/`

## Jak osadzić w Notion

1. Skopiuj link do opublikowanej strony GitHub Pages.
2. W Notion wpisz `/embed`.
3. Wklej link.
4. Rozciągnij embed na całą szerokość strony.

## Ważne

Postęp zapisuje się w `localStorage`, czyli lokalnie w przeglądarce. Działa po odświeżeniu strony, ale nie synchronizuje się automatycznie między urządzeniami.
