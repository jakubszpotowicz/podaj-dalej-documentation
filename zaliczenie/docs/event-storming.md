# Event Storming — Podaj Dalej

**Projekt:** Platforma do wymiany, wypożyczania i oddawania przedmiotów między użytkownikami  
**Zespół projektowy:** Jakub Szpotowicz, Michał Olszowy, Nikola Pisarska

## 1. Cel i zakres

Model domeny obejmuje procesy biznesowe platformy: rejestrację użytkownika, publikację i wyszukiwanie ofert, kontakt między użytkownikami, składanie i rozpatrywanie propozycji, oddawanie, wymianę i wypożyczanie przedmiotów, zwroty, oceny, powiadomienia, historię, statystyki oraz administrację.

Model przedstawia zdarzenia (Events), komendy (Commands), agregaty (Aggregates) i polityki (Policies), a także aktorów i widoki uczestniczące w poszczególnych procesach.

### Aktorzy

- **Gość** — przegląda i wyszukuje publiczne oferty oraz widzi publiczne statystyki.
- **Użytkownik** — zarejestrowana osoba korzystająca z platformy. W konkretnej transakcji występuje jako właściciel oferty albo osoba zainteresowana.
- **Właściciel** — użytkownik, który wystawił daną ofertę.
- **Zainteresowany** — użytkownik, który składa propozycję dotyczącą oferty innej osoby.
- **Wypożyczający** — użytkownik, który wypożycza przedmiot od właściciela.
- **Administrator** — zarządza użytkownikami, kategoriami, zgłoszeniami i ofertami oraz przegląda logi.
- **System** — wykonuje zadania automatyczne, m.in. przypomnienia o zwrocie i generowanie powiadomień.

Właściciel, zainteresowany i wypożyczający nie są odrębnymi typami kont — są rolami użytkownika w ramach konkretnej oferty lub transakcji.

### Legenda

| Element | Kolor w szablonie | Zasada zapisu |
|---|---|---|
| **Zdarzenie (Event)** | Pomarańczowy | Fakt, który już się wydarzył; czas przeszły. |
| **Komenda (Command)** | Niebieski | Polecenie/intencja wykonania działania; tryb rozkazujący. |
| **Agregat (Aggregate)** | Żółty | Obiekt lub spójna grupa obiektów, której stan jest zmieniany. |
| **Polityka (Policy)** | Fioletowy | Reguła reagująca na zdarzenie: „jeśli X, wykonaj Y”. |
| Widok (View) | Zielony | Dane prezentowane użytkownikowi lub administratorowi. |
| Pytanie (Question) | Różowy | Kwestia, której wymagania nie rozstrzygają jednoznacznie. |
| System zewnętrzny | Czerwony (opcjonalnie) | Usługa poza aplikacją, np. Google OpenID Connect lub dostawca poczty. |

## 2. Zdarzenia (Events)

Zdarzenia poniżej opisują fakty biznesowe w kolejności procesu. Każde zdarzenie zapisano w czasie przeszłym. Wyszukiwanie i wyświetlanie danych są operacjami odczytu, dlatego nie tworzą osobnych zdarzeń domenowych.

### 2.1. Konto i uwierzytelnianie

| ID | Zdarzenie | Opis / warunek |
|---|---|---|
| E01 | Konto użytkownika zostało utworzone | Użytkownik zarejestrował się przy użyciu adresu e-mail i hasła. Konto oczekuje na potwierdzenie adresu. |
| E02 | Wiadomość aktywacyjna została przekazana do usługi pocztowej | System przekazał wiadomość z linkiem aktywacyjnym do skonfigurowanego dostawcy poczty. |
| E03 | Adres e-mail został potwierdzony | Użytkownik użył prawidłowego linku aktywacyjnego. |
| E04 | Użytkownik został uwierzytelniony | Użytkownik zalogował się poprawnie za pomocą e-maila i hasła. |
| E05 | Konto zostało utworzone lub powiązane z Google | Pierwsze logowanie przez Google utworzyło konto, a kolejne powiązało dostawcę z istniejącym kontem po zweryfikowanym adresie e-mail. |
| E06 | Żądanie resetu hasła zostało zarejestrowane | Użytkownik poprosił o reset hasła. |
| E07 | Wiadomość resetująca hasło została przekazana do usługi pocztowej | System przekazał wiadomość z jednorazowym, ograniczonym czasowo linkiem. |
| E08 | Hasło użytkownika zostało zmienione | Użytkownik ustawił nowe hasło przy użyciu prawidłowego linku. |
| E09 | Użytkownik został wylogowany | Sesja użytkownika została zakończona. |

### 2.2. Wystawianie i zarządzanie ofertą

| ID | Zdarzenie | Opis / warunek |
|---|---|---|
| E10 | Zdjęcie oferty zostało zapisane | Plik zdjęcia został zapisany w object storage. |
| E11 | Oferta została opublikowana | Oferta zawiera wymagane dane: nazwę, opis, kategorię, zdjęcia, lokalizację, stan i jeden sposób udostępnienia. |
| E12 | Dane oferty zostały zmienione | Właściciel edytował swoją ofertę. |
| E13 | Oferta została wstrzymana przez właściciela | Właściciel czasowo wyłączył ofertę z dostępności. |
| E14 | Oferta została ponownie aktywowana przez właściciela | Właściciel wznowił wcześniej wstrzymaną ofertę. |
| E15 | Oferta została miękko usunięta przez właściciela | Oferta została oznaczona jako usunięta bez fizycznego usuwania historii transakcji. |

