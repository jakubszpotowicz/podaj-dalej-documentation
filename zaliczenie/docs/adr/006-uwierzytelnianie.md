# ADR-006: Uwierzytelnianie przez Identity, JWT i Google OAuth

- **Status:** Zaakceptowana
- **Data:** 2026-10-28
- **Autorzy:** Michał Olszowy, Jakub Szpotowicz, Nikola Pisarska

---

## 1. Kontekst

Wymagana rejestracja e-mailem oraz przez zewnętrznego dostawcę OAuth. SPA działa pod innym adresem niż API, a połączenie WebSocket czatu też musi być uwierzytelnione.

---

## 2. Rozważane opcje

### Opcja 1: ASP.NET Core Identity + JWT (krótki token dostępu) + token odświeżania w ciasteczku HttpOnly; Google OAuth obsługiwany przez backend

- **Zalety:**
  - [+] Jeden mechanizm dla REST i SignalR.
  - [+] Token nie trafia do localStorage, co ogranicza skutki XSS.
- **Wady:**
  - [-] Wydawanie i rotację tokenów odświeżania trzeba napisać samodzielnie.

### Opcja 2: Identity z ciasteczkami sesyjnymi

- **Zalety:**
  - [+] Najmniej kodu, gotowe w frameworku.
- **Wady:**
  - [-] Wymaga ochrony CSRF i konfiguracji ciasteczek między originami.

### Opcja 3: Zewnętrzny serwer tożsamości (Keycloak)

- **Zalety:**
  - [+] Gotowe logowanie, rejestracja i dostawcy zewnętrzni.
- **Wady:**
  - [-] Dodatkowy kontener i konfiguracja, część zadania „przeniesiona” poza aplikację.

---

## 3. Decyzja

Wybieramy **ASP.NET Core Identity z JWT i tokenem odświeżania**; logowanie Google realizuje backend przepływem Authorization Code.

---

## 4. Uzasadnienie

Rozwiązanie spełnia oba wymagane sposoby rejestracji w jednym modelu użytkownika i działa tak samo dla REST i WebSocket. Identity zapewnia bezpieczne hashowanie haseł i blokadę konta.

---

## 5. Konsekwencje

### Pozytywne

- [+] Role (User, Admin) i blokowanie kont w jednym miejscu.

### Negatywne

- [-] Logowanie przez Google wymaga zarejestrowanej aplikacji OAuth i adresu zwrotnego na każdym środowisku.
