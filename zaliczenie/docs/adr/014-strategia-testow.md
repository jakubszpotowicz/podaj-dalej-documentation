# ADR-014: Testy na prawdziwej bazie w kontenerach

- **Status:** Zaakceptowana
- **Data:** 2026-10-28
- **Autorzy:** Michał Olszowy, Jakub Szpotowicz, Nikola Pisarska

---

## 1. Kontekst

Wymagane testy automatyczne w xUnit i Vitest. Kluczowa logika (wyszukiwanie po odległości, kolizje rezerwacji) zależy od funkcji PostgreSQL i PostGIS.

---

## 2. Rozważane opcje

### Opcja 1: xUnit + Testcontainers (PostgreSQL z PostGIS, MinIO) dla testów integracyjnych; Vitest + Testing Library na froncie

- **Zalety:**
  - [+] Testy sprawdzają prawdziwe zapytania przestrzenne i ograniczenia bazy.
- **Wady:**
  - [-] Wymagają Dockera lokalnie i w CI; wolniejsze.

### Opcja 2: xUnit z EF Core InMemory lub SQLite

- **Zalety:**
  - [+] Szybkie, bez Dockera.
- **Wady:**
  - [-] Nie obsługują PostGIS ani ograniczeń wykluczających, więc nie testują kluczowej logiki.

---

## 3. Decyzja

Wybieramy **testy jednostkowe w xUnit dla logiki domenowej, testy integracyjne API w xUnit z Testcontainers** oraz **Vitest z Testing Library i MSW** dla frontendu. Testy uruchamia CI przy każdym pull requeście.

---

## 4. Uzasadnienie

Testy na bazie innej niż produkcyjna dawałyby fałszywe poczucie bezpieczeństwa dokładnie w miejscach najbardziej narażonych na błędy.

---

## 5. Konsekwencje

### Pozytywne

- [+] Wysoka wiarygodność testów integracyjnych.

### Negatywne

- [-] Dłuższy czas pipeline'u CI.
