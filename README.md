# Tracker Matematyki Konkursowej — GitHub sync

Ta paczka ma dwie części:

- `index.html` — frontend do wrzucenia na GitHub Pages i osadzenia w Notion.
- `worker.js` — Cloudflare Worker, który bezpiecznie zapisuje/wczytuje progres do pliku JSON w repozytorium GitHub.

Token GitHuba **nie trafia do HTML-a**. Jest trzymany jako sekret w Cloudflare Worker.

## Rekomendowana architektura

Najczyściej użyć dwóch repozytoriów:

1. `tracker-matematyka` — publiczne repo z `index.html`, publikowane przez GitHub Pages.
2. `tracker-matematyka-progress` — prywatne repo z plikiem `progress/progress.json`, używane tylko jako magazyn progresu.

Możesz użyć jednego publicznego repo, ale wtedy `progress/progress.json` będzie publicznie widoczny.

---

## 1. Frontend na GitHub Pages

1. Utwórz repo, np. `tracker-matematyka`.
2. Wgraj do niego:
   - `index.html`
   - `.nojekyll`
3. Wejdź w `Settings → Pages`.
4. Ustaw:
   - Source: `Deploy from a branch`
   - Branch: `main`
   - Folder: `/ (root)`
5. Zapisz.

Po publikacji dostaniesz URL podobny do:

```txt
https://twoj-login.github.io/tracker-matematyka/
```

---

## 2. Repo na progres

Utwórz najlepiej prywatne repo:

```txt
tracker-matematyka-progress
```

Nie musisz ręcznie tworzyć pliku `progress/progress.json`. Worker utworzy go przy pierwszym zapisie.

---

## 3. GitHub token

Stwórz **fine-grained personal access token**.

Ustaw:

- Repository access: tylko `tracker-matematyka-progress`
- Repository permissions:
  - Contents: `Read and write`

Nie wklejaj tokena do HTML-a ani do Notion.

---

## 4. Cloudflare Worker — wariant przez CLI

Zainstaluj/uruchom Wrangler przez `npx`:

```bash
npm create cloudflare@latest math-tracker-sync
```

Następnie w projekcie Workera podmień wygenerowany kod na `worker.js` z tej paczki i użyj `wrangler.toml` jako wzoru.

W `wrangler.toml` uzupełnij:

```toml
[vars]
GITHUB_OWNER = "twoj-login"
GITHUB_REPO = "tracker-matematyka-progress"
GITHUB_BRANCH = "main"
GITHUB_PATH = "progress/progress.json"
ALLOWED_ORIGINS = "https://twoj-login.github.io"
```

Ustaw sekrety:

```bash
npx wrangler secret put GITHUB_TOKEN
npx wrangler secret put SYNC_KEY
```

`GITHUB_TOKEN` to token z GitHuba.  
`SYNC_KEY` to Twoje własne hasło do synchronizacji, np. długi losowy tekst. Ten klucz wpiszesz potem w UI trackera.

Deploy:

```bash
npx wrangler deploy
```

Po deployu dostaniesz adres Workera podobny do:

```txt
https://math-tracker-sync.twoj-login.workers.dev
```

---

## 5. Cloudflare Worker — wariant przez dashboard

1. Cloudflare Dashboard → Workers & Pages → Create Worker.
2. Wklej kod z `worker.js`.
3. Dodaj zmienne zwykłe:
   - `GITHUB_OWNER`
   - `GITHUB_REPO`
   - `GITHUB_BRANCH`
   - `GITHUB_PATH`
   - `ALLOWED_ORIGINS`
4. Dodaj sekrety:
   - `GITHUB_TOKEN`
   - `SYNC_KEY`
5. Deploy.

---

## 6. Połączenie w trackerze

Otwórz tracker z GitHub Pages i w panelu sync wpisz:

- Worker URL: adres Workera, np. `https://math-tracker-sync.twoj-login.workers.dev`
- Sync key: ten sam tekst, który ustawiłeś jako sekret `SYNC_KEY`

Kliknij:

```txt
Zapisz sync
```

Potem używaj:

- `Zapisz online` — zapisuje obecny progres do GitHuba.
- `Wczytaj online` — pobiera progres z GitHuba i zastępuje lokalny stan.

Lokalny zapis przez `localStorage` nadal działa natychmiast po kliknięciu statusu.

---

## 7. Notion

W Notion użyj `/embed` i wklej URL z GitHub Pages, np.:

```txt
https://twoj-login.github.io/tracker-matematyka/
```

Panel sync będzie działał także w embedzie, bo cały frontend nadal działa jako ta sama strona GitHub Pages.

---

## Ważne bezpieczeństwo

- Token GitHuba trzymaj tylko w Cloudflare Worker jako secret.
- `SYNC_KEY` nie jest tokenem GitHuba, ale też traktuj go prywatnie.
- Jeżeli ktoś pozna `SYNC_KEY`, może nadpisać Twój plik progresu przez Workera.
- Jeżeli chcesz używać tylko jednego publicznego repo, progres może być publiczny. Dla prywatności lepsze jest osobne prywatne repo na progres.
