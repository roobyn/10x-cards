# Dokument wymagań produktu (PRD) - 10xCards

## 1. Przegląd produktu
10xCards jest webową aplikacją do tworzenia i nauki fiszek opartą na metodzie spaced repetition. Celem produktu jest skrócenie czasu potrzebnego na przygotowanie materiałów do nauki poprzez automatyzację generowania fiszek z dowolnego tekstu oraz udostępnienie prostych narzędzi do ich późniejszego przeglądu, edycji i wykorzystania w sesjach powtórek. Docelowi użytkownicy to studenci, profesjonaliści przygotowujący się do egzaminów oraz osoby uczące się języków, które korzystają z fiszek, ale zniechęca je ręczne przygotowywanie zawartości.

Kluczowe założenia produktu:
- Domyślny przepływ zakłada wklejenie tekstu źródłowego (1000–10000 znaków), ustawienie liczby fiszek i ich generację przez AI.
- Aplikacja udostępnia alternatywę w postaci ręcznego tworzenia pojedynczych fiszek.
- Wszystkie fiszki są przechowywane w jednej puli powiązanej z kontem użytkownika; integracja z gotowym algorytmem powtórek umożliwia prowadzenie sesji nauki.
- Projekt startuje jako rozwiązanie webowe MVP bez aplikacji mobilnych i bez rozbudowanych integracji.

Główne komponenty rozwiązania:
- Frontend webowy (interfejs generowania, listy fiszek, widok sesji nauki, zarządzanie kontem).
- Backend API obsługujące konta użytkowników, generowanie i zapis fiszek, logowanie zdarzeń, sesje nauki oparte na zewnętrznej bibliotece spaced repetition.
- Baza danych przechowująca konta, fiszki, historię sesji i logi akceptacji.
- Usługa AI odpowiedzialna za generowanie fiszek na podstawie wklejonego tekstu.

Założenia projektowe:
- Projekt prowadzony jest w trybie MVP, bez określonego harmonogramu kamieni milowych.
- Jakość wygenerowanej treści zależy od przyjętego modelu AI; dodatkowe mechanizmy kontroli jakości nie są planowane w MVP.
- Analiza metryk będzie realizowana manualnie poprzez zapytania SQL na bazie logów.

## 2. Problem użytkownika
Ręczne tworzenie fiszek jest czasochłonne i często stanowi barierę w regularnym korzystaniu z metody spaced repetition. Użytkownicy dysponują materiałami w formie tekstowej, ale muszą je ręcznie przetworzyć na pytania i odpowiedzi, co wydłuża przygotowanie planu nauki i osłabia motywację. Brakuje im narzędzia, które automatycznie wygeneruje wysokiej jakości fiszki na podstawie dostarczonego materiału, jednocześnie pozwalając na pełną kontrolę nad efektem, jego korekty i zarządzanie fiszkami w czasie.

Kluczowe bolączki:
- Nadmierny nakład pracy przy tworzeniu nowych fiszek przed każdą sesją nauki.
- Brak szybkiej weryfikacji jakości wygenerowanych pytań i odpowiedzi.
- Konieczność manualnego zarządzania dużymi listami fiszek i ich powtórkami.

## 3. Wymagania funkcjonalne

### 3.1 System kont użytkowników
- Zakładanie konta na podstawie adresu email, hasła i podstawowych preferencji nauki (np. domyślna liczba fiszek generowanych przez AI).
- Logowanie z użyciem loginu i hasła, stworzenie sesji użytkownika oraz możliwość ręcznego wylogowania.
- Przechowywanie danych w bazie z hasłami zahashowanymi; brak funkcji resetu hasła w MVP.
- Możliwość aktualizacji podstawowych preferencji użytkownika (np. domyślne limity fiszek, język interfejsu) bez wpływu na istniejące fiszki.

### 3.2 Generowanie fiszek przez AI
- Formularz umożliwiający wklejenie tekstu o długości 1000–10000 znaków oraz wybór liczby fiszek (maksymalnie 20).
- Walidacje po stronie klienta i serwera obejmujące długość tekstu, liczbę fiszek oraz ograniczenia 200 znaków dla części pytaniowej i 500 znaków dla odpowiedzi.
- Przekazanie znormalizowanego tekstu i parametrów do usługi AI generującej fiszki.
- Prezentacja wyników w widoku przeglądu zawierającym listę fiszek z możliwością natychmiastowej edycji, odrzucenia lub zaakceptowania każdej karty indywidualnie.

