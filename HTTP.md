## 1. Podstawy HTTP

- ### Wersje HTTP
#http_versions

![[Pasted image 20220926173913.png]]
HTTP/3 wykorzystuje UDP/[[QUIC]]
#QUIC 

- ### Pojęcia 
	- Request - zapytanie wysyłane przez klienta (przeglądarkę, lub program typu curl, burp)
	- Response - odpowiedź od serwera
	- Serwer może być jedną maszyną lub wieloma maszynami ukrytymi za pojedynczym proxy

- ### Założenia HTTP
	- prosty
	- rozszerzalny
	- bezstanowy
	
- ### Bezstanowość
	#stateless
	- Pomimo tego, że protokół transportowy może być stateful to sam HTTP jest stateless, bo każdy request HTTP zawiera pełnie informacji o kliencie.
	
	- Even though multiple requests can be sent over the same HTTP connection, the server does not attach any special meaning to their arriving over the same socket. **That is solely a performance thing, intended to minimize the time/bandwidth that'd otherwise be spent reestablishing a connection for each request**.
	
	- As far as HTTP is concerned, **they are all still separate requests and must contain enough information on their own to fulfill the request.** That is the essence of "statelessness". Requests will not be associated with each other absent some shared info the server knows about, which in most cases is a session ID in a cookie.
	
	- HTTP is called as a `stateless protocol` because each request is executed independently, without any knowledge of the requests that were executed before it, which means once the transaction ends the connection between the browser and the server is also lost. 

### Struktura HTTP
#http_structure

![[Pasted image 20220927174335.png]]


- ### Metody HTTP
#http_methods

![[Pasted image 20220927182043.png]]

Istnieją jeszcze metody OPTIONS, TRACE oraz CONNECT, ale nie mają one dużego zastosowania z punktu widzenia programisty aplikacji webowych.

- #### Put vs POST

