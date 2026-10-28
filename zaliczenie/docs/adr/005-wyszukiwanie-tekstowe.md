# ADR-005: Wyszukiwanie tekstowe przez pg_trgm i unaccent

- **Status:** Zaakceptowana
- **Data:** 2026-10-28
- **Autorzy:** Michał Olszowy, Jakub Szpotowicz, Nikola Pisarska

---

## 1. Kontekst

Wyszukiwanie po nazwie ma tolerować wielkość liter, brak polskich znaków („lodowka” i „lodówka”) oraz drobne literówki. PostgreSQL nie ma wbudowanego słownika języka polskiego do wyszukiwania pełnotekstowego.

---

## 2. Rozważane opcje

### Opcja 1: Rozszerzenia pg_trgm i unaccent z indeksem trigramowym

- **Zalety:**
  - [+] Dopasowanie częściowe i odporność na literówki dla krótkich nazw.
- **Wady:**
  - [-] Brak odmiany słów przez przypadki.

### Opcja 2: Pełnotekstowe tsvector z konfiguracją simple

- **Zalety:**
  - [+] Ranking trafności.
- **Wady:**
  - [-] Gorsze dopasowanie fragmentów słów, brak tolerancji literówek.

### Opcja 3: Zewnętrzny silnik (Elasticsearch, Meilisearch)

- **Zalety:**
  - [+] Najlepsza jakość wyszukiwania.
- **Wady:**
  - [-] Dodatkowy kontener i synchronizacja danych z bazą.

---

## 3. Decyzja

Wybieramy **pg_trgm z unaccent** i indeksem GIN na znormalizowanej nazwie.

---

## 4. Uzasadnienie

Nazwy przedmiotów są krótkie, więc dopasowanie trigramowe daje dobre wyniki bez nowego systemu do utrzymania.

---

## 5. Konsekwencje

### Pozytywne

- [+] Brak nowych usług w środowisku.

### Negatywne

- [-] Opis przedmiotu przeszukiwany tylko pomocniczo; w razie potrzeby można później dodać silnik zewnętrzny nowym ADR-em.
