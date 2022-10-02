### What is CORS?

Cross-origin resource sharing (CORS) is a mechanism that **allows restricted resources on a web page to be requested from another domain outside the domain from which the first resource was served.**

A web page may freely embed cross-origin images, stylesheets, scripts, iframes, and videos. Certain "cross-domain" requests, notably **Ajax requests, are forbidden by default** by the same-origin security policy. CORS defines a way in which a browser and server can interact to determine whether it is safe to allow the cross-origin request. It allows for more freedom and functionality than purely same-origin requests, but is more secure than simply allowing all cross-origin requests. 

#### ZASADA
The server is responsible for reporting the allowed origins. The web browser is responsible for enforcing that requests are only sent from allowed domains. Możesz więc odpytywać z POSTMAN'a i otrzymywać odpowiedzi.


#### Przykład Husajn

Jeśli wyślesz takiego requesta do jakieś domeny, to prawdopodbnie przeglądarka Ci nie pokaże odpowiedzi.

![[Pasted image 20220929223557.png]]

Odpowiedź nie zawiera nagłówka Access-Control-Allow-Origin="twojadomena.pl".
Trzeba więc ustawić na backendzie taki header:
![[Pasted image 20220929223739.png]]

Wtedy odpowiedź wygląda tak i przeglądarka porównuje to z url z jakiego przyszedł request:
![[Pasted image 20220929223846.png]]

Jeśli domeny się zgadzają to NIE ma błędu o CORS.

####
CORS is applied to requests when an Origin header is included in the request. This includes requests made from JavaScript and POST requests. It's not applied all resources. The origin is the protocol, host and port that is making the request. Requests made by JavaScript use the origin that loaded the JavaScript, not the origin that it was loaded from. 

When CORS is not enabled a browser will rely on the [same origin policy](https://en.wikipedia.org/wiki/Same-origin_policy). The same origin policy is only applied to scripts. The browser will only allow scripts to be loaded from same origin as the loaded page. The same origin policy is assumed when not origins are explicitly allowed.

An HTTP client other than a browser won't use either the same origin policy or CORS. Requests made from these other HTTP clients don't have an origin. Unless the Postman desktop app emulates a browser it will be able to make requests to any URL.

CORS and the same origin policy are needed because a browser does not implicitly trust the websites it visits to make requests to other websites. They don't protect the origin site, they protect the site receiving the cross origin requests. This is why the allowed origins are up to the targeted server.

Without these policies a simple script that repeatedly loads a website could be distributed by ad networks or script injection and then any browser loading the script would contribute to a denial of service attack on the website. With CORS and the same origin policy a browser will limit the impact of this script.

Another important protection CORS provides is to protect against [Cross-site request forgery](https://en.wikipedia.org/wiki/Cross-site_request_forgery). It prevents a site from making some types of requests to another site. These requests would be made using any previously created tokens, such as session tokens.

CORS by example:

A web browser loads a page from `www.example.com`. The page includes a script that makes a request to `www.example.org`. The origin of the request is `www.example.com`. The browser either makes the request or sends an `OPTIONS` request first (the preflight request). When the server at `www.example.org` receives a request from an origin other than `www.example.org` it responds with a response header `Access-Control-Allow-Origin` which tells the browser the origins allowed to make requests. It may also respond with other headers like `Access-Control-Allow-Methods` and `Access-Control-Allow-Headers` that can restrict the types of allowed requests. When the browser is told what origins are allowed it will block future requests from disallowed origins.