Better is to choose between PUT and POST based on [idempotence](http://en.wikipedia.org/wiki/Idempotent) of the action.

**PUT** implies putting a resource - completely replacing whatever is available at the given URL with a different thing. By definition, a **PUT is idempotent**. Do it as many times as you like, and the result is the same. `x=5` is idempotent. You can PUT a resource whether it previously exists, or not (eg, to Create, or to Update)!

**POST** updates a resource, adds a subsidiary resource, or causes a change. A POST is not idempotent, in the way that `x++` is **not idempotent**.

![[Pasted image 20220927190005.png]]


- ### Nagłówki HTTP
  #http_headers #headers 

- Nagłówki HTTP **służą do przekazywania dodatkowych informacji między klientami a serwerem za pośrednictwem nagłówka żądania i odpowiedzi.** We wszystkich nagłówkach nie jest rozróżniana wielkość liter, pola nagłówków są oddzielone dwukropkami, pary klucz-wartość w formacie ciągu zwykłego tekstu. Koniec sekcji nagłówka oznaczony pustym nagłówkiem pola.
	
- Nagłówki HTTP spotkamy zarówno w zapytaniach, jak i w odpowiedziach. Są one pierwszymi liniami, oddzielone od ciała jedną pustą linią. **Nagłówki są opcjonalne — protokół nie wymaga ich obecności. Nagłówki to pewnego rodzaju metadane i polecenia wymieniane przez przeglądarkę i serwer** — mogą się w nich znaleźć informacje takie jak rodzaj przesyłanych treści (np. czy jest to obrazek czy plik JSON), sugestia dotycząca traktowania zawartości (czy przeglądarka ma wyświetlić daną treść, czy np. potraktować to jako pobieranie), jaki jest rozmiar przesyłanych danych, kiedy były modyfikowane, jakiego rodzaju odpowiedzi druga strona się spodziewa itp.

- Nagłówki przyjmują postać klucz-wartość, zapisywane w postaci: Klucz: wartość
- Najpierw jest klucz (przeważnie zaczyna się dużą literą, ale nie jest to wymagane), następnie dwukropek, spacja oraz wartość.

W przeważającej większości nagłówki są automatycznie ustawiane przez serwer i aplikacja nie musi ich modyfikować / uzupełniać. Są jednak sytuacje, w których możemy chcieć wysłać określony nagłówek, mamy wtedy taką możliwość.
	
- Nagłówki dzielą się na cztery rodzaje:
		- **General Header** - These header fields have general applicability for both request and response messages.
		- **Request Header** -These header fields have applicability only for request messages.
		- **Response Header** - These header fields have applicability only for response messages.
		- **Entity Header** -These header fields have applicability only for response messages.
	
	- Podstawowe nagłówki:
	
	![[Pasted image 20220927182628.png]]
![[Pasted image 20220927182702.png]]


### Statusy HTTP (kody odpowiedzi)
#status #codes #statusy #codes #response_codes

Jednym z elementów protokołu HTTP są kody odpowiedzi, zwanymi też statusami. To numeryczne, trzycyfrowe kody, które są dołączane do odpowiedzi i sygnalizują status odpowiedzi. Najpopularniejsze i najczęściej spotykane kody to 200 (OK, czyli wszystko jest w porządku, zapytanie jest obsłużone), 302 (przekierowanie), 403 (brak dostępu) oraz 404 (nie znaleziono — stąd liczba 404 często pojawia się na stronach z informacją, że wpisany adres nie istnieje).

Kodów jest mnóstwo, najczęściej wystarczy jednak znajomość tych podstawowych. Co ważne, nawet nie znając kodu można określić jego przybliżone znaczenie na podstawie numeru. Kody są bowiem podzielone na grupy, pierwsza cyfra kodu mówi nam, z której jest on grupy. I tak:

-   **1xx** — informacyjne, nieczęsto można spotkać, dotyczą bardziej środowiska niż samej aplikacji (np. 111 — serwer odrzucił połączenie)
-   **2xx** — zapytanie się powiodło
-   **3xx** — przekierowanie, zapytanie należy kierować pod inny adres / serwer
-   **4xx** — błąd aplikacji spowodowany działaniem użytkownika (np. wspomniany 404 — nie znaleziono — czy 403 — brak dostępu lub 400 — niepoprawnie zapytanie)
-   **5xx** — błąd serwera (np. nieobsłużony wyjątek w Javie)


### Ciało zapytania (ang. request body)
#request #body

Ciało zapytania to nic innego jak dane, które wysyłamy do serwera. **Dobrą praktyką jest to, aby nie dołączać ciała zapytania dla metod GET oraz DELETE**. Wynika to z faktu, że teoretycznie zapytania te powinny być pozbawione jakichkolwiek dodatkowych informacji dla serwera, choć standard tego wprost nie zabrania. Problem może pojawić się po stronie użytkowników lub bibliotek — wiele serwerów proxy (wykorzystywanych np. w firmach) i bibliotek (używanych przy pisaniu klientów i aplikacji korzystających z Twojego API) zakłada, że tego ciała nie ma, co może powodować problemy jeśli Twoja aplikacja go jednak oczekuje. **Z tego powodu lepiej używać go tylko w przypadku zapytań POST / PUT.**

Ciało metody może zawierać właściwie cokolwiek — jest to po prostu strumień danych, ale ponownie istnieje wiele konwencji. **Najważniejsza (i jednocześnie ustandaryzowana) to wykorzystanie nagłówka Content-Type aby ‘powiedzieć’ serwerowi w jakim formacie chcemy wysłać dane i jak należy je interpretować.** Sam format jak i dostępne opcje są określone w standardzie [RFC6838](https://tools.ietf.org/html/rfc6838) i pokrewnych (mniej techniczny opis z przykładami można także znaleźć na stronie [https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types), a aktualną listę na stronie [https://www.iana.org/assignments/media-types/media-types.xhtml](https://www.iana.org/assignments/media-types/media-types.xhtml)). My skupimy się na kilku najczęściej wykorzystywanych we współczesnych aplikacjach.

#popular_headers

#### application/json

Szczególnie popularny w ostatnich latach wraz z rozwojem aplikacji frontendowych oraz standaryzacją tego obszaru. JSON to skrót od JavaScript Object Notation i jest to nic innego jak reprezentacja obiektów w sposób zgodny z językiem JavaScript. Zastosowania wykraczają jednak znacznie poza język JavaScript — główne przyczyny to fakt, że taki sposób zapisu danych łatwo monitorować w sposób automatyczny, jest to zapis zrozumiały dla człowieka (a więc programista może w łatwy sposób testować i weryfikować swoje aplikacje), jest on względnie ustandaryzowany, jest raczej zwięzły (w porównaniu do np. XMLa) i bardzo dobrze się kompresuje z użyciem algorytmów używanych jako standardowe w sieci web (gzip itp). Przykładowe ciało zapytania może wyglądać tak:

```
{"strona": "kobietydokodu.pl", "ocena":"świetna"}
```

#### application/x‑www-form-urlencoded

**Ten rodzaj treści oznacza, że ciało metody zawiera dane (najczęściej przesłane z formularza) zapisane tak, jakbyśmy przesyłali je w adresie URL. To domyślny sposób wysyłania formularzy na stronach WWW, obecnie spotykany głównie w przypadku aplikacji czysto backendowych lub integracji aplikacji frontendowych ze starszymi serwisami.** Przykładowa treść ciała zapytania może wyglądać nastepująco:

```
strona=kobietydokodu.pl&ocena=rewelacyjna
```

Powiązanym rodzajem jest _multipart/form-data_ — wykorzystywany głównie do przesyłania plików w przypadku tradycyjnych formularzy WWW. Składnia ciała jest jednak bardziej skomplikowana i z tego powodu właściwie nie implementuje się jej wprost (ewentualnie w połączeniu z istniejącymi bibliotekami). Pozwala ona na przesłanie kilku różnych elementów o różnych typach w ramach jednego zapytania (stąd najczęściej jest wykorzystywana do przesyłania plików ze strony WWW na serwer).

### URI - Uniform Resource Identifier
#url #uri #urn
 - Używany w celu identyfikacji poszczególnych zasobów w internecie:
 
**Scheme** - pokazuje jaki protokół jest używany
**Authority** - domena
**Path** - ścieżka do rzeczy której szukamy
**Query** - zapytanie
**Fragment** - specyficzny fragment rzeczy której szukamy

 ##### scheme :// authority path ? query # fragment
 
![[URI_Venn_Diagram.svg.png | 500]]

**URN** ([ang.](https://pl.wikipedia.org/wiki/J%C4%99zyk_angielski "Język angielski") _Uniform Resource Name_) – ujednolicony format nazw zasobów (głównie [książek](https://pl.wikipedia.org/wiki/Ksi%C4%85%C5%BCka "Książka")), składający się z identyfikatora [przestrzeni nazw](https://pl.wikipedia.org/wiki/Przestrze%C5%84_nazw "Przestrzeń nazw") (ang. _Namespace IDentifier_, **NID**) i [łańcucha](https://pl.wikipedia.org/wiki/Tekstowy_typ_danych "Tekstowy typ danych") (ciągu znaków) specyficznego dla tej przestrzeni nazw (ang. _Namespace Specific String_, **NSS**). URN jest częścią [URI](https://pl.wikipedia.org/wiki/Uniform_Resource_Identifier "Uniform Resource Identifier") (ang. _Uniform Resource Identifier_).

**URL** ([ang.](https://pl.wikipedia.org/wiki/J%C4%99zyk_angielski "Język angielski") _Uniform Resource Locator_) – ujednolicony format adresowania (określania lokalizacji) zasobów (informacji, danych, usług) stosowany w [Internecie](https://pl.wikipedia.org/wiki/Internet "Internet") i w [sieciach lokalnych](https://pl.wikipedia.org/wiki/Lokalna_sie%C4%87_komputerowa "Lokalna sieć komputerowa"). 

Tak zwany adres URL najczęściej kojarzony jest z adresami stron [WWW](https://pl.wikipedia.org/wiki/World_Wide_Web "World Wide Web"), ale ten format adresowania służy do określania lokalizacji wszelkich zasobów dostępnych w Internecie.

![[Pasted image 20220927193640.png]]



![[mcTKf.jpg]]