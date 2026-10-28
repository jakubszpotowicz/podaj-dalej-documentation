# Rejestr decyzji architektonicznych (ADR)

ADR-y spisano według szablonu kursu (`lab/templates/06-adr-template.md`). Zaakceptowanego ADR-a się nie edytuje; zmiana decyzji to nowy ADR, a stary dostaje status „Zastąpiona”.

| ADR | Tytuł | Status |
| --- | --- | --- |
| [001](001-architektura-systemu.md) | Użycie modularnego monolitu z oddzielną aplikacją SPA | Zaakceptowana |
| [002](002-backend-aspnet-core.md) | Użycie ASP.NET Core Web API jako backendu | Zaakceptowana |
| [003](003-frontend-react.md) | Użycie React + TypeScript + Vite jako frontendu | Zaakceptowana |
| [004](004-baza-danych-postgresql-postgis.md) | Użycie PostgreSQL z PostGIS jako bazy danych | Zaakceptowana |
| [005](005-wyszukiwanie-tekstowe.md) | Wyszukiwanie tekstowe przez pg_trgm i unaccent | Zaakceptowana |
| [006](006-uwierzytelnianie.md) | Uwierzytelnianie przez Identity, JWT i Google OAuth | Zaakceptowana |
| [007](007-ochrona-przed-podwojna-rezerwacja.md) | Ochrona przed podwójną rezerwacją na poziomie bazy | Zaakceptowana |
| [008](008-czat-signalr.md) | Użycie SignalR do czatu i powiadomień | Zaakceptowana |
| [009](009-zdjecia-minio.md) | Przechowywanie zdjęć w MinIO | Zaakceptowana |
| [010](010-mapa-leaflet-osm.md) | Użycie Leaflet i OpenStreetMap jako mapy | Zaakceptowana |
| [011](011-przypomnienia-background-service.md) | Realizacja przypomnień jako BackgroundService | Zaakceptowana |
| [012](012-logi-serilog.md) | Zapis logów aplikacji w PostgreSQL przez Serilog | Zaakceptowana |
| [013](013-dokumentacja-openapi.md) | Generowanie dokumentacji OpenAPI z kodu | Zaakceptowana |
| [014](014-strategia-testow.md) | Testy na prawdziwej bazie w kontenerach | Zaakceptowana |
| [015](015-srodowisko-docker-compose.md) | Uruchamianie środowiska przez Docker Compose | Zaakceptowana |
