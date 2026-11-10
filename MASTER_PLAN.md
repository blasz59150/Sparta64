# SPARTA 64 --- MASTER PLAN v1.0

**Projekt:** Akademia Szachowa LZS Sparta Grębocice\
**Status:** Planowanie / przed rozpoczęciem implementacji\
**Właściciel produktu:** LZS Sparta Grębocice\
**Użytkownicy docelowi:** ponad 100 zawodników, dwóch trenerów,
administratorzy oraz --- w odpowiednim zakresie --- rodzice/opiekunowie.

## 1. Wizja

SPARTA 64 będzie platformą szkoleniową klubu LZS Sparta Grębocice. Ma
wspierać naukę dzieci i młodzieży, samodzielny trening, rozgrywanie
partii, analizę szachową oraz codzienną pracę trenerów.

Priorytetem jest rzeczywista użyteczność edukacyjna, bezpieczeństwo
danych i możliwość długoterminowego rozwijania systemu. Aplikacja ma być
przyjazna dla początkujących, ale nie ograniczać bardziej zaawansowanych
zawodników.

## 2. Zasady projektowe

1.  **Budowa modułowa:** rozwijamy system małymi, działającymi etapami.
2.  **Jeden system danych:** portal i aplikacja mobilna korzystają z
    tego samego backendu i API.
3.  **Najpierw stabilność:** każdy etap ma kryteria ukończenia i testy.
4.  **Bezpieczeństwo od początku:** szczególna ochrona danych
    nieletnich.
5.  **Brak uzależnienia od hostingu:** aplikacja ma być przenośna i
    możliwa do wdrożenia na docelowym VPS/hostingu.
6.  **Dokumentacja i Git:** każda istotna decyzja i ukończony moduł są
    dokumentowane.
7.  **Nie udajemy funkcji:** makiety, prototypy i funkcje produkcyjne
    będą wyraźnie rozróżniane.

## 3. Użytkownicy i role

### Zawodnik / uczeń

-   własny profil i postępy;
-   kursy, lekcje i zadania;
-   gra treningowa z komputerem;
-   zapis i przegląd własnych partii;
-   historia treningów i osiągnięć.

### Trener

-   przegląd przypisanych zawodników i grup;
-   tworzenie lekcji, kursów i zadań;
-   przydzielanie pracy indywidualnej i grupowej;
-   przegląd partii i analiz;
-   komentarze oraz obserwacja postępów.

### Administrator klubu

-   zarządzanie kontami, rolami i grupami;
-   zapraszanie zawodników i aktywacja kont;
-   konfiguracja platformy;
-   zarządzanie treściami i uprawnieniami.

### Rodzic / opiekun --- etap późniejszy

-   ograniczony podgląd postępów dziecka;
-   dostęp wyłącznie po prawidłowym powiązaniu kont i określeniu
    podstawy oraz zakresu dostępu.

## 4. Zakres funkcjonalny

### A. Konta i organizacja klubu

-   logowanie i bezpieczne odzyskiwanie dostępu;
-   role i uprawnienia;
-   profile zawodników;
-   grupy treningowe;
-   zaproszenia lub kody aktywacyjne;
-   rejestrowanie zgód i informacji wymaganych dla użytkowników
    niepełnoletnich.

### B. Rdzeń szachowy

-   interaktywna szachownica;
-   legalność ruchów i pełne reguły szachowe;
-   promocja, roszada, bicie w przelocie;
-   zapis i odczyt PGN;
-   pozycje FEN;
-   historia ruchów i nawigacja po partii.

### C. Gra z komputerem

-   integracja z silnikiem Stockfish przez UCI;
-   wybór koloru i poziomu trudności;
-   kontrola czasu jako funkcja późniejsza;
-   podpowiedzi w trybie nauki;
-   możliwość ponowienia partii.

### D. Analiza partii

-   import PGN;
-   analiza pozycji i ruchów Stockfish;
-   ocena pozycji i najlepsze warianty;
-   wykrywanie poważnych błędów i niedokładności według jawnie opisanej
    metodologii;
-   komentarze trenera;
-   raport edukacyjny z wyjaśnieniami dostosowanymi do poziomu ucznia.

