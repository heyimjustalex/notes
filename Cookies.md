## Cookies

### Definicja
HTTP Cookie - (tłumaczone czasem jako plik cookie, w skrócie cookie) – **mały fragment tekstu, który serwis internetowy wysyła do przeglądarki i który przeglądarka wysyła z powrotem przy następnych wejściach na witrynę. Używane jest głównie do utrzymywania sesji** np. poprzez wygenerowanie i odesłanie tymczasowego identyfikatora po logowaniu. Może być jednak wykorzystywane szerzej poprzez zapamiętanie dowolnych danych, które można zakodować jako ciąg znaków. Dzięki temu użytkownik nie musi wpisywać tych samych informacji za każdym razem, gdy powróci na tę stronę lub przejdzie z jednej strony na inną.

### Funkcje cookies
- bycie zalogowanym po wyjściu ze strony (przechowywanie informacji o sesji)
- przechowywanie informacji o personalizacji użytkownika np. język czy ustawienia motywu
- ciasteczka są używane także do trackowania Cię między stronami

### Tworzenie ciasteczka
- Client-side - Za pomocą javascrpitu w document.cookie w drzewie DOM. Tutaj z poziomu konsoli przeglądarki:

![[Pasted image 20220928175555.png]]

- Server-side - poprzez Set-Cookie Header 
![[Pasted image 20220928180132.png]]
Tu dalej jest setfromsever=value

Tak wygląda response header od serwera:

![[Pasted image 20220928180325.png | 380]]


### Cechy cookies
- Wysyłane z każdym requestem (request to to co pochodzi od klienta)
- Mają scope'y
	- domain
	- path-scope
- Expires/Max-age - jeśli nie ustawisz tego parametru, to cookie zostanie zniszczone po zamknięciu przeglądarki
- Same-Site - parametr mówiący jak przekazywane mają być ciasteczka na stronę ( czy jeśli jest link na obcej stronie, to pozwalamy otworzyć tamtą stronę wysyłając jej ciasteczka (ciasteczka do innej domeny nie przejdą przez CORS))

### Cookies scope

#### Domain scope
Cookies które ustawiasz bez dodatkowych opcji są domyślnie powiązane z domeną. Jeśli ustawisz cookie będąc na domenie example.com, to nie zobaczysz tego cookie na www.example.com lub na about.example.com

![[Pasted image 20220928181136.png]]

Jeśli chcesz ustawić dla całej domeny, to musisz podać jawnie domenę:

![[Pasted image 20220928182654.png]]

#### path scope
Jeśli chcesz ustawić dla danej ścieżki (Path), to należy to zrobić tak:

![[Pasted image 20220928183645.png]]

Jeśli dodasz dodatkowo na wszystkie domeny, czyli <dowolnaDomena>.example.com/test , to musisz też dodać parametr domain z kropką, tak jak robiliśmy to wyżej.

Jeśli masz dużo cookies, to możesz ograniczać zużycie pasma poprzez dobieranie tylko odpowiednich cookies do odpowiednich ścieżek.

### Expires/Max-age
Parametr ten określa jak długo cookie ma być przetrzymywane przez przeglądarkę. Jeśli nie ustawisz tego parametru, to cookie zostanie zniszczone po zamknięciu przeglądarki.

![[Pasted image 20220928190301.png]]


### Same-site
	
Jest to parametr mówiący jak przekazywane mają być ciasteczka na stronę ( czy jeśli jest link na obcej stronie, to pozwalamy otworzyć tamtą stronę wysyłając jej ciasteczka (ciasteczka do innej domeny nie przejdą przez CORS)).

Ustawiany jest podczas ustawiania ciasteczka.

![[Pasted image 20220928195157.png]]