### 2.3. Propozycje i decyzja właściciela

| ID | Zdarzenie | Opis / warunek |
|---|---|---|
| E16 | Propozycja odbioru została złożona | Zainteresowany wysłał propozycję dotyczącą oferty „oddam za darmo”. |
| E17 | Propozycja wymiany została złożona | Zainteresowany wskazał jeden własny przedmiot lub opis tekstowy oraz wiadomość. |
| E18 | Propozycja wypożyczenia została złożona | Zainteresowany wskazał okres od–do mieszczący się w dostępnej ofercie. |
| E19 | Propozycja została zaakceptowana | Właściciel zaakceptował oczekującą propozycję. |
| E20 | Propozycja została odrzucona | Właściciel odrzucił propozycję, opcjonalnie podając powód. |
| E21 | Propozycja została wycofana | Zainteresowany wycofał swoją propozycję przed decyzją właściciela. |
| E22 | Oferta została zarezerwowana | Akceptacja propozycji odbioru lub wymiany zarezerwowała ofertę. |
| E23 | Pozostałe oczekujące propozycje zostały odrzucone | Po akceptacji propozycji odbioru lub wymiany system odrzucił pozostałe oczekujące propozycje do tej oferty. |

### 2.4. Rozmowa i wiadomości

| ID | Zdarzenie | Opis / warunek |
|---|---|---|
| E24 | Rozmowa została utworzona | Zalogowany interesant rozpoczął kontakt z właścicielem z poziomu aktywnej oferty. Rozmowa jest przypisana do oferty i dwóch uczestników, więc może powstać przed złożeniem i akceptacją propozycji. Jeżeli propozycja zostanie zaakceptowana, istniejąca rozmowa jest zachowywana i powiązywana z propozycją/transakcją; jeżeli rozmowa jeszcze nie istnieje, system tworzy ją w ramach obsługi zaakceptowanej propozycji. |
| E25 | Wiadomość została zapisana | Wiadomość została trwale zapisana w historii rozmowy. Wiadomości mogą być wymieniane zarówno przed złożeniem/akceptacją propozycji, jak i po zaakceptowaniu transakcji. |
| E26 | Wiadomość została oznaczona jako przeczytana | Odbiorca odczytał wiadomość. |

### 2.5. Oddanie przedmiotu za darmo

| ID | Zdarzenie | Opis / warunek |
|---|---|---|
| E27 | Termin odbioru został uzgodniony | Strony ustaliły termin i sposób odbioru. |
| E28 | Właściciel potwierdził przekazanie przedmiotu | Właściciel oznaczył przedmiot jako przekazany. |
| E29 | Zainteresowany potwierdził odbiór przedmiotu | Odbiorca potwierdził otrzymanie przedmiotu. |
| E30 | Transakcja oddania została zakończona | Obie strony potwierdziły przekazanie. |
| E31 | Oferta została oznaczona jako zakończona | Oferta nie jest już dostępna do składania propozycji. |

### 2.6. Wymiana przedmiotów

| ID | Zdarzenie | Opis / warunek |
|---|---|---|
| E32 | Szczegóły wymiany zostały uzgodnione | Strony uzgodniły termin i sposób przekazania obu przedmiotów. |
| E33 | Właściciel potwierdził przekazanie swojego przedmiotu | Pierwsza strona potwierdziła przekazanie. |
| E34 | Zainteresowany potwierdził przekazanie swojego przedmiotu | Druga strona potwierdziła przekazanie. |
| E35 | Wymiana została zakończona | Obie strony potwierdziły przekazanie przedmiotów. |
| E36 | Oferty uczestniczące w wymianie zostały oznaczone jako zakończone | Oferty objęte zakończoną wymianą przestały być dostępne. |

### 2.7. Wypożyczenie i zwrot