**Uwaga:** ocena silnika nie jest tym samym co obiektywna „dokładność
zawodnika". Metryki i progi klasyfikacji ruchów muszą zostać
zdefiniowane i przetestowane.

### E. Akademia

-   kursy podzielone na poziomy;
-   lekcje tekstowe i interaktywne;
-   zadania na szachownicy;
-   stopniowane podpowiedzi;
-   informacja zwrotna po ruchu;
-   śledzenie ukończenia i wyników.

### F. Panel trenera

-   lista i wyszukiwanie zawodników;
-   widok aktywności i postępów;
-   kreator lekcji i zadań;
-   przydzielanie pracy;
-   przegląd partii i komentarze;
-   raporty dla zawodnika i grupy.

### G. Statystyki i motywacja

-   aktywność treningowa;
-   postęp w kursach i zadaniach;
-   historia partii;
-   osiągnięcia i wyzwania;
-   rankingi motywacyjne z ostrożnym doborem kryteriów, aby nie
    zniechęcać początkujących.

### H. Import i integracje --- późniejsze etapy

-   import plików PGN jako podstawa;
-   integracja z Lichess i Chess.com po sprawdzeniu aktualnych API,
    warunków i ograniczeń;
-   rankingi PZSzach/FIDE w kolejnej wersji, po ustaleniu legalnego i
    technicznego źródła danych.

## 5. Architektura --- kierunek wstępny

Rekomendowany jest **modularny monolit**: jeden backend z logicznie
oddzielonymi modułami. To prostsze w utrzymaniu niż mikroserwisy i
wystarczające dla skali klubu.

Wstępny kierunek technologiczny do potwierdzenia w prototypie: -
frontend: React + TypeScript; - backend: Laravel/PHP; - API: REST,
wersjonowane; - baza: PostgreSQL; - kolejka zadań: Redis lub mechanizm
zgodny z możliwościami hostingu; - silnik: Stockfish, komunikacja UCI; -
szachownica: biblioteka z obsługą legalnych ruchów, po weryfikacji
licencji i utrzymania; - wdrożenie: Linux VPS lub odpowiedni hosting,
konteneryzacja jeśli uzasadniona; - aplikacja mobilna: najpierw
responsywna PWA; decyzja o natywnej aplikacji Android po pilotażu.

Technologie są propozycją, nie nieodwołalnym wyborem. Zostaną
zatwierdzone po sprawdzeniu wymagań, licencji, hostingu i prototypu.

## 6. Stockfish --- sposób integracji

Stockfish będzie osobnym komponentem analitycznym, komunikującym się
przez protokół UCI. Należy: - zweryfikować aktualną licencję i obowiązki
dystrybucyjne; - testować silnik niezależnie od interfejsu; - ograniczać
czas i zasoby analiz; - wykonywać długie analizy w tle, a nie w żądaniu
użytkownika; - zapisywać wersję silnika, ustawienia analizy i wynik; -
odróżniać analizę treningową od analizy turniejowej; - zapewnić kolejkę,
limity i możliwość ponowienia nieudanej analizy.

## 7. Bezpieczeństwo i prywatność

Przed uruchomieniem produkcyjnym wymagane będą: - HTTPS; - bezpieczne
haszowanie haseł i ochrona sesji; - kontrola dostępu po stronie serwera
dla każdej operacji; - minimalizacja zbieranych danych; - kopie zapasowe
i test odtwarzania; - logi zdarzeń administracyjnych bez nadmiarowych
danych; - ograniczenia prób logowania i ochrona przed nadużyciami; -
procedura usuwania i eksportu danych; - zasady widoczności profili i
partii; - ustalenie ról administratora danych, podstaw przetwarzania i
wymaganych zgód z klubem oraz osobą odpowiedzialną za ochronę danych.

Nie należy używać prawdziwych danych dzieci w środowisku testowym. Testy
wykonujemy na danych fikcyjnych.

## 8. Etapy realizacji i kryteria ukończenia

### Etap 0 --- przygotowanie projektu

-   repozytorium Git;
-   README i dokumentacja;
-   zasady gałęzi i commitów;
-   lista decyzji technicznych;
-   środowisko developerskie.

**Gotowe, gdy:** nowa osoba może odtworzyć środowisko według instrukcji,
a repozytorium nie zawiera sekretów.

