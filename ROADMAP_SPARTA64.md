# SPARTA 64 — ROADMAP v1.0

**Status:** plan bazowy; terminy kalendarzowe ustalimy po pierwszym prototypie.  
**Zasada:** etap kończy się działającym rezultatem i testami, nie samym kodem.

## Etap 0 — Repozytorium i przygotowanie

**Cel:** uporządkować projekt i zapewnić możliwość odtworzenia pracy.

Zadania:
- utrzymywać dokumentację w katalogu głównym lub `/docs`;
- dodać `.gitignore` dla wybranych technologii;
- ustalić zasady nazw gałęzi i commitów;
- przygotować instrukcję uruchamiania;
- nie dodawać sekretów ani danych prawdziwych zawodników.

**Rezultat:** uporządkowane repozytorium i dokumentacja startowa.

## Etap 1 — Prototyp szachowy bez kont

**Cel:** sprawdzić najważniejszy element produktu — poprawne działanie szachownicy i silnika.

Zadania:
- wybrać i sprawdzić bibliotekę reguł szachowych;
- wyświetlić szachownicę w jasnym motywie treningowym;
- obsłużyć legalne ruchy, roszadę, promocję i bicie w przelocie;
- eksportować i importować PGN oraz FEN;
- uruchomić Stockfish lokalnie lub w środowisku prototypowym;
- komunikować się z nim przez UCI;
- sprawdzić odpowiedzi silnika na pozycje testowe.

**Rezultat:** działająca demonstracja szachownicy i osobny test UCI.  
**Test akceptacyjny:** zestaw znanych pozycji i partii przechodzi testy reguł; silnik zwraca legalny najlepszy ruch dla zadanych pozycji.

## Etap 2 — Szkielet aplikacji

**Cel:** uruchomić frontend, backend i bazę w jednym powtarzalnym środowisku.

Zadania:
- potwierdzić stos technologiczny;
- utworzyć strukturę katalogów;
- skonfigurować backend i API;
- skonfigurować frontend;
- skonfigurować PostgreSQL i migracje;
- dodać testy automatyczne i podstawowy pipeline CI.

**Rezultat:** aplikacja uruchamia się lokalnie według README.

## Etap 3 — Tożsamość i klub

**Cel:** bezpiecznie obsłużyć konta i strukturę klubu.

Zadania:
- logowanie i wylogowanie;
- role i uprawnienia;
- zapraszanie/aktywacja kont;
- profile zawodników i trenerów;
- grupy treningowe i przypisania;
- podstawowy panel administratora.

**Rezultat:** fikcyjni użytkownicy trzech ról mogą wykonać dozwolone czynności.  
**Test akceptacyjny:** próby dostępu do cudzych danych są blokowane po stronie serwera.

## Etap 4 — Partie i gra treningowa

**Cel:** umożliwić zawodnikowi rozegranie i zapisanie partii.

Zadania:
- zapis partii i historii ruchów;
- gra z komputerem;
- wybór koloru i poziomu trudności;
- import PGN;
- lista własnych partii;
- kontrola poprawności danych wejściowych.

**Rezultat:** zawodnik rozgrywa, zapisuje i otwiera ponownie partię.

## Etap 5 — Analiza Stockfish

**Cel:** udostępnić analizę partii w sposób bezpieczny i skalowalny.

Zadania:
- worker i kolejka analiz;
- limity czasu, głębokości i zasobów;
- statusy zadań;
- zapis wersji silnika i parametrów;
- ocena pozycji, najlepsze warianty i zmiany oceny;
- widok analizy w motywie jasnym;
- obsługa błędów i ponawiania.

**Rezultat:** analiza przykładowej partii jest dostępna w profilu uprawnionego użytkownika.

## Etap 6 — Akademia i zadania

**Cel:** trener może przygotować materiał, a zawodnik może się na nim uczyć.

Zadania:
- kursy i lekcje;
- edytor zadań na szachownicy;
- rozwiązania, podpowiedzi i wyjaśnienia;
- poziomy trudności i tematy;
- przydziały indywidualne i grupowe;
- zapis prób, wyników i postępów.

**Rezultat:** pełny przepływ: trener tworzy zadanie → przydziela → zawodnik rozwiązuje → trener widzi wynik.

## Etap 7 — Panel trenera

**Cel:** ułatwić codzienną pracę dwóch trenerów z ponad 100 zawodnikami.

Zadania:
- lista zawodników i wyszukiwarka;
- filtry według grupy i aktywności;
- widok profilu i historii pracy;
- przegląd partii i analiz;
- komentarze trenera;
- raporty postępów;
- lista osób wymagających uwagi oparta na przejrzystych regułach.

**Rezultat:** trener potrafi zarządzać grupą i śledzić postępy bez ręcznego zestawiania danych.

## Etap 8 — Pilotaż klubowy

**Cel:** sprawdzić produkt z małą grupą przed udostępnieniem wszystkim.

Zadania:
- testy bezpieczeństwa i uprawnień;
- testy na telefonach i komputerach;
- sprawdzenie dostępności i czytelności dla dzieci;
- testy obciążeniowe;
- szkolenie trenerów;
- zbieranie opinii;
- poprawki po pilotażu.

**Rezultat:** decyzja o gotowości do szerszego uruchomienia.

## Etap 9 — Wdrożenie i dalszy rozwój

**Cel:** uruchomić stabilną platformę dla klubu.

Zadania:
- wybór i konfiguracja hostingu;
- domena i HTTPS;
- kopie zapasowe oraz test odtwarzania;
- monitoring i aktualizacje;
- PWA;
- ocena potrzeby natywnej aplikacji Android;
- ewentualne integracje z zewnętrznymi serwisami szachowymi.

**Rezultat:** produkcyjna SPARTA 64 z procedurą utrzymania.

---

## Priorytety pierwszego wydania

**Musi być:**
- konta i role;
- profile zawodników;
- szachownica z legalnymi ruchami;
- partie i PGN;
- podstawowa analiza Stockfish;
- lekcje i zadania;
- panel trenera;
- podstawowe postępy;
- bezpieczeństwo, kopie zapasowe i monitoring.

**Może poczekać:**
- konta rodziców;
- integracje Chess.com/Lichess;
- oficjalne rankingi;
- rozbudowane turnieje;
- czat i funkcje społecznościowe;
- natywna aplikacja Android, jeśli PWA spełni potrzeby pilotażu.

## Zasady przejścia między etapami

Nie przechodzimy dalej, jeśli:
- podstawowe scenariusze nie działają;
- testy krytyczne nie przechodzą;
- występuje znany problem bezpieczeństwa;
- dokumentacja nie opisuje sposobu uruchomienia;
- nie ma stabilnej wersji do cofnięcia.

## Aktualizacja planu

Po każdym etapie aktualizujemy status, ryzyka, decyzje i kryteria. Terminy określimy po sprawdzeniu dostępnego czasu zespołu i wyników prototypu — nie będziemy obiecywać dat bez danych.
