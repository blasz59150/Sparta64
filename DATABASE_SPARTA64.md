# SPARTA 64 — DATABASE DESIGN v1.0

**Status:** wstępny model logiczny do przeglądu  
**Powiązane dokumenty:** `MASTER_PLAN.md`, `ARCHITECTURE.md`

## 1. Założenia

Baza danych ma przechowywać konta, strukturę klubu, treści edukacyjne, partie, analizy i postępy. Proponowany silnik: PostgreSQL.

Model jest logicznym punktem wyjścia. Przed utworzeniem migracji należy go zweryfikować na prototypie i doprecyzować wymagania dotyczące prywatności, retencji oraz zgód.

### Zasady
- Klucze główne: UUID.
- Daty i czas przechowywane w UTC; interfejs pokazuje czas lokalny.
- Wszystkie tabele biznesowe mają `created_at` i `updated_at`.
- Usuwanie danych użytkowników musi uwzględniać obowiązki prawne i historię audytową; nie stosujemy automatycznego kasowania bez przemyślanej polityki.
- Dane dzieci ograniczamy do niezbędnego minimum.
- W środowiskach testowych używamy danych fikcyjnych.

## 2. Główne encje

### 2.1 `users`
Konto logowania.
- `id` UUID
- `email` — nullable, unikalny jeśli podany
- `username` — unikalny identyfikator logowania lub nazwa użytkownika
- `password_hash`
- `status` — invited / active / suspended
- `email_verified_at` — nullable
- `last_login_at` — nullable
- `created_at`, `updated_at`

Nie przechowujemy haseł jawnie. Używamy mechanizmu haszowania haseł dostarczonego przez framework.

### 2.2 `roles` i `user_roles`
Role: `player`, `coach`, `administrator`, a w przyszłości `parent`.
- `roles`: `id`, `key`, `name`
- `user_roles`: `user_id`, `role_id`, opcjonalnie `scope_group_id`

Uprawnienia sprawdzane są po stronie backendu. Sama ukryta pozycja w menu nie stanowi zabezpieczenia.

### 2.3 `player_profiles`
Dane profilu zawodnika.
- `id`
- `user_id` — relacja do `users`
- `display_name`
- `birth_year` lub inna minimalna informacja wieku, jeśli rzeczywiście potrzebna
- `club_member_code` — opcjonalny identyfikator klubowy
- `joined_at`
- `profile_visibility`
- `created_at`, `updated_at`

Nie zakładamy zbierania pełnej daty urodzenia, adresu domowego ani innych danych wrażliwych, jeśli nie ma uzasadnionej potrzeby.

### 2.4 `coach_profiles`
Dodatkowe informacje o trenerze:
- `id`
- `user_id`
- `display_name`
- `active`
- `created_at`, `updated_at`

### 2.5 `groups` i `group_members`
Grupy treningowe i ich członkowie.
- `groups`: `id`, `name`, `description`, `active`
- `group_members`: `group_id`, `user_id`, `joined_at`, `left_at`
- opcjonalna tabela `group_coaches` do przypisywania trenerów do grup

### 2.6 `guardian_links` — etap późniejszy
Powiązanie konta rodzica/opiekuna z kontem zawodnika.
- `id`
- `guardian_user_id`
- `player_user_id`
- `status` — pending / verified / revoked
- `verified_at`
- `created_at`, `updated_at`

Powiązanie wymaga kontrolowanego procesu weryfikacji i określenia zakresu dostępu.

## 3. Szachy i partie

### 3.1 `games`
Jedna rozegrana lub zaimportowana partia.
- `id`
- `white_player_id` — nullable dla anonimowego przeciwnika/komputera
- `black_player_id` — nullable
- `source` — app / pgn_import / external
- `result` — 1-0 / 0-1 / 1/2-1/2 / *
- `termination` — nullable
- `initial_fen` — nullable
- `pgn`
- `played_at` — nullable
- `visibility`
- `created_by_user_id`
- `created_at`, `updated_at`

Dla partii z komputerem należy zapisać informację o przeciwniku, kolorze, poziomie i konfiguracji silnika, zamiast udawać, że komputer jest kontem użytkownika.

### 3.2 `game_players`
Opcjonalna, bardziej elastyczna tabela uczestników partii:
- `game_id`
- `user_id` — nullable dla komputera lub nieznanego przeciwnika
- `color` — white / black
- `opponent_type` — member / engine / external / unknown
- `opponent_label` — nullable

Przed implementacją zdecydujemy, czy wystarczą pola w `games`, czy potrzebny jest ten model. Nie utrzymujemy dwóch źródeł prawdy.

### 3.3 `game_analyses`
Jedna próba analizy partii.
- `id`
- `game_id`
- `status` — pending / running / completed / failed
- `engine_name`
- `engine_version`
- `engine_options` — JSON
- `depth` lub `time_limit_ms`
- `requested_by_user_id`
- `started_at`, `completed_at`
- `error_code` — nullable
- `created_at`, `updated_at`

### 3.4 `analysis_positions`
Wyniki analizy dla pozycji lub półruchu.
- `id`
- `analysis_id`
- `ply` — numer półruchu
- `fen`
- `evaluation_cp` — nullable, z określonym punktem widzenia
- `mate_in` — nullable
- `best_move_uci` — nullable
- `principal_variation_uci` — nullable
- `depth`
- `created_at`

Punkt widzenia oceny musi być jednoznaczny, np. zawsze z perspektywy białych. Wartości mata zapisujemy oddzielnie od centypionów.

