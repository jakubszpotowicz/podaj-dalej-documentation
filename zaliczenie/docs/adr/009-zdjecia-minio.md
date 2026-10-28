# ADR-009: Przechowywanie zdjęć w MinIO

- **Status:** Zaakceptowana
- **Data:** 2026-10-28
- **Autorzy:** Michał Olszowy, Jakub Szpotowicz, Nikola Pisarska

---

## 1. Kontekst

Zdjęcia przedmiotów muszą być w zewnętrznym lub emulowanym object storage. Oferty ukryte przez administratora nie powinny dalej udostępniać zdjęć.

---

## 2. Rozważane opcje

### Opcja 1: MinIO (API zgodne z S3) w kontenerze, odczyt przez podpisane URL-e

- **Zalety:**
  - [+] Emuluje S3 lokalnie; kod działa też z AWS S3 po zmianie konfiguracji.
  - [+] Kontrola dostępu do zdjęć.
- **Wady:**
  - [-] Podpisane URL-e wymagają publicznego adresu MinIO w konfiguracji API.

### Opcja 2: Zdjęcia w bazie danych (bytea) lub na dysku kontenera API

- **Zalety:**
  - [+] Brak dodatkowej usługi.
- **Wady:**
  - [-] Nie spełnia wymagania object storage; rośnie baza i kopie zapasowe.

### Opcja 3: Chmurowe S3 / Azure Blob

- **Zalety:**
  - [+] Prawdziwa usługa produkcyjna.
- **Wady:**
  - [-] Wymaga konta i kosztów; projekt nie uruchomi się offline u prowadzącego.

---

## 3. Decyzja

Wybieramy **MinIO**. Upload idzie przez API (walidacja typu i rozmiaru, usunięcie EXIF, miniatura), a przeglądarka pobiera zdjęcia przez podpisane URL-e.

---

## 4. Uzasadnienie

MinIO spełnia wymaganie i działa w Docker Compose bez kont zewnętrznych. Upload przez API pozwala kontrolować pliki i usuwać dane lokalizacji ze zdjęć.

---

## 5. Konsekwencje

### Pozytywne

- [+] Łatwa migracja do AWS S3.

### Negatywne

- [-] Upload obciąża API; przy limicie 10 MB na zdjęcie jest to akceptowalne.
