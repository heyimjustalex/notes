**QUIC** (Quick UDP Internet Connections ) to eksperymentalny protokół sieciowy warstwy transportowej zaprojektowany przez Google (RFC 9000). Głównym celem jest zmniejszenie opóźnień w porównaniu z TCP. QUIC jest podobny do **TCP+TLS+HTTP/2** zaimplementowanego na bazie UDP UDP. Ponieważ TCP jest zaimplementowany na najniższych poziomach systemów (systemy operacyjne, oprogramowanie układowe routingu), wprowadzanie zmian w TCP jest prawie niemożliwe, biorąc pod uwagę liczbę ulepszeń, które musiałyby nastąpić. **Ponieważ QUIC jest zbudowany na bazie UDP, nie ma takich ograniczeń i może być zintegrowany z aplikacjami hosta końcowego.** #QUIC

Szacuje się, że 88% ruchu dla Androida i Chrome Desktop opiera się teraz na QUIC, a interakcje z backendami Google mogą skutkować 5% poprawą wydajności i 30% mniejszym ponownym buforowaniem aplikacji do przesyłania strumieniowego.

### Cechy QUIC
- Oparty na UDP (UDP stanowi warstwę transportową i na tym jest QUIC)

![[Pasted image 20220927210958.png]]

- Nowy stack protokołów wygląda w ten sposób: #HTTP

![[1.png]]

- Pomimo, że jest oparty na UDP to jest pewność, że pakiety zostaną dostarczone zaimplementowana na poziomie QUIC, a nie na poziomie UDP
- QUIC posiada mechanizm mulitpleksacji, który pomaga w obsłudze dużej ilości requestów od klienta, z czym HTTP/2 + TCP może sobie nie radzić
- Ma coś takiego jak 0-RTT - koncept który występuje też w najnowszym TLS'ie i pozwala na to żeby nie przeprowadzać handshaków jeśli już była wcześniej jakaś komunikacja. W TLS 1.3 jest to w oparciu o PSK (pre-shared key), tutaj pewnie podobnie
- Mniejszy overhead w porównaniu do TCP
- Handshake jest krótszy i wygląda jakbyś połączył TCP handshake z TLS negotiation #TLS


 ![[Pasted image 20220927211304.png]]
- Quic posiada w headerze coś takiego jak Connection ID, które pozwala trackować użytkownika nawet po zmianie ip i pewnie wtedy działa to 0-RTT #RTT

![[quic-fig2.png]]

### QUIC encryption

https://blogs.keysight.com/blogs/tech/nwvs.entry.html/2021/07/17/looking_into_quicpa-pUtF.html