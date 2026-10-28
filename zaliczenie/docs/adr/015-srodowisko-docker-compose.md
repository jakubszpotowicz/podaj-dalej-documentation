# ADR-015: Uruchamianie środowiska przez Docker Compose

- **Status:** Zaakceptowana
- **Data:** 2026-10-28
- **Autorzy:** Michał Olszowy, Jakub Szpotowicz, Nikola Pisarska

---

## 1. Kontekst

Projekt musi uruchomić się u każdego członka zespołu i u prowadzącego. Składa się z API, frontendu, bazy, MinIO i serwera poczty do e-maili aktywacyjnych i przypomnień.

---

## 2. Rozważane opcje

### Opcja 1: Docker Compose z usługami: api, web, db (PostGIS), minio, mailpit

- **Zalety:**
  - [+] Jedno polecenie uruchamia całość.
  - [+] Mailpit pokazuje wysłane e-maile bez prawdziwej skrzynki.
- **Wady:**
  - [-] Wymaga Dockera i kilku GB pamięci.

### Opcja 2: Ręczna instalacja zależności na każdej maszynie

- **Zalety:**
  - [+] Brak Dockera.
- **Wady:**
  - [-] Różnice wersji między komputerami, długa instrukcja uruchomienia.

### Opcja 3: Kubernetes (np. minikube)

- **Zalety:**
  - [+] Bliżej produkcji.
- **Wady:**
  - [-] Nadmiarowa złożoność dla projektu semestralnego.

---

## 3. Decyzja

Wybieramy **Docker Compose**; migracje i dane przykładowe (kategorie, konto administratora) wykonują się przy starcie API. Konfiguracja przez `.env` z szablonem `.env.example`.

---

## 4. Uzasadnienie

Compose daje powtarzalne środowisko przy minimalnym nakładzie i jest wymagany przez stos projektu.

---

## 5. Konsekwencje

### Pozytywne

- [+] Instrukcja uruchomienia sprowadza się do `docker compose up`.

### Negatywne

- [-] Sekret klienta Google OAuth każdy dodaje lokalnie do `.env`; nie trafia do repozytorium.
