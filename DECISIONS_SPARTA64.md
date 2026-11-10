# SPARTA 64 — DECISIONS LOG

Rejestr decyzji projektowych. Nowe decyzje dopisujemy na górze. Zmiany decyzji nie kasują historii — dodajemy nowy wpis z uzasadnieniem.

## D-001 — Projekt budowany modułowo
- **Status:** przyjęta
- **Decyzja:** budujemy SPARTA 64 małymi, działającymi modułami według wspólnego planu i architektury.
- **Uzasadnienie:** łatwiejsze testowanie, mniejsze ryzyko i możliwość regularnego korzystania z gotowych funkcji.

## D-002 — Architektura modularnego monolitu
- **Status:** kierunek przyjęty, szczegóły do walidacji
- **Decyzja:** początkowo jeden backend z logicznie odseparowanymi modułami, zamiast mikroserwisów.
- **Uzasadnienie:** prostsze wdrożenie i utrzymanie dla skali klubu.

## D-003 — Wspólny backend dla urządzeń
- **Status:** przyjęta
- **Decyzja:** przeglądarka, PWA i ewentualna aplikacja Android korzystają ze wspólnego API i wspólnej bazy danych.
- **Uzasadnienie:** jedno źródło danych i spójne konta.

## D-004 — Dwa motywy wizualne
- **Status:** przyjęta
- **Decyzja:** główny motyw klubowy DARK SPARTA (czerń, czerwień, złoto) oraz jasny motyw treningowy LIGHT CHESS.
- **Zastosowanie:** dashboardy i identyfikacja klubu w motywie ciemnym; rozgrywanie partii, zadania i analiza mają korzystać z jasnego, łagodnego motywu. Użytkownik może mieć ustawienie preferencji.
- **Uzasadnienie:** zachowanie charakteru klubu bez męczenia wzroku podczas nauki.

## D-005 — Pierwsze wydanie skupione na akademii
- **Status:** przyjęta
- **Decyzja:** priorytetem jest aplikacja akademii szachowej. Publiczny portal klubu nie jest częścią pierwszego wydania; klub korzysta obecnie z Facebooka.
- **Uzasadnienie:** ograniczenie zakresu i skupienie wysiłku na narzędziu szkoleniowym. Portal można zintegrować później.

## D-006 — Grupa użytkowników
- **Status:** przyjęta jako założenie planistyczne
- **Decyzja:** system projektujemy dla ponad 100 zawodników i dwóch trenerów, z możliwością rozwoju.
- **Uzasadnienie:** od początku uwzględniamy filtrowanie, grupy, role i wydajność.

## D-007 — Silnik analityczny
- **Status:** kierunek przyjęty, licencję i wdrożenie trzeba zweryfikować
- **Decyzja:** Stockfish jest preferowanym silnikiem do analizy i gry z komputerem; komunikacja przez UCI.
- **Uzasadnienie:** dojrzały silnik szachowy i standardowy protokół integracji.

## D-008 — Aplikacja mobilna
- **Status:** kierunek przyjęty
- **Decyzja:** zaczynamy od responsywnej aplikacji webowej/PWA. Natywną aplikację Android rozważymy po pilotażu.
- **Uzasadnienie:** jeden kod i szybsze uruchomienie na telefonach oraz komputerach.

## D-009 — Hosting
- **Status:** odłożona do prototypu
- **Decyzja:** nie kupujemy hostingu przed sprawdzeniem wymagań backendu, kolejki i Stockfish. Docelowo dobierzemy serwer do potwierdzonych potrzeb.
- **Uzasadnienie:** uniknięcie zakupu niewłaściwego planu.

## D-010 — Dane nieletnich
- **Status:** zasada obowiązująca
- **Decyzja:** używamy minimalnego zakresu danych, fikcyjnych danych w testach i kontroli dostępu po stronie serwera. Szczegóły zgód, retencji i obowiązków klubu ustalamy przed pilotażem.
- **Uzasadnienie:** ochrona zawodników i odpowiedzialne przetwarzanie danych.

## D-011 — Dokumentacja i repozytorium
- **Status:** przyjęta
- **Decyzja:** GitHub jest głównym repozytorium kodu i dokumentacji. Każdy etap ma testy, opis uruchomienia i stabilny punkt przywracania.
- **Uzasadnienie:** ciągłość projektu i możliwość przekazania go innemu programiście.

---

## Szablon nowej decyzji

### D-XXX — Krótki tytuł
- **Data:** RRRR-MM-DD
- **Status:** proponowana / przyjęta / odrzucona / zastąpiona
- **Decyzja:** 
- **Uzasadnienie:** 
- **Konsekwencje:** 
- **Powiązane dokumenty:** 