Sekurak:
Rozwiązaniem problemu nadużywania zachowania przeglądarek względem ciasteczek ma być ustawienie flagi [SameSite](https://tools.ietf.org/html/draft-west-first-party-cookies-07). SameSite może przyjmować dwie wartości: Lax lub Strict. Zajmijmy się w pierwszej kolejności wartością Strict.

Przypisanie do flagi SameSite wartości Strict spowoduje, że w przypadku gdy zapytanie do serwera zostanie wygenerowane z innej domeny (zapytanie typu cross-site) niż ta, dla której ciasteczko zostało utworzone, przeglądarka nie dołączy takiego ciasteczka do żądania.

#### Scenariusz

Mamy domenę example1.com i example2.com. Druga zawiera ciasteczko ustawiane przez serwer po zalogowaniu. Po zalogowaniu widoczne jest zdjęcie pod linkiem example2.com/img, które nie jest widoczne po zalogowaniu.

Strona exmaple1.com zawiera link do zdjęcia oraz tak img z src na zdjęcie. Jeśli nie jesteś zalogowany na example2.com, to nie dostaniesz się do zdjęcia przez link ani nie zobaczysz go poprzez tag img. Jeśli natomiast jesteś zalogowany, to w zależności od tego jak ustawiony jest parametr same-site tak będzie się zachowywała strona na example1.com. **Parametr same-site de-facto manipuluje tym czy przeglądarka ma przekazać ciasteczko do domeny example2.com, jeśli żądanie pochodzi z domeny example1.com.** Ciasteczko nie jest przekazywanie domenie example1.com, bo dba o to CORS. Możliwości

- **same-site=strict** - ciasteczko **nie jest** przekazywane podczas kliknięcia w link i **nie jest** przekazywane przy żądaniu obrazu poprzez tag img
- **same-site=lax** - ciasteczko **jest** przekazywane podczas kliknięcia w link i **nie jest** przekazywane przy żądaniu obrazu poprzez tag img
- **same-site=none** - ciasteczko **jest** przekazywane podczas kliknięcia w link i **jest** przekazywane przy żądaniu obrazu poprzez tag img
- brak ustawienia - zachowanie zależne od przeglądarki, ale na ten moment przeglądarki zachowują się tak jakby miały ustawiony same-site=lax

Parametr same-site ustawiamy w ten sposób, w sumie nie wiem czy można to robić tylko po stronie serwera, czy takie są zalecenia. Obstawiam, że taka jest dobra praktyka.

![[Pasted image 20220928202101.png]]


#### Secure

The secure attribute is an option that can be set by the application server when sending a new cookie to the user within an HTTP Response. The purpose of the secure attribute is to prevent cookies from being observed by unauthorized parties due to the transmission of the cookie in clear text. To accomplish this goal, browsers which support the secure attribute will only send cookies with the secure attribute when the request is going to an HTTPS page. Said in another way, the browser will not send a cookie with the secure attribute set over an unencrypted HTTP request. By setting the secure attribute, the browser will prevent the transmission of a cookie over an unencrypted channel.

![[Pasted image 20220928202329.png]]

##### Jeśli domena, której używasz nie ma HTTPS'a to takie ciasteczko Ci się nie ustawi.

![[Pasted image 20220928202630.png]]


### Typy cookie

- **Session** - cookie, które nie ma ustawionego Expires/MaxAge i jest usuwane przy zamknięciu przeglądarki
- **Permament** - cookie, które ma ustawione Expires/MaxAge więc **nie jest** usuwane przy zamknięciu przeglądarki
- **Secure Cookie** - te z atrybutem secure
- **Zombie Cookie** - 
- **Third-party Cookie** - używane do trackowania, ustawiane przez requesty wykonywane przez jedną stronę do innych stron. Widać to po innych domenach niż ta na której jesteś:
  
  ![[Pasted image 20220928204739.png]]
  
- **HTTPonly** - cookie, które mogą być ustawiane tylko z poziomu serwera i przeglądarka nie może ich odczytać, co sprawia, że można przechowywać SESSID i jeśli będzie atak XSS to i tak nie da się oczytać takiego cookie poprzez document.cookie, choć widać to z poziomu przeglądarki. Dobre dla tokenów i sesid, słabe np. do przechowywania score w grze, bo nie da się odczytać.

Ustawianie backend

![[Pasted image 20220928203224.png]] 

Ustawiony jest parametr HTTP lub w Firefox HTTPonly

![[Pasted image 20220928203330.png]]           
![[Pasted image 20220928203429.png]]

Niewidoczne w document.cookie
![[Pasted image 20220928203934.png]]