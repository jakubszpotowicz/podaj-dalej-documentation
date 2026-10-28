# ADR-010: Użycie Leaflet i OpenStreetMap jako mapy

- **Status:** Zaakceptowana
- **Data:** 2026-10-28
- **Autorzy:** Michał Olszowy, Jakub Szpotowicz, Nikola Pisarska

---

## 1. Kontekst

System ma korzystać z mapy: użytkownik wskazuje lokalizację oferty i punkt wyszukiwania, a wyniki widzi na mapie.

---

## 2. Rozważane opcje

### Opcja 1: Leaflet + kafelki OpenStreetMap, geokodowanie Nominatim przez backend

- **Zalety:**
  - [+] Darmowe, bez klucza API.
- **Wady:**
  - [-] Publiczne serwery OSM mają limity i nie są przeznaczone do ruchu produkcyjnego.

### Opcja 2: Google Maps

- **Zalety:**
  - [+] Najlepsze geokodowanie polskich adresów.
- **Wady:**
  - [-] Wymaga klucza i konta rozliczeniowego.

### Opcja 3: Mapbox

- **Zalety:**
  - [+] Ładne style map.
- **Wady:**
  - [-] Wymaga klucza; darmowy limit.

---

## 3. Decyzja

Wybieramy **Leaflet z OpenStreetMap**; geokodowanie Nominatim wywołuje backend z cache i limitem 1 żądania na sekundę.

---

## 4. Uzasadnienie

Rozwiązanie spełnia wymaganie bez kosztów i kluczy. Proxy w backendzie pilnuje zasad korzystania z Nominatim.

---

## 5. Konsekwencje

### Pozytywne

- [+] Projekt działa od razu po sklonowaniu.

### Negatywne

- [-] Przy realnym wdrożeniu trzeba zmienić dostawcę kafelków i geokodowania (nowy ADR).