| ID | Zdarzenie | Opis / warunek |
|---|---|---|
| E37 | Wypożyczenie zostało utworzone | Zaakceptowana propozycja utworzyła wypożyczenie z planowaną datą zwrotu równą końcowi wnioskowanego okresu. |
| E38 | Termin wypożyczenia został zarezerwowany | System zarezerwował wybrany przedział dat. |
| E39 | Właściciel potwierdził wydanie przedmiotu | Właściciel oznaczył przedmiot jako wydany wypożyczającemu. |
| E40 | Wypożyczający potwierdził odbiór przedmiotu | Wypożyczający potwierdził otrzymanie przedmiotu. |
| E41 | Wypożyczenie zostało rozpoczęte | Obie strony potwierdziły wydanie i odbiór przedmiotu. |
| E42 | Przypomnienie o zwrocie zostało utworzone | System utworzył przypomnienie zgodnie z harmonogramem: 2 dni przed terminem, 1 dzień przed terminem i w dniu zwrotu. |
| E43 | Przypomnienie o zwrocie zostało przekazane kanałem powiadomień | Powiadomienie zostało zapisane w aplikacji i przekazane na żywo przez SignalR; zgodnie z FR-52 system podejmuje też próbę wysyłki e-mail. |
| E44 | Wypożyczenie otrzymało status „po terminie” | Upłynęła planowana data zwrotu, a obie strony nie potwierdziły zwrotu przedmiotu. |
| E45 | Codzienne przypomnienie o przekroczonym terminie zostało utworzone | System utworzył kolejne przypomnienie dla obu stron, dopóki zwrot nie zostanie potwierdzony. |
| E46 | Właściciel potwierdził przyjęcie zwrotu | Właściciel potwierdził otrzymanie przedmiotu od wypożyczającego. |
| E47 | Wypożyczający potwierdził zwrot przedmiotu | Wypożyczający zgłosił i potwierdził zwrot przedmiotu właścicielowi. |
| E48 | Wypożyczenie zostało zakończone | Wypożyczający zgłosił zwrot, a właściciel potwierdził otrzymanie przedmiotu. Wypożyczenie i powiązana transakcja zostały zakończone. |

### 2.8. Anulowanie i oceny

| ID | Zdarzenie | Opis / warunek |
|---|---|---|
| E49 | Transakcja została anulowana | Jedna ze stron anulowała transakcję przed przekazaniem przedmiotu. |
| E50 | Oferta ponownie stała się aktywna | Po anulowaniu przed przekazaniem oferta wróciła do statusu aktywnego. |
| E51 | Ocena została wystawiona | Uczestnik ocenił drugą stronę: 1–5 gwiazdek i opcjonalny komentarz do 500 znaków, do 14 dni po zakończeniu transakcji. |

### 2.9. Powiadomienia, historia i statystyki

| ID | Zdarzenie | Opis / warunek |
|---|---|---|
| E52 | Powiadomienie w aplikacji zostało utworzone | System utworzył powiadomienie o nowej propozycji, decyzji, wiadomości lub przypomnieniu. |
| E53 | Powiadomienie zostało oznaczone jako przeczytane | Użytkownik odczytał powiadomienie. |
| E54 | Wiadomość e-mail została przekazana do usługi pocztowej | System przekazał wiadomość do skonfigurowanego dostawcy. Konkretna usługa pozostaje poza zakresem decyzji domenowej. |
| E55 | Dane statystyk zostały przeliczone lub odświeżone | System zaktualizował dane wykorzystywane w publicznym i osobistym panelu statystyk. |

### 2.10. Administracja i moderacja

| ID | Zdarzenie | Opis / warunek |
|---|---|---|
| E56 | Zgłoszenie naruszenia zostało utworzone | Użytkownik zgłosił ofertę lub użytkownika, podając powód i opis. |
| E57 | Zgłoszenie zostało rozpatrzone | Administrator podjął decyzję i dodał notatkę. |
| E58 | Oferta została ukryta przez administratora | Oferta przestała być widoczna w publicznym wyszukiwaniu, a właściciel widzi powód. |
| E59 | Oferta została przywrócona przez administratora | Administrator przywrócił ofertę, jeśli jej stan na to pozwala. |
| E60 | Użytkownik został zablokowany | Administrator zablokował konto; użytkownik nie może się logować ani wystawiać ofert. |
| E61 | Użytkownik został odblokowany | Administrator przywrócił możliwość korzystania z konta. |
| E62 | Rola administratora została nadana użytkownikowi | Administrator nadał uprawnienia administratora. |
| E63 | Kategoria została utworzona | Administrator dodał kategorię. |
| E64 | Kategoria została zmieniona | Administrator zmienił nazwę lub strukturę kategorii. |
| E65 | Kategoria została dezaktywowana | Administrator dezaktywował kategorię; używane kategorie nie są fizycznie usuwane. |
| E66 | Akcja administratora została zapisana w dzienniku audytu | Zapisano kto, co i kiedy wykonał. |
| E67 | Rozmowa została powiązana z zaakceptowaną propozycją/transakcją | Istniejąca rozmowa dotycząca oferty została powiązana z zaakceptowaną propozycją; jeżeli rozmowa nie istniała, została utworzona i udostępniona uczestnikom. |
| E68 | Użytkownik został objęty ograniczeniem możliwości wypożyczania | Po oznaczeniu wypożyczenia jako „po terminie” użytkownik nie może składać nowych propozycji wypożyczenia jako zainteresowany. |
| E69 | Ograniczenie możliwości wypożyczania zostało automatycznie zniesione | Wszystkie zaległe wypożyczenia użytkownika zostały zakończone po potwierdzeniu zwrotów przez obie strony. |
| E70 | Administrator uchylił ograniczenie możliwości wypożyczania | Administrator po rozpatrzeniu sprawy ręcznie zdjął ograniczenie. Wypożyczenie może nadal mieć status „po terminie”, jeżeli zwrot nie został potwierdzony. |

## 3. Komendy (Commands)

