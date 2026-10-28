# ADR-011: Realizacja przypomnień jako BackgroundService

- **Status:** Zaakceptowana
- **Data:** 2026-10-28
- **Autorzy:** Michał Olszowy, Jakub Szpotowicz, Nikola Pisarska

---

## 1. Kontekst

System ma przypominać o zbliżającym się terminie zwrotu i oznaczać wypożyczenia po terminie. Przypomnienie nie może zostać wysłane dwa razy.

---

## 2. Rozważane opcje

### Opcja 1: Wbudowany `BackgroundService` uruchamiany cyklicznie

- **Zalety:**
  - [+] Brak dodatkowych zależności.
- **Wady:**
  - [-] Brak panelu zadań i gotowych ponowień.

### Opcja 2: Hangfire

- **Zalety:**
  - [+] Panel, harmonogram, ponawianie zadań.
- **Wady:**
  - [-] Dodatkowa biblioteka i tabele; nadmiarowe przy jednym typie zadania.

### Opcja 3: Quartz.NET

- **Zalety:**
  - [+] Rozbudowany harmonogram (cron).
- **Wady:**
  - [-] Więcej konfiguracji niż potrzeba.

---

## 3. Decyzja

Wybieramy **`BackgroundService`** uruchamiany co 15 minut, z zapisem wysłanych przypomnień w tabeli z unikalnym kluczem (wypożyczenie + typ przypomnienia).

---

## 4. Uzasadnienie

Jedno proste zadanie cykliczne nie uzasadnia nowej biblioteki. Unikalny klucz chroni przed podwójnym wysłaniem nawet po restarcie.

---

## 5. Konsekwencje

### Pozytywne

- [+] Mniej zależności do utrzymania.

### Negatywne

- [-] Przy rozbudowie o kolejne zadania warto wrócić do Hangfire (nowy ADR).
