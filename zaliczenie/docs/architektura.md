# Architektura w skrócie

SPA w przeglądarce rozmawia z jednym kontenerem API przez REST i WebSocket; API jako jedyne sięga do bazy, MinIO, poczty i geokodowania.

## Komponenty

```mermaid
flowchart LR
    subgraph Przegladarka["Przeglądarka"]
        SPA["React + TypeScript (Vite)<br/>Leaflet, klient SignalR"]
    end

    subgraph Compose["Docker Compose"]
        WEB["web<br/>nginx z buildem SPA"]
        subgraph API["api · ASP.NET Core"]
            REST["REST API /api/v1<br/>Identity, JWT, EF Core"]
            HUB["Hub SignalR /hubs/app<br/>czat i powiadomienia"]
            BG["BackgroundService<br/>przypomnienia o zwrocie"]
            OA["OpenAPI + Scalar<br/>/openapi/v1.json"]
        end
        DB[("PostgreSQL<br/>PostGIS, pg_trgm")]
        MINIO[("MinIO<br/>zdjęcia, S3")]
        MAIL["Mailpit<br/>SMTP"]
    end

    GOOGLE["Google OAuth (OIDC)"]
    OSM["OpenStreetMap<br/>kafelki, Nominatim"]

    WEB -- "pliki SPA" --> SPA
    SPA -- "REST" --> REST
    SPA <-- "WebSocket" --> HUB
    REST -- "EF Core" --> DB
    HUB --> DB
    BG --> DB
    REST -- "S3" --> MINIO
    SPA -- "podpisane URL-e" --> MINIO
    BG -- "SMTP" --> MAIL
    REST -- "SMTP" --> MAIL
    SPA -- "przekierowanie" --> GOOGLE
    REST -- "wymiana kodu" --> GOOGLE
    REST -- "geokodowanie" --> OSM
    SPA -- "kafelki mapy" --> OSM
```

Kafelki mapy przeglądarka pobiera bezpośrednio z OpenStreetMap, a zdjęcia przez podpisane URL-e z MinIO ([ADR-009](adr/009-zdjecia-minio.md)).

## Cykl życia propozycji i transakcji

```mermaid
stateDiagram-v2
    [*] --> Oczekujaca: propozycja wysłana
    Oczekujaca --> Zaakceptowana: akceptacja
    Oczekujaca --> Odrzucona: odrzucenie / wycofanie
    Zaakceptowana --> Zakonczona: przekazanie potwierdzone (oddanie, wymiana)
    Zaakceptowana --> Wypozyczona: przekazanie (wypożyczenie)
    Zaakceptowana --> Anulowana: anulowanie
    Wypozyczona --> Zakonczona: zwrot
    Wypozyczona --> PoTerminie: minął termin zwrotu
    PoTerminie --> Zakonczona: zwrot po terminie
    Zakonczona --> [*]: oceny przez 14 dni
    Odrzucona --> [*]
    Anulowana --> [*]

    Oczekujaca: Oczekująca
    Zakonczona: Zakończona
    Wypozyczona: Wypożyczona (planowana data zwrotu)
    PoTerminie: Po terminie (przypomnienie co dzień)
```

Oddanie i wymiana kończą się po potwierdzeniu przekazania; wypożyczenie dopiero po potwierdzeniu zwrotu, a system pilnuje terminu w tle ([ADR-011](adr/011-przypomnienia-background-service.md)).