Komenda jest intencją wykonania działania. Poniżej komendy przypisano do zdarzeń, które powodują w przypadku pomyślnej walidacji. Jeżeli komenda nie przejdzie walidacji lub autoryzacji, odpowiadające jej zdarzenie sukcesu nie występuje.

### 3.1. Konto i uwierzytelnianie

| ID | Komenda | Powoduje zdarzenie |
|---|---|---|
| C01 | Zarejestruj konto za pomocą adresu e-mail i hasła | E01 |
| C02 | Wyślij wiadomość aktywacyjną | E02 |
| C03 | Potwierdź adres e-mail linkiem aktywacyjnym | E03 |
| C04 | Zaloguj się za pomocą adresu e-mail i hasła | E04 |
| C05 | Zaloguj się przez Google | E05 |
| C06 | Poproś o reset hasła | E06 |
| C07 | Wyślij wiadomość resetującą hasło | E07 |
| C08 | Ustaw nowe hasło za pomocą prawidłowego linku | E08 |
| C09 | Wyloguj użytkownika | E09 |

### 3.2. Wystawianie i zarządzanie ofertą

| ID | Komenda | Powoduje zdarzenie |
|---|---|---|
| C11 | Zapisz zdjęcie oferty | E10 |
| C12 | Opublikuj ofertę z wymaganymi danymi | E11 |
| C13 | Edytuj własną ofertę | E12 |
| C14 | Wstrzymaj własną ofertę | E13 |
| C15 | Wznów własną ofertę | E14 |
| C16 | Usuń miękko własną ofertę | E15 |

### 3.3. Propozycje i decyzja właściciela

| ID | Komenda | Powoduje zdarzenie |
|---|---|---|
| C17 | Złóż propozycję odbioru | E16 |
| C18 | Złóż propozycję wymiany, wskazując jeden własny przedmiot lub opis tekstowy i wiadomość | E17 |
| C19 | Złóż propozycję wypożyczenia z datą od–do | E18 |
| C20 | Zaakceptuj oczekującą propozycję | E19 |
| C21 | Odrzuć oczekującą propozycję, opcjonalnie podając powód | E20 |
| C22 | Wycofaj własną oczekującą propozycję przed decyzją właściciela | E21 |
| C23 | Zarezerwuj ofertę po akceptacji odbioru lub wymiany | E22 |
| C24 | Odrzuć pozostałe oczekujące propozycje dotyczące zarezerwowanej oferty | E23 |

### 3.4. Rozmowa i wiadomości

| ID | Komenda | Powoduje zdarzenie |
|---|---|---|
| C25 | Rozpocznij rozmowę z właścicielem aktywnej oferty przed złożeniem propozycji | E24 |
| C26 | Wyślij wiadomość w rozmowie dotyczącej oferty | E25 |
| C27 | Oznacz wiadomość jako przeczytaną | E26 |
| C68 | Powiąż rozmowę z zaakceptowaną propozycją/transakcją lub utwórz ją, jeżeli nie istnieje | E67 |

### 3.5. Oddanie przedmiotu za darmo

| ID | Komenda | Powoduje zdarzenie |
|---|---|---|
| C28 | Uzgodnij termin i sposób odbioru | E27 |
| C29 | Potwierdź przekazanie przedmiotu jako właściciel | E28 |
| C30 | Potwierdź odbiór przedmiotu jako zainteresowany | E29 |
| C31 | Zakończ transakcję oddania po potwierdzeniu obu stron | E30 |
| C32 | Oznacz ofertę jako zakończoną | E31 |

### 3.6. Wymiana przedmiotów

| ID | Komenda | Powoduje zdarzenie |
|---|---|---|
| C33 | Uzgodnij termin i sposób wymiany | E32 |
| C34 | Potwierdź przekazanie przedmiotu jako właściciel | E33 |
| C35 | Potwierdź przekazanie przedmiotu jako zainteresowany | E34 |
| C36 | Zakończ wymianę po potwierdzeniu przekazania przez obie strony | E35 |
| C37 | Oznacz oferty objęte wymianą jako zakończone | E36 |

### 3.7. Wypożyczenie i zwrot

| ID | Komenda | Powoduje zdarzenie |
|---|---|---|
| C38 | Utwórz wypożyczenie na podstawie zaakceptowanej propozycji | E37 |
| C39 | Zarezerwuj wskazany przedział dat | E38 |
| C40 | Potwierdź wydanie przedmiotu jako właściciel | E39 |
| C41 | Potwierdź odbiór przedmiotu jako wypożyczający | E40 |
| C42 | Rozpocznij wypożyczenie po potwierdzeniu obu stron | E41 |
| C43 | Utwórz przypomnienie o zwrocie zgodnie z harmonogramem | E42 |
| C44 | Przekaż przypomnienie kanałem powiadomień | E43 |
| C45 | Oznacz wypożyczenie jako „po terminie” | E44 |
| C46 | Utwórz codzienne przypomnienie o przekroczonym terminie | E45 |
| C47 | Potwierdź przyjęcie zwrotu jako właściciel | E46 |
| C48 | Potwierdź zwrot przedmiotu jako wypożyczający | E47 |
| C49 | Zakończ wypożyczenie po potwierdzeniu zwrotu przez obie strony | E48 |

