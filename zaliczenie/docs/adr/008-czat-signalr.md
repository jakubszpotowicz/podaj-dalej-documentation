# ADR-008: Użycie SignalR do czatu i powiadomień

- **Status:** Zaakceptowana
- **Data:** 2026-10-28
- **Autorzy:** Michał Olszowy, Jakub Szpotowicz, Nikola Pisarska

---

## 1. Kontekst

Czat musi działać w czasie rzeczywistym z wykorzystaniem WebSocket. Ten sam kanał przyda się do powiadomień o propozycjach i przypomnieniach.

---

## 2. Rozważane opcje

### Opcja 1: SignalR z wymuszonym transportem WebSocket

- **Zalety:**
  - [+] Grupy, automatyczne ponawianie połączenia, uwierzytelnianie i klient dla przeglądarki w zestawie.
- **Wady:**
  - [-] Własny protokół nad WebSocketem, potrzebny klient `@microsoft/signalr`.

### Opcja 2: Surowe WebSockety (`System.Net.WebSockets`)

- **Zalety:**
  - [+] Pełna kontrola, brak dodatkowego protokołu.
- **Wady:**
  - [-] Ręczna obsługa grup, serializacji, ponownych połączeń i uwierzytelniania.

### Opcja 3: Long polling / Server-Sent Events

- **Zalety:**
  - [+] Proste.
- **Wady:**
  - [-] Nie spełnia wymagania użycia WebSocket.

---

## 3. Decyzja

Wybieramy **SignalR z jednym hubem dla czatu i powiadomień**, z transportem ograniczonym do WebSocket.

---

## 4. Uzasadnienie

SignalR korzysta z WebSocket, więc spełnia wymaganie, a gotowe grupy i ponawianie oszczędzają kilka dni pracy. Ograniczenie transportu do WebSocket czyni to jawnym.

---

## 5. Konsekwencje

### Pozytywne

- [+] Jeden kanał na żywo dla całej aplikacji.

### Negatywne

- [-] Przy kilku instancjach API potrzebny byłby backplane (Redis).
