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

![[Pasted image 20221002202045.png]]

### JWT based authentication

Token generowany jest na podstawie klucza symetrycznego lub asymterycznego i publicznie dostępnych danych przekazywanych przez daną osobę. Taki token powinen być wysłany z nagłówkami same-site:strict (żeby nie dało się zrobić CSRF) oraz httponly (żeby nie dało się odczytać) oraz secure, żeby był wysyłany tylko po HTTPS

![[Pasted image 20221002202429.png]]

Autentykacja przebiega w następujący sposób: 
- Użytkownik wysyła requesta z tokenem
- Serwer w jakiś sposób weryfikuje to i nie musi używać bazy danych
- Serwer daje dostęp do zasobu

![[Pasted image 20221002202828.png]]