### 3.5 `game_comments`
Komentarze trenera lub systemu:
- `id`
- `game_id`
- `author_user_id`
- `ply` — nullable
- `body`
- `visibility`
- `created_at`, `updated_at`

## 4. Akademia i zadania

### 4.1 `courses`
- `id`
- `title`
- `description`
- `level`
- `status` — draft / published / archived
- `created_by_user_id`
- `created_at`, `updated_at`

### 4.2 `lessons`
- `id`
- `course_id`
- `title`
- `content` — struktura treści, po ustaleniu formatu
- `position`
- `status`
- `created_at`, `updated_at`

### 4.3 `chess_puzzles`
Zadanie szachowe.
- `id`
- `title`
- `instructions`
- `initial_fen`
- `solution_pgn` lub uporządkowana lista ruchów UCI
- `difficulty`
- `topic` — np. mat w 2, widełki, związanie
- `hint_data`
- `explanation`
- `status`
- `created_by_user_id`
- `created_at`, `updated_at`

Rozwiązania nie mogą być zwracane uczniowi przed zakończeniem próby. Backend powinien udostępniać je tylko w odpowiednim momencie.

### 4.4 `lesson_puzzles`
Łączy zadania z lekcjami i ustala kolejność:
- `lesson_id`
- `puzzle_id`
- `position`

### 4.5 `assignments`
Przydział kursu, lekcji lub zadania zawodnikowi albo grupie:
- `id`
- `created_by_user_id`
- `target_type` — user / group
- `target_id`
- `content_type`
- `content_id`
- `due_at` — nullable
- `status`
- `created_at`, `updated_at`

Warto zweryfikować model relacji i kluczy obcych przy implementacji; nie należy polegać wyłącznie na luźnych identyfikatorach bez walidacji.

### 4.6 `puzzle_attempts`
Próba rozwiązania zadania:
- `id`
- `puzzle_id`
- `user_id`
- `assignment_id` — nullable
- `moves_uci` — JSON lub uporządkowane rekordy ruchów
- `is_correct`
- `hints_used`
- `duration_ms`
- `completed_at`
- `created_at`

## 5. Postępy i aktywność

### 5.1 `lesson_progress`
- `id`
- `lesson_id`
- `user_id`
- `status` — not_started / in_progress / completed
- `progress_percent`
- `started_at`
- `completed_at`
- `updated_at`

### 5.2 `activity_events`
Ograniczony dziennik zdarzeń potrzebny do statystyk:
- `id`
- `user_id`
- `event_type`
- `occurred_at`
- `metadata` — wyłącznie niezbędne, niesensytywne dane

Nie zapisujemy każdego kliknięcia bez potrzeby. Zakres i okres przechowywania zdarzeń wymagają ustalenia.

### 5.3 `achievements` i `user_achievements`
Odznaki i ich przyznania:
- `achievements`: `id`, `key`, `title`, `description`, `criteria`
- `user_achievements`: `user_id`, `achievement_id`, `awarded_at`

Kryteria osiągnięć muszą być jawne i nie mogą ujawniać prywatnych danych.

## 6. Administracja i audyt

### `audit_events`
Dziennik ważnych działań administracyjnych i zmian uprawnień:
- `id`
- `actor_user_id`
- `action`
- `target_type`
- `target_id`
- `occurred_at`
- `metadata` — bez haseł, tokenów i zbędnych danych osobowych

Dostęp do dziennika ograniczony do upoważnionych administratorów.

## 7. Najważniejsze relacje

```text
users 1---1 player_profiles
users 1---1 coach_profiles
users *---* roles                 (user_roles)
users *---* groups                (group_members)

games 1---* game_analyses
game_analyses 1---* analysis_positions
games 1---* game_comments

courses 1---* lessons
lessons *---* chess_puzzles       (lesson_puzzles)
users/groups 1---* assignments
users 1---* puzzle_attempts
users 1---* lesson_progress
users *---* achievements          (user_achievements)
```

## 8. Indeksy i integralność

W implementacji należy rozważyć indeksy dla:
- `users.username` i `users.email`;
- `group_members.user_id`, `group_members.group_id`;
- `games.played_at`, identyfikatorów uczestników;
- `game_analyses.game_id`, `game_analyses.status`;
- `analysis_positions.analysis_id, ply`;
- `assignments.target_type, target_id, due_at`;
- `puzzle_attempts.user_id, puzzle_id, completed_at`;
- `lesson_progress.user_id, lesson_id`.

Wszystkie relacje powinny mieć klucze obce i odpowiednie ograniczenia unikalności. Polityka usuwania rekordów (RESTRICT, CASCADE, soft delete) musi być ustalona świadomie dla każdej relacji.

## 9. Decyzje przed migracjami

1. Czy loginem będzie nazwa użytkownika, e-mail, czy oba warianty?
2. Czy zawodnik może należeć do wielu grup jednocześnie?
3. Czy partie są domyślnie prywatne, czy widoczne dla trenerów przypisanych do grupy?
4. Jak długo przechowujemy partie, próby i zdarzenia aktywności?
5. Czy rodzice otrzymają konta w pierwszym wydaniu, czy później?
6. Jaki format danych lekcji i zadań będzie najwygodniejszy dla trenerów?
7. Jakie dane są rzeczywiście konieczne do prowadzenia klubu?

## 10. Kryterium gotowości

Model danych jest gotowy do implementacji, gdy:
- trener może prowadzić grupę zawodników;
- zawodnik może wykonać zadanie i zapisać wynik;
- zawodnik może zapisać partię;
- trener może zobaczyć dozwolone partie i postępy;
- analiza silnika jest powiązana z konkretną partią i wersją silnika;
- uprawnienia i widoczność danych są testowane.