### 3.8. Anulowanie i oceny

| ID | Komenda | Powoduje zdarzenie |
|---|---|---|
| C50 | Anuluj transakcję przed przekazaniem przedmiotu | E49 |
| C51 | Przywróć ofertę do statusu aktywnego po anulowaniu | E50 |
| C52 | Wystaw ocenę drugiej stronie zakończonej transakcji | E51 |

### 3.9. Powiadomienia, historia i statystyki

| ID | Komenda | Powoduje zdarzenie |
|---|---|---|
| C53 | Utwórz powiadomienie w aplikacji dla odbiorcy | E52 |
| C54 | Oznacz powiadomienie jako przeczytane | E53 |
| C55 | Wyślij wiadomość e-mail przez skonfigurowanego dostawcę | E54 |
| C56 | Odśwież dane wykorzystywane w statystykach | E55 |

### 3.10. Administracja i moderacja

| ID | Komenda | Powoduje zdarzenie |
|---|---|---|
| C57 | Zgłoś ofertę lub użytkownika, podając powód i opis | E56 |
| C58 | Rozpatrz zgłoszenie i zapisz decyzję oraz notatkę | E57 |
| C59 | Ukryj ofertę jako administrator | E58 |
| C60 | Przywróć ofertę jako administrator | E59 |
| C61 | Zablokuj użytkownika | E60 |
| C62 | Odblokuj użytkownika | E61 |
| C63 | Nadaj użytkownikowi rolę administratora | E62 |
| C64 | Dodaj kategorię | E63 |
| C65 | Zmień nazwę lub strukturę kategorii | E64 |
| C66 | Dezaktywuj kategorię | E65 |
| C67 | Zapisz akcję administratora w dzienniku audytu | E66 |

### 3.11. Ograniczenie możliwości wypożyczania

| ID | Komenda | Powoduje zdarzenie |
|---|---|---|
| C69 | Nałóż ograniczenie możliwości składania nowych propozycji wypożyczenia | E68 |
| C70 | Znieś ograniczenie możliwości wypożyczania po zakończeniu wszystkich zaległych wypożyczeń | E69 |
| C71 | Uchyl ograniczenie możliwości wypożyczania jako administrator po rozpatrzeniu sprawy | E70 |

### Operacje odczytu — nie są zdarzeniami domenowymi

Wyszukiwanie ofert po nazwie, filtrowanie, sortowanie, paginacja, wyświetlanie mapy, profilu, historii, statystyk, listy użytkowników, kategorii i logów są operacjami odczytu. W modelu są reprezentowane jako widoki opisane w sekcji 7, a nie jako zdarzenia domenowe.

## 4. Agregaty (Aggregates)

Agregat to granica spójności domenowej: grupa danych, których zmiany powinny być kontrolowane wspólnie. Poniższa lista przedstawia agregaty logiczne zidentyfikowane w modelu, a nie bezpośredni projekt tabel bazy danych.

| ID | Agregat | Odpowiedzialność / pilnowane reguły |
|---|---|---|
| A01 | **Konto użytkownika** | Dane konta, stan aktywacji, dane profilu, administracyjny status blokady i role. Administracyjna blokada konta uniemożliwia logowanie i wystawianie ofert. Odrębne ograniczenie możliwości wypożyczania nie blokuje konta ani pozostałych funkcji — jest opisane przez A12. |
| A02 | **Oferta przedmiotu** | Właściciel, nazwa, opis, kategoria, zdjęcia, lokalizacja, stan, sposób udostępnienia i status oferty. Właściciel może edytować, wstrzymać, wznowić lub miękko usunąć własną ofertę. |
| A03 | **Propozycja transakcji** | Oferta, autor, właściciel, typ propozycji, dane wymiany lub okres wypożyczenia, wiadomość i status. Propozycja może być rozpatrzona tylko raz; autor może ją wycofać przed decyzją. |
| A04 | **Transakcja** | Strony, typ transakcji, status, potwierdzenia przekazania i zakończenie. Zakończenie oddania lub wymiany następuje po potwierdzeniach obu stron. |
| A05 | **Wypożyczenie** | Zarezerwowany przedział dat, data planowanego zwrotu, status (w tym „po terminie”), potwierdzenia wydania i zwrotu. Termin musi być dostępny i nie może kolidować z innym zaakceptowanym wypożyczeniem. Zakończenie wymaga potwierdzenia zwrotu przez obie strony. |
| A06 | **Rozmowa** | Powiązanie z ofertą i dwoma uczestnikami: właścicielem oraz zainteresowanym. Rozmowa może zostać rozpoczęta przed złożeniem lub akceptacją propozycji. Po akceptacji propozycji może zostać powiązana z tą propozycją/transakcją. Dostęp mają wyłącznie uczestnicy rozmowy. Wiadomości są zapisywane i pobierane stronicowo. |
| A07 | **Powiadomienie** | Odbiorca, typ, treść, data utworzenia i stan przeczytania. Powiadomienie pozostaje dostępne po ponownym zalogowaniu. |
| A08 | **Ocena** | Autor, oceniany użytkownik, transakcja, liczba gwiazdek i komentarz. Jedna ocena na stronę transakcji, w terminie 14 dni od zakończenia. |
| A09 | **Zgłoszenie naruszenia** | Zgłaszający, zgłoszony obiekt, powód, opis, status, decyzja i notatka administratora. |
| A10 | **Kategoria** | Nazwa, hierarchia i status aktywności. Kategoria używana przez istniejące oferty jest dezaktywowana, a nie fizycznie usuwana. |
| A11 | **Wpis audytowy** | Informacja o tym, kto, co i kiedy wykonał w ramach działań administracyjnych. Wpisy służą rozliczalności i nie są edytowane przez zwykłych użytkowników. |
| A12 | **Ograniczenie możliwości wypożyczania** | Użytkownik objęty ograniczeniem, przyczyna (co najmniej jedno zaległe wypożyczenie), data nałożenia, status i sposób zniesienia. Ograniczenie dotyczy wyłącznie występowania jako wypożyczający w nowych transakcjach wypożyczenia; nie jest blokadą całego konta. |

