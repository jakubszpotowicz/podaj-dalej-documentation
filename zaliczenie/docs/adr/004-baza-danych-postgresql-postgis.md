# ADR-004: Użycie PostgreSQL z PostGIS jako bazy danych

- **Status:** Zaakceptowana
- **Data:** 2026-10-28
- **Autorzy:** Michał Olszowy, Jakub Szpotowicz, Nikola Pisarska

---

## 1. Kontekst

Dane są silnie relacyjne (użytkownicy, oferty, propozycje, transakcje, oceny). Wyszukiwanie musi filtrować po odległości od wybranego punktu i sortować wyniki po dystansie, łącznie z filtrami nazwy i kategorii.

---

## 2. Rozważane opcje

### Opcja 1: PostgreSQL + PostGIS (EF Core, NetTopologySuite)

- **Zalety:**
  - [+] Transakcje ACID i relacje.
  - [+] Indeks przestrzenny i dokładne odległości w metrach w jednym zapytaniu SQL z innymi filtrami.
- **Wady:**
  - [-] Wymaga rozszerzenia PostGIS i dodatkowego pakietu EF Core.

### Opcja 2: PostgreSQL bez PostGIS, odległość wzorem haversine

- **Zalety:**
  - [+] Brak rozszerzeń.
- **Wady:**
  - [-] Brak indeksu, każde wyszukiwanie liczy odległość dla wszystkich ofert.

### Opcja 3: MongoDB z indeksem geo

- **Zalety:**
  - [+] Wbudowane zapytania geograficzne.
- **Wady:**
  - [-] Słabsze transakcje między dokumentami, mniej naturalne modelowanie relacji, brak wsparcia EF Core w tym zakresie.

---

## 3. Decyzja

Wybieramy **PostgreSQL z rozszerzeniem PostGIS**, dostępny przez Entity Framework Core.

---

## 4. Uzasadnienie

PostGIS spełnia wymaganie wyszukiwania po odległości z indeksem, a PostgreSQL zachowuje spójność transakcji, ważną przy rezerwacjach. Wzór haversine nie skaluje się, a MongoDB osłabia gwarancje transakcyjne.

---

## 5. Konsekwencje

### Pozytywne

- [+] Filtry tekstowe, kategorii i odległości w jednym zapytaniu.

### Negatywne

- [-] Testy muszą działać na prawdziwym PostgreSQL z PostGIS (ADR-014).
- [-] Każda zmiana modelu wymaga migracji EF Core.