### Etap 1 --- prototyp szachowy

-   szachownica;
-   legalne ruchy;
-   PGN/FEN;
-   testy reguł;
-   mały test komunikacji ze Stockfish UCI.

**Gotowe, gdy:** zestaw testowych pozycji i partii przechodzi testy, a
silnik zwraca poprawne odpowiedzi na kontrolowane zapytania.

### Etap 2 --- backend i baza

-   szkielet API;
-   migracje bazy;
-   modele użytkowników, ról, grup i partii;
-   walidacja i testy.

**Gotowe, gdy:** API zapisuje i odczytuje dane testowe z kontrolą
uprawnień.

### Etap 3 --- logowanie i profile

-   logowanie;
-   role;
-   aktywacja kont;
-   profile zawodników;
-   podstawowy panel administratora.

**Gotowe, gdy:** scenariusze zawodnika, trenera i administratora
przechodzą testy dostępu.

### Etap 4 --- partie i gra

-   zapis partii;
-   gra z komputerem;
-   historia partii;
-   import PGN.

**Gotowe, gdy:** zawodnik może rozegrać, zapisać i ponownie otworzyć
partię.

### Etap 5 --- analiza

-   kolejka analiz;
-   Stockfish po stronie serwera;
-   raport ruch po ruchu;
-   komentarze trenera.

**Gotowe, gdy:** analiza partii testowej jest powtarzalna, opisana i
widoczna tylko dla uprawnionych osób.

### Etap 6 --- akademia

-   kreator lekcji i zadań;
-   kursy i poziomy;
-   przydziały;
-   informacja zwrotna;
-   śledzenie postępów.

**Gotowe, gdy:** trener tworzy zadanie, przydziela je uczniowi, a uczeń
rozwiązuje je i widzi wynik.

### Etap 7 --- panel trenera i raporty

-   lista zawodników;
-   grupy;
-   filtry i podsumowania;
-   postępy i aktywność;
-   raporty.

**Gotowe, gdy:** trenerzy mogą obsługiwać pilotażową grupę bez ręcznego
zarządzania danymi poza systemem.

### Etap 8 --- integracje i wdrożenie

-   import z usług zewnętrznych po weryfikacji API;
-   kopie zapasowe;
-   monitoring;
-   testy obciążeniowe;
-   PWA i ewentualna aplikacja Android;
-   pilotaż, poprawki i uruchomienie.

**Gotowe, gdy:** system przejdzie testy akceptacyjne, procedurę
odtwarzania kopii i pilotaż z użytkownikami.

## 9. Zasady pracy nad kodem

-   Małe, logiczne zmiany.
-   Każda funkcja ma testy.
-   Nie łączymy wielu niepowiązanych zmian w jednym commicie.
-   Przed większymi zmianami tworzymy gałąź.
-   Nie zapisujemy haseł, tokenów ani kluczy w repozytorium.
-   Nie wdrażamy bez kopii zapasowej i planu cofnięcia.
-   Aktualizujemy dokumentację po każdej zmianie architektury.
-   Każdy etap kończy się demonstracją działającej funkcji.

## 10. Pierwsze zadania

1.  Sprawdzić stan repozytorium i ustalić główną gałąź.
2.  Dodać ten plan oraz README.
3.  Ustalić wersje narzędzi i sposób uruchamiania lokalnego.
4.  Sprawdzić licencje i wybrać biblioteki szachowe.
5.  Zbudować mały prototyp szachownicy i test UCI Stockfish.
6.  Dopiero na podstawie prototypu zatwierdzić szczegóły stosu
    technologicznego.

## 11. Poza zakresem pierwszego wydania

-   publiczny portal informacyjny klubu;
-   rozbudowane funkcje społecznościowe i czat;
-   oficjalne rankingi PZSzach/FIDE;
-   zaawansowane turniejowe zarządzanie kojarzeniami;
-   automatyczne oceny „talentu" lub potencjału dziecka.

Te funkcje mogą zostać dodane po uruchomieniu stabilnej akademii.

------------------------------------------------------------------------

**Cel nadrzędny:** dostarczyć bezpieczną, działającą i użyteczną
platformę szkoleniową dla LZS Sparta Grębocice --- nie tylko efektowną
makietę.
