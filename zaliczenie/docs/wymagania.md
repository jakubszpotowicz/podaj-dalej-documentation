# Założenia funkcjonalne i niefunkcjonalne

Platforma do wymiany, wypożyczania i oddawania przedmiotów między użytkownikami.

## 1. Wprowadzenie

System to webowa platforma, na której użytkownicy oddają za darmo, wymieniają i wypożyczają przedmioty w swojej okolicy. Celem jest wydłużenie życia rzeczy, które inaczej trafiłyby do śmietnika, oraz ułatwienie sąsiedzkiej wymiany.

### Aktorzy

| Aktor | Opis |
| --- | --- |
| Gość | Niezalogowany odwiedzający; przegląda i wyszukuje oferty, widzi statystyki publiczne. |
| Użytkownik | Zarejestrowane konto; wystawia przedmioty, składa i rozpatruje propozycje, czatuje, ocenia. W danej transakcji występuje jako **właściciel** albo **zainteresowany**. |
| Administrator | Zarządza użytkownikami, kategoriami, zgłoszeniami i ofertami; przegląda logi. |
| System | Procesy w tle: przypomnienia o zwrocie, wygaszanie propozycji, agregacja statystyk. |

**Zakres:** aplikacja webowa (desktop i mobile w przeglądarce). Funkcje poza zakresem opisuje punkt 2.11.

### Stos technologiczny

| Warstwa | Technologia |
| --- | --- |
| Backend | C#, .NET 10 (LTS), ASP.NET Core Web API |
| Frontend | React, TypeScript, Vite |
| Baza danych | PostgreSQL + PostGIS, Entity Framework Core (Npgsql + NetTopologySuite) |
| Uwierzytelnianie | ASP.NET Core Identity, Google OAuth 2.0 / OpenID Connect |
| Czas rzeczywisty | SignalR (transport WebSocket) |
| Zdjęcia | MinIO (API zgodne z S3) |
| Mapa | Leaflet + OpenStreetMap (react-leaflet), geokodowanie Nominatim |
| Testy | xUnit (backend), Vitest (frontend) |
| Wdrożenie | Docker, Docker Compose |
| Dokumentacja API | OpenAPI 3.1 |

## 2. Założenia funkcjonalne