### 3.3 Manualne tworzenie i edycja fiszek
- Formularz do ręcznego tworzenia pojedynczej fiszki z polami "góra" i "dół", objętymi tymi samymi ograniczeniami znaków co fiszki wygenerowane.
- Widok szczegółowy fiszki pozwalający na edycję treści ("góra", "dół") oraz podgląd metadanych (źródło AI/manual, data utworzenia, data modyfikacji, status).
- Zachowanie historii zmian w bazie lub sygnowanie ostatniej modyfikacji w logach.

### 3.4 Zarządzanie listą fiszek
- Widok listy prezentujący wszystkie fiszki należące do użytkownika, z możliwością filtrowania (wyszukiwanie tekstowe po polach "góra" i "dół"), sortowania po dacie utworzenia lub ostatniej modyfikacji oraz paginacji przy dużych zbiorach.
- Możliwość zaznaczenia fiszek i ich zbiorczego usunięcia z poziomu listy, przy czym edycja odbywa się wyłącznie w widoku szczegółowym.
- Link do szczegółów fiszki pozwalający na przejście do edycji.

### 3.5 Sesje nauki i integracja z algorytmem
- Możliwość rozpoczęcia sesji nauki z wybranym zbiorem fiszek (np. wszystkimi lub filtrowanymi według statusu).
- Integracja z zewnętrzną biblioteką spaced repetition, która przyjmuje dane fiszek i zwraca kolejność oraz wynik sesji.
- Widok sesji prezentujący aktualną fiszkę, liczbę pozostałych kart oraz przyciski ocen zapamiętania zgodne z biblioteką (skala zostanie doprecyzowana po wyborze rozwiązania).
- Automatyczne przejście do kolejnej fiszki po udzieleniu oceny, z możliwością ręcznej nawigacji do poprzednich lub kolejnych kart w razie pomyłki.
- Zapis wyników sesji i historii powtórek w bazie danych, w tym oznaczenie sesji jako ukończonej nawet po przerwaniu.

### 3.6 Logowanie zdarzeń i raportowanie
- Logowanie każdej akcji akceptacji lub odrzucenia fiszek wraz z identyfikatorem użytkownika, źródłem fiszki (AI/manual), znacznikiem czasu oraz tekstem źródłowym.
- Rejestrowanie liczby utworzonych fiszek (manualnie i AI) oraz wyników sesji w sposób umożliwiający manualne raportowanie wskaźników sukcesu.
- Przygotowanie struktury tabel logów umożliwiającej ręczne zapytania SQL (np. widoki akceptacji, udziału AI, czasu tworzenia fiszek).

## 4. Granice produktu
- MVP nie obejmuje tworzenia własnego, zaawansowanego algorytmu powtórek; produkt korzysta z gotowej biblioteki.
- Brak importu materiałów w formatach innych niż prosty tekst wklejony do formularza.
- Brak funkcji współdzielenia fiszek między kontami użytkowników.
- Brak integracji z innymi platformami edukacyjnymi oraz brak API publicznego na etapie MVP.
- Brak aplikacji mobilnych; interfejs responsywny web stanowi jedyny kanał dostępu.
- Brak resetu hasła i mechanizmów odzyskiwania konta w pierwszej wersji.
- Brak dodatkowej moderacji treści generowanych przez AI poza edycją użytkownika.

## 5. Historyjki użytkowników

### US-001. Rejestracja konta
Tytuł: Rejestracja nowego użytkownika
Opis: Jako nowy użytkownik chcę utworzyć konto przy użyciu adresu email i hasła, aby móc zapisywać moje fiszki i postępy.
Kryteria akceptacji:
- Formularz rejestracji wymaga unikalnego adresu email oraz hasła spełniającego minimalne wymagania zdefiniowane przez produkt.
- Hasło jest przechowywane w bazie w formie hashu zgodnego z najlepszymi praktykami bezpieczeństwa.
- Po rejestracji użytkownik jest automatycznie logowany i przekierowany do pulpitu głównego.
- W przypadku błędów walidacji lub zajętego emaila system wyświetla jednoznaczny komunikat i pozwala poprawić dane.

### US-002. Logowanie i wylogowanie
Tytuł: Bezpieczne logowanie do konta
Opis: Jako zarejestrowany użytkownik chcę logować się i wylogowywać, aby mieć bezpieczny dostęp do moich fiszek.
Kryteria akceptacji:
- Logowanie wymaga poprawnego adresu email i hasła; błędne dane generują czytelny komunikat bez ujawniania, które pole jest niepoprawne.
- Po zalogowaniu system tworzy sesję użytkownika i umożliwia dostęp do fiszek powiązanych z kontem.
- Użytkownik może w dowolnym momencie zakończyć sesję poprzez wylogowanie, co usuwa sesję po stronie serwera i klienta.
- Niedawno wygasła sesja powoduje przekierowanie do formularza logowania przy próbie dostępu do chronionych widoków.