**Uwaga modelowa:** Wypożyczenie może być osobnym agregatem albo częścią agregatu Transakcja. Granicę należy ustalić podczas projektowania domeny i modelu danych. Podobnie Wiadomość może być elementem agregatu Rozmowa, a nie osobnym agregatem. Powyższa lista wskazuje odpowiedzialności, nie narzuca struktury tabel.

## 5. Polityki (Policies)

Polityka opisuje reakcję systemu na zdarzenie i wskazuje działanie, które system wykonuje po jego wystąpieniu.

| ID | Zdarzenie wyzwalające | Polityka — jeżeli…, to… | Komenda / rezultat |
|---|---|---|---|
| P01 | Propozycja została zaakceptowana | Jeżeli propozycja została zaakceptowana, zachowaj rozmowę dotyczącą tej oferty i powiąż ją z zaakceptowaną propozycją/transakcją. Jeżeli rozmowa nie istnieje, utwórz ją. Nie twórz drugiej rozmowy dla tej samej oferty i tej samej pary uczestników. | C68 → E67 |
| P02 | Propozycja odbioru lub wymiany została zaakceptowana | Jeżeli zaakceptowano odbiór lub wymianę, zarezerwuj ofertę i odrzuć pozostałe oczekujące propozycje do tej oferty. | C23 → E22 oraz C24 → E23 |
| P03 | Propozycja wypożyczenia została zaakceptowana | Jeżeli zaakceptowano wypożyczenie, utwórz wypożyczenie, w którym planowana data zwrotu jest końcem wnioskowanego okresu. | C38 → E37 |
| P04 | Propozycja wypożyczenia została złożona lub zaakceptowana | Jeżeli system sprawdza propozycję/rezerwację wypożyczenia, zweryfikuj dostępność przedziału i brak kolizji z zaakceptowanymi wypożyczeniami. Sprawdź dostępność przy złożeniu propozycji, ponownie przy akceptacji oraz przy rezerwacji. W razie konfliktu nie twórz zdarzenia akceptacji/rezerwacji. | Walidacja przed C19/C20/C39 |
| P05 | Obie strony potwierdziły przekazanie przedmiotu | Jeżeli właściciel i zainteresowany potwierdzili przekazanie przy oddaniu za darmo, zakończ transakcję i oznacz ofertę jako zakończoną. | C31 → E30; C32 → E31 |
| P06 | Obie strony potwierdziły przekazanie przedmiotów | Jeżeli obie strony potwierdziły przekazanie w ramach wymiany, zakończ wymianę i oznacz obie oferty jako zakończone. | C36 → E35; C37 → E36 |
| P07 | Obie strony potwierdziły wydanie i odbiór | Jeżeli właściciel i wypożyczający potwierdzili przekazanie, oznacz wypożyczenie jako rozpoczęte. | C42 → E41 |
| P08 | Zbliża się planowana data zwrotu | Jeżeli do zwrotu pozostały 2 dni, 1 dzień lub nadszedł dzień zwrotu, utwórz powiadomienie dla obu stron i podejmij próbę dostarczenia go w wymaganych kanałach. | C43/C44 → E42/E43 |
| P09 | Planowana data zwrotu minęła, a zwrot nie został potwierdzony | Jeżeli termin minął, zmień status wypożyczenia na „po terminie” i twórz codzienne przypomnienia dla obu stron. | C45/C46 → E44/E45 |
| P10 | Obie strony potwierdziły zwrot | Jeżeli właściciel i wypożyczający potwierdzili zwrot, zakończ wypożyczenie i transakcję. | C49 → E48 |
| P11 | Transakcja została anulowana przed przekazaniem | Jeżeli jedna ze stron anuluje transakcję przed przekazaniem, przywróć ofertę do statusu aktywnego. | C51 → E50 |
| P12 | Transakcja została zakończona | Jeżeli transakcja została zakończona, umożliw każdej stronie jednokrotne wystawienie oceny drugiej strony przez 14 dni. | C52 → E51 |
| P13 | Propozycja została złożona, zaakceptowana lub odrzucona; wiadomość została zapisana; przypomnienie zostało utworzone | Jeżeli wystąpi jedno z tych zdarzeń, utwórz powiadomienie w aplikacji dla właściwego odbiorcy i przekaż je na żywo przez SignalR. Podejmij również próbę wysyłki wiadomości e-mail. | C53 → E52; C55 → E54 |
| P14 | Oferta została ukryta przez administratora | Jeżeli administrator ukrył ofertę, wyłącz ją z publicznych wyników wyszukiwania i pokaż właścicielowi powód ukrycia. | Aktualizacja widoku oferty |
| P15 | Administrator wykonał akcję administracyjną | Jeżeli administrator zmienił użytkownika, kategorię, ofertę, rozpatrzył zgłoszenie lub uchylił ograniczenie, zapisz kto, co i kiedy wykonał w dzienniku audytu. | C67 → E66 |
| P16 | Wypożyczenie otrzymało status „po terminie” (E44) | Jeżeli upłynęła planowana data zwrotu i zwrot nie został potwierdzony, nałóż na wypożyczającego ograniczenie składania nowych propozycji wypożyczenia. Ograniczenie nie blokuje logowania, czatu dotyczącego bieżącego wypożyczenia, zgłoszenia zwrotu, oddawania ani wymiany. | C69 → E68 |
| P17 | Użytkownik objęty ograniczeniem próbuje złożyć propozycję wypożyczenia lub właściciel próbuje zaakceptować jego oczekującą propozycję | Jeżeli zainteresowany ma aktywne ograniczenie wypożyczania, nie przyjmuj nowej propozycji i nie pozwól na utworzenie nowego wypożyczenia na jego rzecz. Ograniczenie sprawdzaj zarówno przy składaniu propozycji, jak i ponownie przy akceptacji. Użytkownik nadal może występować jako właściciel własnej oferty. | Walidacja C19 i C20; brak zdarzenia sukcesu w razie ograniczenia |
| P18 | Wypożyczenie zostało zakończone po potwierdzeniu zwrotu (E48) | Jeżeli wszystkie zaległe wypożyczenia danego użytkownika zostały zakończone, automatycznie zdejmij jego ograniczenie możliwości wypożyczania. Jeżeli ma inne zaległe wypożyczenia, pozostaw ograniczenie aktywne. | C70 → E69 |
| P19 | Administrator rozpatrzył sprawę dotyczącą ograniczenia | Jeżeli administrator po rozpatrzeniu sprawy uzna, że ograniczenie należy uchylić, zdejmij ograniczenie i zapisz decyzję w dzienniku audytu. Uchylenie ograniczenia nie oznacza automatycznego oznaczenia wypożyczenia jako zwróconego lub zakończonego. | C71 → E70 oraz C67 → E66 |

