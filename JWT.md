### Definicja

**JWT - JSON Web Tokens** to otwarty standard, który umożliwia transfer danych lub wymianę informacji między dwiema frakcjami. Działa na zabezpieczonym URL-u. Dane przekazywane są zakodowane jako obiekty JSON Web Signature lub jako struktury JSON Web Encryption. Przekazywanym informacjom można ufać, ponieważ są podpisane cyfrowo. Podpisuje się je w sposób niejawny za pomocą algorytmu HMAC lub jawnie za pomocą klucza publicznego/prywatnego RSA lub ECDSA.

JSON Web Tokens pozwala nadać podpisane przekazy. Umożliwiają one weryfikację zawartości. Natomiast zakodowane przesyłki ukrywają tę zawartość. Korzystanie z prywatnego lub publicznego klucza dodatkowo certyfikuje nadawcę transferu danych lub informacji.


### Session based authentication (stary sposób)

![[Pasted image 20221002200904.png]]

SessionId przechowujemy w bazie i użytkownik za każdym razem wysyła swoje sessionId w ciasteczku, co pozwala na bezpieczną identyfikację i zarządzanie zasobami danego użytkownika.

![[Pasted image 20221002201059.png]]

Każdy request ma dodatkowe latency, bo odpytuje bazę danych. Można cache'ować sesję, ale powoduje to dodatkowe problemy z wygaszeniem sesji w przypadku jej przejęcia i może powodować niekonsystencję stanu między serwerem a bazą danych.


### Cechy JWT

- Bezstanowy - pojedynczo aplikacja oraz jako system
- Ma 3 części - Header, Data, Signature
- Tylko serwer może zweryfikować podpis
- Podpis może być symteryczny i asymteryczny
- Symetryczne algorytmy wymagają tego samego klucza do stworzenia JWT i zwalidowania
- Asymetryczne algortymy - prywatny klucz tworzy JWT, a publiczny waliduje

### Składnia JWT

**Header** zawiera podstawowe informacje - użyty algorytm
**Payload** zawiera:
	sub: id usera z bazy
	name
	iat - issued at 
	exat - expires at 
Chyba chodzi o to, żeby to były po prostu unikalne dane, żeby token na ich podstawie generowany był też unikalny
**Verify Signature** -  Informacje o tym jak zweryfikować podpis


![[Pasted image 20221003092813.png]]

#### Zastosowanie
Jeśli mamy dwie różne aplikacje i chcemy, żeby autentykacja na jednej powodowała, że user nie musi się logować na drugiej. 

Kiedy mamy reverse-proxy i wiele serwerów, to wystarczy, że współdzielą ten sam klucz.

Te możliwości są zasługą tego, że token przechowuje w całości klient, a 

![[Pasted image 20221003093246.png]]

### JWT based authentication

Token generowany jest na podstawie klucza symetrycznego lub asymterycznego i publicznie dostępnych danych przekazywanych przez daną osobę. Taki token powinen być wysłany z nagłówkami same-site:strict (żeby nie dało się zrobić CSRF) oraz httponly (żeby nie dało się odczytać) oraz secure, żeby był wysyłany tylko po HTTPS.

![[Pasted image 20221002202429.png]]

Autentykacja przebiega w następujący sposób: 
- Użytkownik wysyła requesta z tokenem
- Serwer w weryfikuje to poprzez sprawdzenie podpisu z użyciem własnego klucza prywatnego i nie musi używać bazy danych
- Serwer daje dostęp do zasobu

![[Pasted image 20221002202828.png]]

#### JWT vs Session

Serwer w przypadku sesji przechowuje sessionID w bazie danych. W przypadku JWT całość informacji przechowuje klient, a serwer tylko weryfikuje to z użyciem swojego klucza.

![[Pasted image 20221003091715.png]]

### Problemy

Problemem w przypadku podstawowej implementacji JWT jest to, że tokena nie da się odwołać. Będzie ważny do momentu wygaśnięcia, a UX z logowaniem co 15 minut to przypał.

![[Pasted image 20221003180243.png]]

#### Refresh Tokens

Rozwiązaniem mogą być refresh tokens, które łączą trochę podejście sesyjne, gdzie sesję przechowujemy w bazie danych i podejście JWT. 

![[Pasted image 20221003180441.png]]

 Tutaj koncept wygląda tak, że wystawiamy tokeny zwykłe z exp. time na 15 minut, a w bazie przechowujemy Refresh Token.

![[Pasted image 20221003180620.png]]


Po 15 minutach klient musi odnowić i wtedy odpytywana jest baza po stronie backend'u, żeby sprawdzić czy token jest na blackliście. Jeśli nie jest to nowy token jest dostarczany użytkownikowi. 

![[Pasted image 20221003180737.png]]

To podejście łączy zalety JWT bo działa optymalniej bo nie odpytuje co chwila bazy jak w przypadku sesji oraz w przypadku utraty tokenu jest możliwość jego modyfikacji poprzez unieważnienie refresh token w bazie. 

### Klucze
Powinny być przechowywane jako zmienne systemowe, a nie w kodzie, bo to niebezpieczne.

### Plusy i minusy JWT

![[Pasted image 20221003185912.png]]