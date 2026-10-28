# ADR-013: Generowanie dokumentacji OpenAPI z kodu

- **Status:** Zaakceptowana
- **Data:** 2026-10-28
- **Autorzy:** Michał Olszowy, Jakub Szpotowicz, Nikola Pisarska

---

## 1. Kontekst

Backend musi być udokumentowany w OpenAPI, a dokumentacja końcowa projektu ma zawierać wyrenderowaną specyfikację. Frontend w TypeScript potrzebuje typów zgodnych z API.

---

## 2. Rozważane opcje

### Opcja 1: Wbudowany `Microsoft.AspNetCore.OpenApi` + Scalar jako interfejs, klient TS generowany ze specyfikacji

- **Zalety:**
  - [+] Specyfikacja zawsze zgodna z kodem; brak zewnętrznej biblioteki do generowania.
- **Wady:**
  - [-] Opisy endpointów trzeba utrzymywać w atrybutach i komentarzach.

### Opcja 2: Swashbuckle (Swagger UI)

- **Zalety:**
  - [+] Popularny, dużo przykładów.
- **Wady:**
  - [-] Usunięty z szablonów .NET od wersji 9; dodatkowa zależność.

### Opcja 3: Ręcznie pisany plik OpenAPI (design-first)

- **Zalety:**
  - [+] Kontrakt ustalony przed kodem.
- **Wady:**
  - [-] Ryzyko rozjazdu między specyfikacją a implementacją.

---

## 3. Decyzja

Wybieramy **wbudowany generator OpenAPI** z interfejsem Scalar; frontend generuje typy i klienta ze specyfikacji.

---

## 4. Uzasadnienie

Generowanie z kodu eliminuje rozjazd dokumentacji, a typy na froncie zamieniają błędy kontraktu w błędy kompilacji.

---

## 5. Konsekwencje

### Pozytywne

- [+] Plik `openapi.json` można bezpośrednio wyrenderować w dokumentacji końcowej.

### Negatywne

- [-] Krok generowania typów musi być w CI.
