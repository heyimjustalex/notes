### Key-Value DB - wykład 2
#### Charakterystyka
- przechowuje pary klucz-wartość
- stosuje podejście black-box, bo nie "rozumie" wartości które przechowuje
- wysoka odporność, skalowalność, wydajność
- wydajność i skalowalność osiągana poprzez dystrybucję na kilka serwerów
- implementacja w oparciu o distributed hash-table

W klasycznej bazie klucz-wartość baza przechowuje wartości identyfikowane przez klucze. Baza danych nie jest w stanie zinterpretować zawartości wartości (jedynie przechowuje ją i zwraca).
Jak każda baza nierelacyjna baza klucz-wartość jest nacelowana na rozproszenie. Efektywne rozproszenie wartości pomiędzy wiele serwerów zazwyczaj osiągane jest przez użycie funkcji mieszającej (hash function).

Bazę klucz-wartość można wyobrazić sobie jako tablicę z dwoma kolumnami. Zazwyczaj są jednak zrealizowane inaczej.

#### Operacje na bazie klucz-wartość

![[Pasted image 20221011113917.png | 500]]

Taka baza ma dwa wyzwania:
- sprawdzenie czy klucz istnieje
- zwracanie wartości znajdującym się pod danym kluczem

Najgorszym przypadkiem O(n) jest przeszukanie całej tablicy w poszukiwaniu klucza.

#### Hashowanie
Do adresowania tablicy używamy funkcji hashujących. W większości przypadków sprawia to, że czas dostępu do tablicy jest stały O(1).

Ponieważ zbiór kluczy jest nieskończony, a wielkość tablicy już tak, to występują konflikty.

![[Pasted image 20221011114533.png]]

Występowanie konfliktów może wydłużać czas pobierania wartości, ale sam konflikt daje się rozwiązać. Konflikt występuje, gdy więcej niż jeden klucz otrzymuje jakiś adres.

#### Sposoby rozwiązywania konfliktów
-  **obliczanie alternatywnego adresu** w przypadku trafienia na zajęte miejsce w tabeli. Nowy adres obliczany jest w sposób deterministyczny, aby można było go odtworzyć podczas wyszukiwania elementów.

![[Pasted image 20221011114737.png|300]]

- **użycie kubełków (buckets)** - w takim przypadku adresowana tablica nie zawiera zapisywanej pary klucz-wartość, a jedynie wskaźnik na kubełek, w którym jest ona zapisana. Taka organizacja pozwala na łączenie kubełków w listy i w efekcie pozwala na zapisanie wielu par klucz-wartość pod jednym adresem.

![[Pasted image 20221011114831.png | 300]]

#### Przetwarzanie wartości
Baza danych nie jest w stanie zinterpretować zawartości wartości (jedynie przechowuje ją i zwraca). Oznacza to, że wartość może być dowolnego typu, ale również to, że wartość można pobrać jedynie podając klucz, pod którym jest przechowywana. Baza danych nie próbuje rozpoznawać struktury zapisanych wartości: nie ma więc możliwości wyszukiwania wartości przy użyciu ich zawartości (lub części), nie ma też możliwości edycji przechowywanej wartości (dodania dodatkowej treści, usunięcia/zmiany wybranej części wartości); wartość może być zmieniona tylko przez nadpisanie jej nową wartością.

#### Zastosowanie
- **Przechowywanie sesji użytkownika** - jego profilu, wiadomości, targetowanych promocji. Mniejszy koszt "per page" niż RDB

	Przykładowe zastosowania baz klucz-wartość to przechowywanie sesji użytkownika: w przypadku aplikacji sieciowych możemy mieć ogromną liczbę użytkowników, każdy z informacjami przypisywanymi do jego sesji i zazwyczaj pobieranymi łącznie. Jednocześnie nie istnieją zależności pomiędzy sesjami różnych użytkowników. 

- **Przechowywanie shopping cart na stronie** - strony e-commerce mogą przechowywać distributed informacje o użytkownikach i w ten sposób obsługiwać miliony klientów.

	W przypadku sklepów internetowych użycie bazy klucz-wartość jest warte rozważenia. Przyjmując duży ruch w sklepie i zakładając, że większość koszyków zostanie porzucona (ostatecznie nie zostanie dokonany zakup), obciążanie bazy relacyjnej zapamiętywaniem zawartości koszyków wydaje się niepotrzebne.
	
####  Redis
- Baza klasyfikowana jako key-value mimo, że twórcy utrzymują inaczej
- Redis jest bazą przechowującą dane w pamięci operacyjnej, ale może zapisywać je na dysk w celu zapewnienia trwałości. W przypadku rezygnacji z zapisów na dysk baza może służyć jako sieciowy cache.
- Redis posiada również funkcjonalność brokera wiadomości. Redis jest w stanie interpretować i modyfikować zawartość wartości, choć dostęp zawsze odbywa się z użyciem klucza.
- Ma też cechy baz nierelacyjnych takie jak rozproszenie i wysoka dostępność.
- Baza Redis jest dostarczana z tekstowym klientem redis-cli, ale istnieją klienty graficzne od innych dostawców.

#### Operacje REDIS
- Podstawowe operacje w bazie klucz wartość to to wstawienie do bazy pary klucz-wartość
- Dodając parę z innym kluczem, zostanie ona dodana jako druga wartość. Para z takim samym kluczem nadpisze starą wartość nowo podaną. SET "AGE" "35"
- Z bazy można również pobrać ostatnio ustawioną wartość dla zadanego klucza. -> GET "Name"

#### Działanie

