# ADR-002: Użycie ASP.NET Core Web API jako backendu

- **Status:** Zaakceptowana
- **Data:** 2026-10-28
- **Autorzy:** Michał Olszowy, Jakub Szpotowicz, Nikola Pisarska

---

## 1. Kontekst

Backend musi udostępniać REST API z dokumentacją OpenAPI, obsługiwać rejestrację z OAuth, WebSocket i zadania w tle. Zespół zna C# z wcześniejszych zajęć.

---

## 2. Rozważane opcje

### Opcja 1: ASP.NET Core Web API (.NET 10 LTS, C#)

- **Zalety:**
  - [+] Wbudowane Identity, obsługa Google OAuth, SignalR, generator OpenAPI i BackgroundService.
  - [+] Silne typowanie, EF Core, znajomość w zespole.
- **Wady:**
  - [-] Więcej kodu konfiguracyjnego niż w lżejszych frameworkach.

### Opcja 2: Node.js (NestJS, TypeScript)

- **Zalety:**
  - [+] Ten sam język co frontend.
- **Wady:**
  - [-] Uwierzytelnianie, WebSocket i ORM z osobnych bibliotek; mniejsze doświadczenie zespołu.

### Opcja 3: Java (Spring Boot)

- **Zalety:**
  - [+] Dojrzały ekosystem.
- **Wady:**
  - [-] Zespół nie zna Javy; dłuższy start.

---

## 3. Decyzja

Wybieramy **ASP.NET Core Web API na .NET 10 (LTS)**.

---

## 4. Uzasadnienie

Wszystkie wymagane mechanizmy (konta, OAuth, WebSocket, OpenAPI, zadania w tle) są częścią frameworka, co ogranicza liczbę zależności. Wersja LTS ma wsparcie dłuższe niż czas trwania projektu.

---

## 5. Konsekwencje

### Pozytywne

- [+] Spójny stos i jedno źródło dokumentacji (Microsoft Learn).

### Negatywne

- [-] Frontend i backend w różnych językach; kontrakt trzymamy w OpenAPI (ADR-013).
