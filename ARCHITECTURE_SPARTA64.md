# SPARTA 64 — ARCHITECTURE v1.0

**Status:** propozycja architektury do zatwierdzenia  
**Powiązany dokument:** `MASTER_PLAN.md`

## 1. Cel architektury

SPARTA 64 ma być jednym systemem dla zawodników, trenerów i administratorów klubu. Portal webowy oraz aplikacja mobilna będą korzystać ze wspólnego backendu i wspólnego API.

Na początek wybieramy architekturę **modularnego monolitu**. Oznacza to jedną aplikację serwerową, ale z wyraźnie oddzielonymi modułami domenowymi. Pozwala to zachować prostotę wdrożenia i jednocześnie uniknąć splątania całego kodu.

Nie rozpoczynamy od mikroserwisów.

## 2. Schemat logiczny

```text
                UŻYTKOWNICY
        przeglądarka / PWA / Android
                      |
                 HTTPS / API
                      |
             APLIKACJA BACKEND
          (modularny monolit Laravel)
                      |
   +------------------+------------------+
   |                  |                  |
Konta i role       Akademia            Szachy
   |             kursy, lekcje       partie, PGN,
Profile          zadania             FEN, reguły
Grupy                |                  |
   +------------------+------------------+
                      |
              ANALIZA SZACHOWA
              kolejka zadań
                      |
                  Stockfish
                      |
                 PostgreSQL
                      |
          kopie zapasowe / monitoring
```

## 3. Warstwy systemu

### 3.1 Frontend
Odpowiada za interfejs użytkownika:
- dashboard zawodnika;
- panel trenera;
- panel administratora;
- szachownicę i widok analizy;
- responsywną obsługę telefonu, tabletu i komputera.

Wstępna propozycja: React + TypeScript. Ostateczny wybór potwierdzimy po uruchomieniu minimalnego prototypu.

### 3.2 Backend i API
Backend odpowiada za:
- logowanie i autoryzację;
- reguły biznesowe;
- sprawdzanie uprawnień;
- zapisywanie partii i wyników;
- obsługę kursów, lekcji i zadań;
- udostępnianie danych frontendowi przez wersjonowane API.

Wstępna propozycja: Laravel/PHP. API powinno mieć prefiks wersji, np. `/api/v1/`.

### 3.3 Baza danych
Wstępna propozycja: PostgreSQL. Baza przechowuje dane kont, grup, lekcji, zadań, partii, analiz i postępów.

Frontend nie łączy się bezpośrednio z bazą. Dostęp odbywa się wyłącznie przez backend.

### 3.4 Silnik szachowy
Stockfish komunikuje się z modułem analizy przez protokół UCI. Analizy dłuższe i analizy całych partii będą wykonywane jako zadania w tle.

Nie wolno blokować zwykłego żądania HTTP na czas długiej analizy.

### 3.5 Kolejka zadań
Kolejka obsługuje operacje, które mogą trwać dłużej:
- analiza partii;
- generowanie raportów;
- import większej liczby partii;
- wysyłanie powiadomień.

Technologię kolejki dobierzemy do docelowego hostingu.

## 4. Granice modułów

1. **Identity** — konta, logowanie, role, uprawnienia.
2. **Club** — zawodnicy, trenerzy, grupy, przypisania.
3. **Chess Core** — pozycje, legalność ruchów, PGN, FEN.
4. **Games** — partie treningowe i zapis przebiegu.
5. **Engine Analysis** — kolejka Stockfish, wyniki i warianty.
6. **Academy** — kursy, lekcje, zadania i próby rozwiązania.
7. **Progress** — aktywność, postępy i osiągnięcia.
8. **Coach Workspace** — narzędzia trenera, komentarze i przydziały.
9. **Integrations** — zewnętrzne źródła partii, uruchamiane w późniejszym etapie.
10. **Administration** — ustawienia i operacje administracyjne.

Moduły mogą korzystać ze wspólnych usług, ale nie powinny bezpośrednio modyfikować wewnętrznych danych innych modułów bez jasno określonych interfejsów.

## 5. Przepływ analizy partii

1. Zawodnik lub trener przesyła plik PGN albo wybiera zapisaną partię.
2. Backend sprawdza format, poprawność i uprawnienia.
3. Partia zostaje zapisana.
4. Backend umieszcza analizę w kolejce.
5. Worker uruchamia Stockfish i wysyła pozycje przez UCI.
6. Wyniki analizy są zapisywane wraz z wersją silnika i parametrami.
7. Użytkownik otrzymuje informację o zakończeniu analizy.
8. Frontend prezentuje ocenę, warianty i komentarze edukacyjne.

Każda analiza powinna mieć status, np. `pending`, `running`, `completed`, `failed`. Nieudane zadanie musi dać się bezpiecznie ponowić.

## 6. Bezpieczeństwo i dostęp

- Wszystkie połączenia produkcyjne przez HTTPS.
- Autoryzacja i kontrola uprawnień po stronie backendu.
- Zasada minimalnych uprawnień.
- Trener widzi wyłącznie dane zgodne z przydzielonym zakresem.
- Zawodnik widzi własne dane oraz treści udostępnione przez klub.
- Rodzic, jeśli funkcja zostanie wdrożona, widzi tylko prawidłowo powiązane konto dziecka i wyłącznie dozwolony zakres.
- Sekrety przechowujemy poza repozytorium, w zmiennych środowiskowych lub bezpiecznym magazynie.
- Dane testowe są fikcyjne.

Szczegółowy model prywatności, retencji i zgód wymaga uzgodnienia z klubem przed pilotażem.

## 7. Środowiska

Docelowo utrzymujemy oddzielne środowiska:
- **development** — lokalna praca nad kodem;
- **staging** — testy przed wydaniem;
- **production** — system używany przez klub.

Dane produkcyjne nie mogą być kopiowane do development bez anonimizacji i wyraźnej potrzeby.

## 8. Wdrożenie i przenośność

System ma być możliwy do uruchomienia na standardowym serwerze Linux. Konfiguracja środowiska, migracje bazy i proces wdrożenia będą opisane w dokumentacji.

Przed wyborem hostingu sprawdzimy:
- obsługę wymaganej wersji PHP;
- możliwość uruchamiania procesów worker;
- dostęp do SSH;
- obsługę PostgreSQL;
- limity CPU/RAM i procesów;
- możliwość uruchomienia Stockfish;
- kopie zapasowe i monitoring.

Zwykły hosting współdzielony może nie spełnić wymagań analizy Stockfish w tle. Nie kupujemy hostingu, dopóki nie zweryfikujemy wymagań prototypu.

## 9. Decyzje do potwierdzenia

- dokładne wersje React, Laravel, PHP i PostgreSQL;
- biblioteka szachownicy i biblioteka reguł szachowych;
- sposób uruchamiania Stockfish (serwer, WebAssembly lub oba);
- technologia kolejki;
- sposób uwierzytelniania aplikacji PWA/Android;
- docelowy hosting i strategia kopii zapasowych.

## 10. Kryterium poprawności architektury

Architektura jest wystarczająca na pierwszy etap, jeśli pozwala:
- uruchomić system lokalnie według instrukcji;
- oddzielić role i dane użytkowników;
- rozgrywać i zapisywać partie;
- analizować partie asynchronicznie;
- rozwijać akademię i panel trenera bez przepisywania fundamentów;
- przenieść system na inny serwer bez zmiany logiki biznesowej.