Redis działa jako serwer sieciowy. Może to być jedna maszyna albo wiele.

![[Pasted image 20221011120053.png |400]] 
![[Pasted image 20221011120126.png|400]]

#### Replikacja
##### Master-slave

W przypadku użycia wielu serwerów można zdecydować się na replikację. W replikacji występuje węzeł (serwer) główny do którego podłączają się węzły podrzędne (repliki), które przechowują te same dane co węzeł główny.

![[Pasted image 20221011120232.png|400]]

- konfiguracja master-slave
- slave może mieć kolejnych slaves
- brak węzła zastępczego dla mastera w przypadku awarii (no-failover)
- replikacja asynchroniczna
- force synchronizacji z wykorzystaniem komendy WAIT
- klient może zapisywać do replik w przypadku awarii mastera, ale takie zmiany nie są propagowane

W takiej konfiguracji w przypadku awarii węzła głównego baza będzie posiadać same węzły podrzędne do momentu interwencji administratora. Synchronizacja domyślnie jest asynchroniczna, ale polecenie WAIT wymusi synchronizację replik.
Możliwy jest zapis do replik, ale takie zapisy nie zostaną powielone na żaden inny węzeł (nawet do replik podłączonych do tej, do której zapisano wartość). Ponadto taka wartość w dowolnej chwili może zostać z repliki usunięta.

##### Cluster

Bardziej zaawansowaną opcją jest klaster Redis. Pozwala on nie tylko na powielanie (replikację) danych ale również na rozdzielenie danych pomiędzy różne węzły (wiele węzłów głównych).

![[Pasted image 20221011140843.png | 400]]

- automatycznie dzieli dane pomiędzy węzły
- dane dzielone na 16384 sloty
- hash slot'y są przypisane do node'ów (mogą być przenoszone)
- automatyczny master failover
- automatyczna replika przy migracji
- dużo komend i operacji na kluczach dostępnych tylko na pojedynczym węźle (ale hash tagi są dostępne cokolwiek to znaczy)
- liczba węzłów jest zmienna

Awaria węzła master powoduje wypromowanie slave do nowego mastera (slave przechowuje kopie mastera).
Ponadto jeśli liczba replik poszczególnych węzłów głównych różni się o więcej niż jeden następuje automatyczne rebalansowanie (migracja) replik.

![[Pasted image 20221011141143.png | 400]]

W przypadku klastra Redis większość operacji na wielu kluczach wykona się jedynie, jeśli wszystkie klucze będą na jednym węźle (np. SUNION). Problem ten jest jednak ograniczony przez mechanizm hash tags. Dzięki temu mechanizmowi można sterować do którego hash slot trafi zestaw wartości. Wszystkie wartości, które mają trafić do jednego hash slotu powinny mieć taki sam podciąg w kluczu objęty w nawiasy klamrowe ({}).
W klastrze Redis liczba węzłów podczas działania klastra może być zmieniana.

#### Persistence
Zapewnienie trwałości danych poprzez odczyt i zapis danych na dysku. Istnieją dwa modele:
##### RDB
RDB persistence polega na okresowym zapisie snapshota bazy

Cechy:
- prosty w użyciu
- snapshot co pewien okres czasu sprawia, że pewne dane są tracone
- może być czasochłonne przy dużych danych (snaspshotach dużych baz)

Łatwość stosowania przy tworzeniu kopii zapasowych wynika z faktu, że wystarczy przegrać jeden, najnowszy plik.
Długi czas wykonania migawki może sprawić, że baza przestanie na jakiś czas odpowiadać na zapytania.

##### AOF 
Append-only file - każda operacja zmieniająca stan bazy zapisywana w pliku log, a co jakiś czas ten log file jest chyba zapisywany, albo robiona z niego jakaś migawka jak w procesie RDB

Istnieją tutaj trzy polityki **appendfsync**:
- always
- everysec
- no

Przy wybranej polityce appendfsync zapisy powinny być tak samo szybkie jak RDB. Domyślna polityka everysec zapewnia nadal dobrą wydajność (nowe zapisy odbywają się na bieżąco jeśli akurat nie odbywa się synchronizacja).

#### Redis data model
##### Cechy
 - implementuje klucze proste
 - implementuje różne typy danych na których można przeprowadzać operacje
 - wszystkie wartości są adresowane przez swoje klucze
 - model "Grey box"

##### Klucze
- brak wsparcia dla kluczy complex
- binary-safe strings - każda sekwencja binarna może być użyta jako klucz np. tekst lub obrazek PNG
- maksymalna wielkość 512MB
- można używać separatorów, ale są one tylko konwencją nazewniczą

![[Pasted image 20221011143717.png|300]]

Separatory są jedynie konwencją nazewniczą i nie mają wpływu na to, jak Redis traktuje klucze (wyjątkiem są hash tags).

##### Zasady tworzenia kluczy
- unikaj dużych kluczy, bo obciąża to pamięć i zwiększa processing time
- unikaj bardzo krótkich kluczy, ich skracanie nie wpływa bardzo na performance, a zmniejsza czytelność

 ![[Pasted image 20221011144105.png|300]]
- stosuj naming schema, bo to upraszcza operacje na bazie

- hastagi mogą być używane, żeby przypisać klucze do tego samego hash slota

![[Pasted image 20221011144226.png | 300]]
Zalecenia dotyczące tworzenia kluczy. Dla dwóch kluczy podanych w przykładzie, trzeba podać cały klucz, żeby pobrać wartość, ale tylko hash tag („user:1000”) będzie decydował do jakiego hash slotu (a więc i na który węzeł główny) trafią oba klucze.

