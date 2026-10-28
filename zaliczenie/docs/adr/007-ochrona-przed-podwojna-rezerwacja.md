# ADR-007: Ochrona przed podwójną rezerwacją na poziomie bazy

- **Status:** Zaakceptowana
- **Data:** 2026-10-28
- **Autorzy:** Michał Olszowy, Jakub Szpotowicz, Nikola Pisarska

---

## 1. Kontekst

Właściciel może zaakceptować dwie propozycje do tej samej oferty niemal jednocześnie, a terminy wypożyczeń jednego przedmiotu nie mogą się nakładać.

---

## 2. Rozważane opcje

### Opcja 1: Ograniczenia w bazie: blokada optymistyczna na ofercie + ograniczenie wykluczające na przedziałach dat wypożyczeń

- **Zalety:**
  - [+] Gwarancja spójności niezależna od błędów w kodzie i liczby instancji API.
- **Wady:**
  - [-] Ograniczenie wykluczające trzeba dodać ręcznie w migracji.

### Opcja 2: Sprawdzanie kolizji tylko w kodzie aplikacji

- **Zalety:**
  - [+] Prostsze w implementacji.
- **Wady:**
  - [-] Wyścig dwóch równoczesnych żądań może utworzyć kolizję.

---

## 3. Decyzja

Wybieramy **ograniczenia w bazie danych**: token współbieżności na ofercie i ograniczenie `EXCLUDE` na przedziałach dat aktywnych wypożyczeń. Akceptacja propozycji i zmiana statusu oferty wykonują się w jednej transakcji.

---

## 4. Uzasadnienie

Rezerwacje to rdzeń domeny; błąd tu oznacza dwie osoby umówione po ten sam przedmiot. Sprawdzenie w kodzie zostaje dla czytelnych komunikatów, ale ostatnie słowo ma baza.

---

## 5. Konsekwencje

### Pozytywne

- [+] Konflikt zwraca czytelny błąd HTTP 409.

### Negatywne

- [-] Logika jest częściowo w SQL, co trzeba opisać w dokumentacji technicznej.