### US-003. Aktualizacja preferencji konta
Tytuł: Zarządzanie podstawowymi preferencjami
Opis: Jako zalogowany użytkownik chcę ustawić domyślne preferencje generowania (np. liczba fiszek, język interfejsu), aby przyspieszyć kolejne sesje tworzenia.
Kryteria akceptacji:
- Widok profilu wyświetla aktualne preferencje oraz umożliwia ich modyfikację.
- Zmiana preferencji jest zapisywana w bazie i domyślnie wypełnia formularze generowania lub manualnego tworzenia fiszek.
- Aktualizacja preferencji nie modyfikuje istniejących fiszek ani historii sesji.
- Nieprawidłowe wartości są odrzucane z komunikatem wyjaśniającym wymagany zakres.

### US-004. Generowanie fiszek z tekstu
Tytuł: Automatyczne tworzenie fiszek przez AI
Opis: Jako zalogowany użytkownik chcę wkleić tekst i otrzymać wygenerowane fiszki, aby szybciej przygotować materiał do nauki.
Kryteria akceptacji:
- Formularz przyjmuje tekst o długości 1000–10000 znaków i parametr liczby fiszek od 1 do 20.
- System waliduje długość pól i informuje o przekroczeniu limitu z konkretnym komunikatem.
- Po zatwierdzeniu żądania aplikacja wysyła dane do usługi AI i wyświetla widok przeglądu z wygenerowanymi fiszkami w ciągu akceptowalnego czasu (np. do 10 sekund).
- Każda fiszka zawiera pola "góra" (max 200 znaków) i "dół" (max 500 znaków) oraz oznaczenie, że pochodzi z AI.

### US-005. Przegląd i edycja propozycji AI
Tytuł: Kuracja wygenerowanych fiszek
Opis: Jako użytkownik chcę przejrzeć wygenerowane fiszki, edytować ich treść i zdecydować, które zapisać, aby zapewnić wysoką jakość materiału.
Kryteria akceptacji:
- Widok przeglądu pozwala edytować pola "góra" i "dół" każdej fiszki przed zapisaniem.
- Użytkownik może zaakceptować, odrzucić lub edytować każdą fiszkę indywidualnie.
- Akceptacja zapisuje fiszkę w bazie i loguje zdarzenie z metadanymi (źródło AI, timestamp, ID użytkownika, tekst źródłowy).
- Odrzucenie usuwa fiszkę z bieżącej propozycji i loguje decyzję odrzucenia.

### US-006. Manualne tworzenie fiszki
Tytuł: Ręczne dodawanie karty
Opis: Jako użytkownik chcę ręcznie utworzyć pojedynczą fiszkę, gdy potrzebuję pełnej kontroli nad treścią.
Kryteria akceptacji:
- Formularz manualny zawiera pola "góra" i "dół" z walidacją limitów znaków (200 i 500).
- Po zapisaniu fiszka otrzymuje oznaczenie źródła manual i pojawia się na liście fiszek.
- W przypadku niepoprawnych danych formularz informuje, które pola wymagają poprawy.
- Zapis fiszki loguje zdarzenie utworzenia wraz z danymi wymaganymi do analizy udziału AI.

### US-007. Lista fiszek z wyszukiwaniem i paginacją
Tytuł: Przegląd wszystkich fiszek
Opis: Jako użytkownik chcę przeglądać listę moich fiszek z wyszukiwaniem, sortowaniem i paginacją, aby łatwo znaleźć potrzebne karty.
Kryteria akceptacji:
- Widok listy prezentuje fiszki w kolejności od najnowszych z możliwością sortowania po dacie dodania lub modyfikacji.
- Pole wyszukiwania filtruje fiszki po tekście pola "góra" i "dół" w czasie rzeczywistym lub po zatwierdzeniu.
- Paginacja umożliwia przechodzenie między stronami przy dużej liczbie fiszek; zmiana strony zachowuje aktywne filtry.
- Każdy wiersz listy zawiera skrócony podgląd fiszki, informację o źródle (AI/manual) i link do widoku szczegółowego.