Wymagania podzielono na 10 modułów i opatrzono priorytetem według metody MoSCoW: **M** (Must have, wymagania z treści zadania, zakres MVP), **S** (Should have), **C** (Could have). Funkcje świadomie pominięte (Won't have) zebrano w punkcie 2.11.

### 2.1 Konta i uwierzytelnianie

| ID | Wymaganie | Priorytet |
| --- | --- | --- |
| FR-01 | Rejestracja przez e-mail i hasło z potwierdzeniem adresu linkiem aktywacyjnym. | M |
| FR-02 | Rejestracja i logowanie przez Google OAuth; pierwsze logowanie zakłada konto, kolejne łączy po zweryfikowanym e-mailu. | M |
| FR-03 | Logowanie, wylogowanie, odświeżanie sesji, reset hasła przez e-mail. | M |
| FR-04 | Profil: nazwa wyświetlana, avatar, domyślna lokalizacja, średnia ocen i liczba zakończonych transakcji. | M |
| FR-05 | Konto zablokowane przez administratora nie może się zalogować ani wystawiać ofert. | M |

### 2.2 Przedmioty (oferty)

| ID | Wymaganie | Priorytet |
| --- | --- | --- |
| FR-10 | Użytkownik wystawia przedmiot z polami: nazwa, opis, kategoria, zdjęcia (1–8, JPEG/PNG/WebP, do 10 MB), lokalizacja, stan. | M |
| FR-11 | Stan przedmiotu ze słownika: nowy, jak nowy, dobry, używany, do naprawy. | M |
| FR-12 | Lokalizację wskazuje się pinezką na mapie lub adresem zamienianym na współrzędne; publicznie pokazywana jest przybliżona pozycja (np. z dokładnością ~200 m). | M |
| FR-13 | Właściciel wybiera sposób udostępnienia: **oddam za darmo**, **wymienię**, **wypożyczę** (dokładnie jeden na ofertę). | M |
| FR-14 | Dla wymiany właściciel opisuje, czego oczekuje w zamian (tekst + opcjonalnie kategorie). | M |
| FR-15 | Dla wypożyczenia właściciel definiuje dostępne terminy jako przedziały dat; terminy zajęte przez zaakceptowane wypożyczenia są blokowane. | M |
| FR-16 | Właściciel edytuje, wstrzymuje i usuwa (miękko) swoje oferty. | M |
| FR-17 | Statusy oferty: aktywna, zarezerwowana, wypożyczona, zakończona, wstrzymana, ukryta przez administratora. | M |
| FR-18 | Użytkownik może zgłosić ofertę lub użytkownika jako naruszenie, z powodem i opisem. | M |

### 2.3 Wyszukiwanie i mapa

| ID | Wymaganie | Priorytet |
| --- | --- | --- |
| FR-20 | Wyszukiwanie po nazwie (pełnotekstowe, odporne na wielkość liter i polskie znaki). | M |
| FR-21 | Filtry: kategoria, sposób udostępnienia, stan. | M |
| FR-22 | Filtr odległości: punkt (moja lokalizacja, adres lub kliknięcie na mapie) i promień 1–100 km; wyniki z podaną odległością. | M |
| FR-23 | Sortowanie: najbliższe, najnowsze. Paginacja wyników. | M |
| FR-24 | Widok listy i widok mapy (Leaflet + OSM) z markerami ofert, grupowanymi przy oddaleniu. | M |

### 2.4 Propozycje i transakcje

| ID | Wymaganie | Priorytet |
| --- | --- | --- |
| FR-30 | Zainteresowany wysyła propozycję zgodną z typem oferty: **odbiór** (oddam), **wymiana** (wymienię), **wypożyczenie** (z datą od–do). | M |
| FR-31 | Propozycja wymiany wskazuje jeden z przedmiotów proponującego lub opis tekstowy, plus wiadomość. | M |
| FR-32 | Propozycja wypożyczenia musi mieścić się w dostępnych terminach i nie kolidować z zaakceptowanymi wypożyczeniami. | M |
| FR-33 | Właściciel akceptuje lub odrzuca propozycję (z opcjonalnym powodem); proponujący może ją wycofać do czasu decyzji. | M |
| FR-34 | Akceptacja propozycji odbioru lub wymiany rezerwuje ofertę i automatycznie odrzuca pozostałe oczekujące propozycje do niej. | M |
| FR-35 | Akceptacja wypożyczenia tworzy wypożyczenie z **planowaną datą zwrotu** równą końcowi wnioskowanego okresu. | M |
| FR-36 | Strony potwierdzają przekazanie i (dla wypożyczeń) zwrot; potwierdzenie obu stron kończy transakcję. | M |
| FR-37 | Każda strona może anulować transakcję przed przekazaniem; oferta wraca wtedy do statusu aktywna. | S |

Przebieg stanów propozycji i transakcji pokazuje diagram w [architektura.md](architektura.md).

### 2.5 Czat

| ID | Wymaganie | Priorytet |
| --- | --- | --- |
| FR-40 | Po akceptacji propozycji powstaje rozmowa dostępna tylko dla dwóch stron transakcji. | M |
| FR-41 | Wiadomości dostarczane w czasie rzeczywistym (SignalR/WebSocket), zapisywane w bazie, z historią ładowaną stronicowo. | M |
| FR-42 | Wskaźnik nieprzeczytanych wiadomości i znacznik przeczytania. | S |
| FR-43 | Wskaźnik „pisze…” i status online. | C |

### 2.6 Przypomnienia i powiadomienia

| ID | Wymaganie | Priorytet |
| --- | --- | --- |
| FR-50 | System przypomina obu stronom o zwrocie 2 dni i 1 dzień przed planowaną datą, oraz w dniu zwrotu. | M |
| FR-51 | Po przekroczeniu terminu wypożyczenie dostaje status „po terminie”, a strony codzienne przypomnienie. | M |
| FR-52 | Powiadomienia w aplikacji (na żywo przez SignalR) i e-mailem: nowa propozycja, decyzja, nowa wiadomość, przypomnienie o zwrocie. | M |

### 2.7 Oceny

| ID | Wymaganie | Priorytet |
| --- | --- | --- |
| FR-60 | Po zakończeniu transakcji każda strona może raz ocenić drugą: 1–5 gwiazdek + komentarz do 500 znaków. | M |
| FR-61 | Ocenę można wystawić do 14 dni po zakończeniu; oceny są widoczne w profilu. | M |

### 2.8 Historia

| ID | Wymaganie | Priorytet |
| --- | --- | --- |
| FR-70 | Użytkownik widzi historię w trzech zakładkach: **oddane** (i odebrane), **wymiany**, **wypożyczenia** (jako właściciel i jako pożyczający). | M |
| FR-71 | Każdy wpis: przedmiot, druga strona, daty, status, wystawione i otrzymane oceny. | M |

### 2.9 Statystyki

| ID | Wymaganie | Priorytet |
| --- | --- | --- |
| FR-80 | Publiczny panel statystyk: najpopularniejsze kategorie (wg ofert i transakcji), liczba przedmiotów oddanych zamiast wyrzuconych, liczba zakończonych wymian i wypożyczeń. | M |
| FR-81 | Statystyki z wyborem okresu (30 dni, rok, całość) i wykresami. | S |
| FR-82 | Osobiste statystyki użytkownika w profilu. | S |

### 2.10 Administracja

| ID | Wymaganie | Priorytet |
| --- | --- | --- |
| FR-90 | Administrator przegląda, wyszukuje, blokuje i odblokowuje użytkowników oraz nadaje rolę administratora. | M |
| FR-91 | Administrator zarządza drzewem kategorii (dodaj, zmień nazwę, dezaktywuj; bez twardego usuwania używanych kategorii). | M |
| FR-92 | Administrator obsługuje kolejkę zgłoszeń naruszeń: przegląd, decyzja (odrzuć, ukryj ofertę, zablokuj użytkownika), notatka. | M |
| FR-93 | Administrator ukrywa i przywraca niewłaściwe oferty; ukryta oferta znika z wyszukiwania, a właściciel widzi powód. | M |
| FR-94 | Administrator przegląda logi aplikacji z filtrowaniem po poziomie, czasie, użytkowniku i identyfikatorze żądania. | M |
| FR-95 | Każda akcja administratora trafia do dziennika audytu (kto, co, kiedy). | M |

### 2.11 Poza zakresem (Won't have)

- Płatności, kaucje i opłaty za wypożyczenie.
- Natywne aplikacje mobilne i powiadomienia push poza przeglądarką.
- Organizacja dostawy kurierem.
- Czat przed akceptacją propozycji oraz rozmowy grupowe.
- Interfejs w innych językach niż polski.

## 3. Założenia niefunkcjonalne

Wartości liczbowe dobrano do skali projektu zaliczeniowego (do ~1000 użytkowników, ~10 000 ofert) uruchamianego lokalnie przez Docker Compose.

| ID | Obszar | Wymaganie |
| --- | --- | --- |
| NFR-01 | Architektura | Backend i frontend to osobne aplikacje i kontenery; komunikują się wyłącznie przez REST API (JSON) oraz hub SignalR. |
| NFR-02 | API | API wersjonowane prefiksem `/api/v1`; błędy w formacie ProblemDetails (RFC 9457); paginacja `page`/`pageSize` z polem `totalCount`. |
| NFR-03 | Dokumentacja | Pełna specyfikacja OpenAPI 3.1 generowana z kodu, dostępna pod `/openapi/v1.json` i w interaktywnym UI; każdy endpoint ma opis, przykłady i kody odpowiedzi. |
| NFR-04 | Bezpieczeństwo — hasła | Hasła hashowane przez ASP.NET Core Identity (PBKDF2); min. 8 znaków; blokada konta po 5 nieudanych logowaniach na 15 min. |
| NFR-05 | Bezpieczeństwo — sesja | Krótkotrwały token dostępu (JWT, 15 min) i token odświeżania w ciasteczku `HttpOnly`, `Secure`, `SameSite=Strict`, z rotacją. |
| NFR-06 | Bezpieczeństwo — autoryzacja | Autoryzacja oparta na rolach (User, Admin) i politykach właściciela zasobu; dostęp do czatu tylko dla stron transakcji, sprawdzany w hubie. |
| NFR-07 | Bezpieczeństwo — wejście | Walidacja wszystkich danych wejściowych (FluentValidation lub DataAnnotations); sprawdzanie typu MIME i rozmiaru zdjęć; usuwanie metadanych EXIF z lokalizacją. |
| NFR-08 | Bezpieczeństwo — ogólne | HTTPS w środowisku docelowym, CORS tylko dla adresu frontendu, rate limiting na logowaniu, rejestracji i wysyłce wiadomości; sekrety w zmiennych środowiskowych, nie w repozytorium. |
| NFR-09 | Prywatność | Dokładny adres widzi tylko druga strona zaakceptowanej transakcji; publicznie pozycja zaokrąglona. Użytkownik może usunąć konto (anonimizacja danych w historii). |
| NFR-10 | Wydajność | Wyszukiwanie z filtrem odległości i paginacją odpowiada w p95 < 500 ms przy 10 000 ofert; wiadomość czatu dociera do odbiorcy w < 1 s. |
| NFR-11 | Zdjęcia | Zdjęcia w MinIO; serwer generuje miniaturę (np. 400 px) przy uploadzie; przeglądarka pobiera je przez podpisane URL-e. |
| NFR-12 | Dostępność | Frontend responsywny od 360 px szerokości; zgodność z WCAG 2.1 AA w zakresie kontrastu, etykiet formularzy i obsługi klawiaturą. |
| NFR-13 | Lokalizacja | Interfejs w języku polskim; daty w strefie Europe/Warsaw na froncie, w UTC w bazie i API. |
| NFR-14 | Niezawodność | Czat automatycznie wznawia połączenie po zerwaniu; wiadomości wysłane offline nie giną (ponowna próba lub komunikat błędu). Zadania w tle są idempotentne. |
| NFR-15 | Logowanie i obserwowalność | Logi strukturalne (Serilog) z identyfikatorem korelacji żądania; zapisywane do konsoli i bazy, z retencją 30 dni; endpointy `/health` dla kontenerów. |
| NFR-16 | Utrzymywalność | Kod backendu w warstwach (Api, Application, Domain, Infrastructure); migracje EF Core w repozytorium; spójny styl (`dotnet format`, ESLint + Prettier). |
| NFR-17 | Testy | Testy jednostkowe logiki domenowej i serwisów (xUnit), testy integracyjne API na prawdziwym PostgreSQL w kontenerze (WebApplicationFactory + Testcontainers), testy komponentów i hooków (Vitest + Testing Library). Cel: ≥ 70% pokrycia warstwy Application. |
| NFR-18 | Wdrożenie | Całość startuje jednym `docker compose up`: api, web, postgres (PostGIS), minio, mailpit; dane przykładowe (seed) i konto administratora tworzone przy pierwszym starcie. |
| NFR-19 | CI | Pipeline (np. GitHub Actions) buduje oba projekty, uruchamia testy i lintery przy każdym pull requeście. |
| NFR-20 | Zgodność z OSM | Mapa wyświetla atrybucję OpenStreetMap; geokodowanie respektuje limit Nominatim (1 żądanie/s) przez cache i opóźnianie zapytań. |
| NFR-21 | Skalowalność | API jest bezstanowe (sesja w JWT, dane w bazie), więc da się uruchomić kilka instancji za load balancerem. Wymagałoby to backplane'u SignalR (Redis) i blokady zadań w tle; w projekcie działa jedna instancja. |

Decyzje stojące za tymi wymaganiami opisują [ADR-y](adr/README.md).
