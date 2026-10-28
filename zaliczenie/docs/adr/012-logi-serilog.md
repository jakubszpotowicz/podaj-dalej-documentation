# ADR-012: Zapis logów aplikacji w PostgreSQL przez Serilog

- **Status:** Zaakceptowana
- **Data:** 2026-10-28
- **Autorzy:** Michał Olszowy, Jakub Szpotowicz, Nikola Pisarska

---

## 1. Kontekst

Administrator ma widzieć logi aplikacji w panelu. Logi powinny być strukturalne, by dało się je filtrować po użytkowniku, poziomie i żądaniu.

---

## 2. Rozważane opcje

### Opcja 1: Serilog z zapisem do konsoli i tabeli w PostgreSQL

- **Zalety:**
  - [+] Logi dostępne w panelu przez zwykły endpoint API.
- **Wady:**
  - [-] Dodatkowe zapisy w bazie.

### Opcja 2: Serilog + Seq

- **Zalety:**
  - [+] Wygodne przeglądanie i wyszukiwanie logów.
- **Wady:**
  - [-] Osobny kontener i osobny interfejs, poza panelem aplikacji.

### Opcja 3: Stos ELK lub Grafana Loki

- **Zalety:**
  - [+] Rozwiązanie produkcyjne.
- **Wady:**
  - [-] Kilka kontenerów, duża konfiguracja.

---

## 3. Decyzja

Wybieramy **Serilog z zapisem do konsoli i tabeli w PostgreSQL**, z retencją 30 dni. Akcje administratora trafiają osobno do dziennika audytu.

---

## 4. Uzasadnienie

Wymaganie mówi o logach widocznych dla administratora w aplikacji; zapis do bazy pozwala to zrealizować bez nowej infrastruktury.

---

## 5. Konsekwencje

### Pozytywne

- [+] Logi i dane w jednym miejscu, łatwe filtrowanie SQL.

### Negatywne

- [-] Baza obciążona zapisem logów; dlatego zapisujemy poziom Information i wyżej.