### US-008. Edycja fiszki w widoku szczegółowym
Tytuł: Modyfikacja istniejącej karty
Opis: Jako użytkownik chcę edytować istniejącą fiszkę z poziomu jej szczegółów, aby poprawić treść po zapisaniu.
Kryteria akceptacji:
- Widok szczegółowy pokazuje pełne pola "góra" i "dół", metadane oraz przycisk edycji.
- Edycja wymaga potwierdzenia i podlega tym samym limitom znaków co tworzenie fiszek.
- Po zapisaniu zmian fiszka aktualizuje się w bazie i na liście, zaktualizowane zostają metadane (ostatnia modyfikacja).
- Zdarzenie edycji jest logowane wraz z identyfikatorem użytkownika i powodem (jeśli podany).

### US-009. Usuwanie fiszek z listy i szczegółu
Tytuł: Usuwanie niepotrzebnych kart
Opis: Jako użytkownik chcę usuwać fiszki zarówno z poziomu listy, jak i widoku szczegółowego, aby utrzymać aktualność bazy kart.
Kryteria akceptacji:
- Na liście fiszek dostępna jest opcja usunięcia pojedynczej karty z potwierdzeniem akcji.
- Widok szczegółowy zawiera przycisk usuń z dodatkowym potwierdzeniem.
- Po usunięciu fiszka znika z listy, nie jest dostępna w sesjach i zdarzenie usunięcia jest logowane.
- Próba usunięcia fiszki używanej w aktywnej sesji powoduje komunikat o konieczności zakończenia sesji lub potwierdzenie przerwania.

### US-010. Rozpoczęcie sesji nauki
Tytuł: Start sesji powtórek
Opis: Jako użytkownik chcę rozpocząć sesję nauki na podstawie wybranych fiszek, aby przećwiczyć materiał zgodnie z metodą spaced repetition.
Kryteria akceptacji:
- Użytkownik może wybrać zakres fiszek do sesji (np. wszystkie, wynik wyszukiwania, oznaczone jako nowe).
- System przekazuje wybrane fiszki do biblioteki spaced repetition i inicjuje sesję z kolejnością wygenerowaną przez bibliotekę.
- Widok sesji wyświetla liczbę kart pozostałych do nauki oraz bieżącą fiszkę.
- Przerwanie sesji zapisuje jej stan jako ukończony z oznaczeniem, że zakończyła się przed przejściem wszystkich kart.

### US-011. Ocena zapamiętania i nawigacja w sesji
Tytuł: Ocenianie fiszek podczas sesji
Opis: Jako użytkownik chcę oceniać stopień zapamiętania każdej fiszki i w razie potrzeby wracać do poprzednich kart, aby kontrolować przebieg sesji.
Kryteria akceptacji:
- Dla każdej fiszki dostępne są przyciski zgodne ze skalą biblioteki (np. Pamiętam, Częściowo, Nie pamiętam).
- Po udzieleniu oceny system automatycznie przechodzi do kolejnej fiszki zgodnie z algorytmem.
- Użytkownik może ręcznie przejść do poprzedniej fiszki lub powtórzyć ocenę, co loguje korektę.
- Wszystkie oceny są zapisywane wraz z timestampem, ID użytkownika i parametrami wymaganymi przez bibliotekę spaced repetition.

### US-012. Raportowanie i logi
Tytuł: Monitorowanie efektywności fiszek
Opis: Jako właściciel produktu lub analityk chcę mieć dostęp do logów akceptacji i udziału AI, aby ręcznie weryfikować wskaźniki sukcesu.
Kryteria akceptacji:
- System zapisuje logi akceptacji i tworzenia fiszek w strukturze umożliwiającej zapytania SQL filtrujące po dacie, użytkowniku i źródle.
- Dostępne jest minimum jedno zapytanie referencyjne lub widok ułatwiający obliczenie wskaźników 75% akceptacji i 75% udziału AI.
- Zdarzenia obejmują stan fiszki po sesji (np. ile razy została powtórzona, kiedy ostatnio oceniona).
- Uprawnieni użytkownicy mogą wykonać eksport logów (np. CSV) lub pobrać wyniki zapytania na własne potrzeby analityczne.

## 6. Metryki sukcesu
- Co najmniej 75% fiszek wygenerowanych przez AI zostaje zaakceptowanych przez użytkowników i zapisanych w systemie.
- Co najmniej 75% wszystkich nowych fiszek powstaje z wykorzystaniem generatora AI (w relacji do manualnie tworzonych).
- Średni czas od wklejenia tekstu do zapisania wygenerowanych fiszek nie przekracza 5 minut.
- Minimum 80% rozpoczętych sesji nauki jest kończonych (także tych przerwanych, które uznaje się za ukończone w logach).
- Liczba zgłoszeń błędów krytycznych związanych z generowaniem lub sesją nauki wynosi mniej niż 5% aktywnych użytkowników miesięcznie.