### Zasady wspólne dla polityk

- Zadania automatyczne, w szczególności przypomnienia o zwrocie, muszą być idempotentne — ponowne uruchomienie zadania nie może tworzyć duplikatów tego samego przypomnienia.
- Zapis powiadomienia w aplikacji jest trwały; SignalR służy do dostarczenia nowej informacji na żywo, a nie do przechowywania historii.
- Wiadomości e-mail dotyczą aktywacji konta, resetowania hasła oraz powiadomień o propozycjach, decyzjach, wiadomościach i terminach zwrotu. Przekazanie wiadomości odbywa się przez skonfigurowaną usługę pocztową.
- Nieudana komenda nie powoduje zdarzenia sukcesu. System powinien zwrócić użytkownikowi czytelny komunikat o błędzie.
- Każda operacja administratora powinna zostać zapisana w dzienniku audytu niezależnie od zwykłego logowania technicznego.

## 6. Widoki (Views)

Widoki przedstawiają informacje dostępne użytkownikom i administratorowi w poszczególnych procesach.

| ID | Widok | Odbiorca |
|---|---|---|
| V01 | Formularz rejestracji i logowania | Gość |
| V02 | Profil użytkownika | Użytkownik / Gość |
| V03 | Formularz wystawiania i edycji oferty | Użytkownik |
| V04 | Lista ofert, filtry i wyniki wyszukiwania | Gość / Użytkownik |
| V05 | Mapa ofert z przybliżonymi lokalizacjami | Gość / Użytkownik |
| V06 | Szczegóły oferty | Gość / Użytkownik |
| V07 | Lista propozycji wysłanych i otrzymanych oraz ich statusy | Użytkownik |
| V08 | Szczegóły propozycji i decyzja właściciela | Właściciel |
| V09 | Lista rozmów, historia wiadomości oraz akcja „Napisz do właściciela” dostępna na szczegółach aktywnej oferty | Użytkownik |
| V10 | Lista powiadomień i licznik nieprzeczytanych | Użytkownik |
| V11 | Aktywne wypożyczenia i planowane daty zwrotu | Użytkownik |
| V12 | Historia: oddane/odebrane, wymiany, wypożyczenia | Użytkownik |
| V13 | Profil z ocenami | Użytkownik / Gość |
| V14 | Publiczne statystyki platformy | Gość / Użytkownik |
| V15 | Panel zarządzania użytkownikami, kategoriami i zgłoszeniami | Administrator |
| V16 | Logi aplikacji z filtrami | Administrator |
| V17 | Informacja o aktywnym ograniczeniu wypożyczania, przyczynie i sposobie jego zniesienia | Użytkownik |
| V18 | Szczegóły zaległych wypożyczeń i możliwość rozpatrzenia/uchylenia ograniczenia | Administrator |

