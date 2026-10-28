# ADR-001: Użycie modularnego monolitu z oddzielną aplikacją SPA

- **Status:** Zaakceptowana
- **Data:** 2026-10-28
- **Autorzy:** Michał Olszowy, Jakub Szpotowicz, Nikola Pisarska

---

## 1. Kontekst

Zadanie wymaga odseparowanego backendu i frontendu komunikujących się przez REST API. Zespół jest kilkuosobowy, projekt trwa jeden semestr, a system ma kilkanaście powiązanych modułów (oferty, propozycje, czat, oceny).

---

## 2. Rozważane opcje

### Opcja 1: Modularny monolit (jedno API z modułami) + osobna aplikacja SPA

- **Zalety:**
  - [+] Jedno wdrożenie i jedna baza, proste transakcje między modułami.
  - [+] Mały narzut konfiguracyjny, szybki start.
- **Wady:**
  - [-] Skalowanie tylko w całości.

### Opcja 2: Mikroserwisy (osobne usługi dla ofert, czatu, użytkowników) + SPA

- **Zalety:**
  - [+] Niezależne wdrażanie i skalowanie usług.
- **Wady:**
  - [-] Transakcje rozproszone, komunikacja między usługami, wiele kontenerów i baz.
  - [-] Nakład pracy nieproporcjonalny do skali projektu.

### Opcja 3: Aplikacja renderowana po stronie serwera (MVC/Razor Pages)

- **Zalety:**
  - [+] Jeden projekt, brak osobnego frontendu.
- **Wady:**
  - [-] Nie spełnia wymogu odseparowania frontendu i backendu.

---

## 3. Decyzja

Wybieramy **modularny monolit** w ASP.NET Core (warstwy Api, Application, Domain, Infrastructure) oraz **oddzielną aplikację SPA** w osobnym kontenerze.

---

## 4. Uzasadnienie

Opcja 3 odpada przez wymaganie zadania. Mikroserwisy rozwiązują problemy skali, których projekt nie ma, a kosztują najwięcej pracy. Monolit z wyraźnymi modułami spełnia wymaganie separacji i pozwala skupić czas zespołu na funkcjach.

---

## 5. Konsekwencje

### Pozytywne

- [+] Jedno repozytorium backendu, łatwe debugowanie i testy integracyjne.
- [+] Granice modułów umożliwiają późniejsze wydzielenie usługi.

### Negatywne

- [-] Wymagana dyscyplina, by moduły nie sięgały do swoich tabel bezpośrednio.
