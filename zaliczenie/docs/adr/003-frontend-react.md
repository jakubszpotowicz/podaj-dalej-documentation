# ADR-003: Użycie React + TypeScript + Vite jako frontendu

- **Status:** Zaakceptowana
- **Data:** 2026-10-28
- **Autorzy:** Michał Olszowy, Jakub Szpotowicz, Nikola Pisarska

---

## 1. Kontekst

Frontend to SPA z formularzami, mapą, czatem na żywo i panelem administratora. Wymagane testy automatyczne frontendu.

---

## 2. Rozważane opcje

### Opcja 1: React + TypeScript + Vite

- **Zalety:**
  - [+] Największy ekosystem (react-leaflet, klient SignalR, biblioteki formularzy).
  - [+] Vite daje szybki serwer deweloperski i natywną integrację z Vitest.
- **Wady:**
  - [-] React sam nie narzuca struktury; trzeba dobrać router i obsługę danych.

### Opcja 2: Angular

- **Zalety:**
  - [+] Kompletny framework z routerem i formularzami.
- **Wady:**
  - [-] Większy próg wejścia, mniej doświadczenia w zespole.

### Opcja 3: Vue 3 + Vite

- **Zalety:**
  - [+] Prosty i szybki w nauce.
- **Wady:**
  - [-] Mniej gotowych integracji map i mniejsza znajomość w zespole.

---

## 3. Decyzja

Wybieramy **React z TypeScript, budowany przez Vite**, z React Router i TanStack Query do danych z API.

---

## 4. Uzasadnienie

React ma gotowe komponenty dla Leaflet i klienta SignalR, a TypeScript pozwala generować typy z OpenAPI. Vite upraszcza konfigurację testów w Vitest.

---

## 5. Konsekwencje

### Pozytywne

- [+] Typy zgodne z API wykrywają błędy przy kompilacji.

### Negatywne

- [-] Zespół musi uzgodnić bibliotekę komponentów i konwencje struktury katalogów.