## 7. Reguły domenowe

### 7.1. Rozmowy dotyczące ofert i transakcji

- Zalogowany użytkownik może rozpocząć rozmowę z właścicielem z poziomu aktywnej oferty, jeszcze przed złożeniem propozycji.
- Rozmowa jest prywatna i dostępna wyłącznie dwóm uczestnikom: właścicielowi oraz zainteresowanemu.
- Po zaakceptowaniu propozycji ta sama rozmowa zostaje powiązana z propozycją/transakcją i jest kontynuowana; nie powstaje drugi czat.
- Historia wiadomości pozostaje zapisana i dostępna stronom po ponownym zalogowaniu.

### 7.2. Ograniczenie możliwości wypożyczania po przekroczeniu terminu

- Ograniczenie jest nakładane automatycznie po upływie planowanej daty zwrotu, jeżeli zwrot nie został potwierdzony.
- Ograniczenie dotyczy wyłącznie składania nowych propozycji wypożyczenia jako zainteresowany (potencjalny wypożyczający). System sprawdza je również przy akceptowaniu oczekującej propozycji, aby nie dało się rozpocząć nowego wypożyczenia po nałożeniu ograniczenia.
- Ograniczenie nie blokuje logowania, przeglądania ofert, korzystania z czatu dotyczącego zaległego wypożyczenia, zgłoszenia zwrotu, oddawania przedmiotów ani wymiany. Użytkownik nadal może występować jako właściciel i rozpatrywać propozycje dotyczące jego własnych ofert, o ile nie ma odrębnej blokady administracyjnej konta.
- Użytkownik zgłasza zwrot przedmiotu, a właściciel potwierdza jego otrzymanie. Po potwierdzeniu obu stron wypożyczenie zostaje zakończone.
- Ograniczenie jest automatycznie zdejmowane, gdy wszystkie zaległe wypożyczenia danego użytkownika zostaną zakończone. Jeżeli istnieje więcej niż jedno zaległe wypożyczenie, zakończenie tylko jednego nie wystarcza do zdjęcia ograniczenia.
- Administrator może ręcznie uchylić ograniczenie po rozpatrzeniu sprawy. Taka decyzja jest rejestrowana w dzienniku audytu i sama w sobie nie oznacza, że przedmiot został zwrócony.
- Nie przewiduje się kar finansowych, opłat ani kaucji.

### 7.3. Powiadomienia i wiadomości e-mail

- Wiadomości e-mail są przekazywane do skonfigurowanej usługi pocztowej w procesach aktywacji konta, resetowania hasła i powiadomień. Dostawca poczty jest elementem infrastruktury systemu.
- Powiadomienia w aplikacji są zapisywane trwale i przekazywane na żywo przez SignalR.
- Powiadomienia o zbliżającym się i przekroczonym terminie zwrotu są dostępne dla obu stron wypożyczenia.

## 8. Powiązanie procesów z wymaganiami

| Obszar modelu | Wymagania funkcjonalne |
|---|---|
| Konto i uwierzytelnianie | FR-01–FR-05 |
| Wystawianie i zarządzanie ofertą | FR-10–FR-18 |
| Wyszukiwanie i mapa | FR-20–FR-24 |
| Propozycje i decyzja właściciela | FR-30–FR-35 |
| Czat | FR-40–FR-43 |
| Powiadomienia i przypomnienia | FR-50–FR-52 |
| Oddanie, wymiana, wypożyczenie, anulowanie i zwrot | FR-30–FR-37 |
| Oceny | FR-60–FR-61 |
| Historia | FR-70–FR-71 |
| Statystyki | FR-80–FR-82 |
| Administracja i moderacja | FR-90–FR-95 |
| Ograniczenie możliwości wypożyczania po terminie | Reguły obsługi zaległego wypożyczenia powiązane z FR-51 i uprawnieniami administratora z FR-90 |

## 9. Podsumowanie modelu

Model przedstawia główne zdarzenia zachodzące w platformie, komendy inicjujące działania, agregaty odpowiadające za spójność danych oraz polityki określające reakcje systemu na zdarzenia. Obejmuje pełny cykl życia oferty i transakcji, w tym komunikację użytkowników, obsługę wypożyczeń po terminie, powiadomienia i działania administracyjne.

## 10. Materiały powiązane

- [Wymagania funkcjonalne i niefunkcjonalne](wymagania.md)
- [Architektura systemu](architektura.md)
- [Rejestr ADR](adr/README.md)
- [Szablon Event Storming prowadzącego](https://github.com/collegiumwitelona/INF-s5-WDZPD-wprowadzenie-do-zarzadzania-projektami-deweloperskimi-template/blob/main/lab/templates/03-event-storming-template.md)
