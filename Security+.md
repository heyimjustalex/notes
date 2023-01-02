### Social Engineering

SEToolkit - sprawdz to, bo można sobie łatwo podmienić stronę

## Wirusy

**Polymorphic virus**  - wirus modyfikujący się przy każdym starcie, aby nie dało się policzyć sygnatury

**Armoured virus** - taki, który jest ciężki do reverse engineering

Vising - Phising over voice

SPIM - Spam over instant messenger

Dużo dzisiejszych wirusów jest fileless, dlatego antywirusy nie działają, bo są w stanie skanować tylko pliki. Robisz wirusa ładowanego do pamięci i manipulujesz rejestrami, żeby za każdym razem był uruchamiany jakiś shell script po prostu. 

PuP - potentially unwanted program, wersja light malware

RAT  - Remote Access Tool/Trojan

Logic Bomb - program odpalany w komputerze bazujący na jakimś warunku, time or event triggered

#### Rady
- limited access for user
- anti-virus and anti-malware
- ngfw, ngips
- dns protection np. onpenDNS
- user training

brute force - wszystko po kolei

dictionary attack - wszystko z danego słownika


spraying - branie tysiecy kont i testowanie na nich popularnego hasla

timeouty zeby limitowac bruteforcy, czy captcha

rainbow table to tablica z wyliczonymi juz hashami zebys nie musial ich liczyc jak crackujesz baze z haslami

NTLM

Narzedzie responder Kali

narzedze john

group policy moze blokowac podlaczanie usb, to dobrze bo chronisz sie przed badusb

jak zarzadzasz korporacją, to musisz być odporny na card cloning jesli sa karty. jesli czip to ciezej sie kradnie, a jesli magnetic strip, to albo skaner albo skimmer

mozna atakować ludzi poprzez dostarczanie fałszywych danych do modelu AI i w ten sposób narażać firmę na straty

security to takze bezpieczenstwo supply chains - jesli czip i jego firmware są compromised to ty tez jesteś

downgrade attack - zmuszamy do skorzystnia ze starszego protokołu, który ma podatności

jesli wystąpi kolizja hasła to atakujący nie musi znać orginalnego hasła, wystarczy, że zna takie, które daje ten sam hash. Jeśli jest używana sól w aplikacji, to nie ma takiej możliwości

mozna wymusic używanie tls'a na poziomie group policy, żeby ktoś nie używał ssla

jesli chcesz zobaczyc czy nie masz jakiegoś RAT'a to wejdź netstat -r

jeśli zmienił się hash boot sectora czy systemu, to znaczy, że możesz mieć rootkita

### app security

memory leak moze doprowadzić do dos'a

buffer overflow

integer overflow

race condition - time of check and time of use

directory traversal

replay session

pass the hash

request forgery - server and client site

możesz sie czasem zalogować kasując hasło z parametrów GET, jesli apka jest zle napisana

driver manipulation, shimming vs refactoring

pointer derefercence change

trzeba robić limity na resety passwordów

### Network attacks

initialization vector attack

rouge access point - evil twin attack
triangulacja, autoryzacja poprzez kontroler wifi i serwer aaa

bluesnarfing - bluethooth sniffing
bluejacking - pushing information into bluetooh device
zigbee i iot nie jest bezpieczne
nfc

In-line/MITM/On-path

dhcp spoofing, podmiana bramy na własną
arp spoofin / Mac flooding - moze zmusic switcha do broadcastow informacji i mogą trafić do ciebei
malicious layer3 router
dns spoofing 
dns poisoning
nie wolno pozwalać na downgrage, trzeba robić https tls strict, żeby nie dało się zrobić ssl stripping
typosquatting

malicious DHCP - brak autoryzacji serwera DHCP sprawia, że można podać klientowi fałszywego DNS'a lub bramę

nie wyłączenie trunk'a na połączeniu sprawia, że vlany nie mają sensu. Atakujący może wynegocjować trunk'a jeśli jest dynamicznie i tagować swój ruch w odpowiedni sposób i tak dostać się do dowolnej sieci.

CDP i LLDP - mogą dostarczyć atakującemu więcej informacji i też nie wiaodomo jak switch się zachowa jeśli dostanie kilkaset tysięcy pakietów CDP/LLDP

STP - bpdu priority, można zmusić do ruchu przez twój switch

#### DNS

domain hijacking - przejęcie domeny i skierowanie jej na inne ip
URL redirection -

DNS tunneling - ponieważ mało zaawansowane switche analizują np. po portach to możliwe jest tunelowanie ruchu po porcie 53 imitując, że jest to dns (chyba DPI (NGFW) może to zwalczyć bo zajrzy do środka)

ARP inspection, dhcp snooping

Man in the browser ?


MAC cloning - poniewaz drukarki nie maja czesto 802.1x to daje sie im po MAC'u większe uprawnienia. Sklonowanie takiego maca może dać nam wyższe uprawnienia. Podobnie jest przy filtrowaniu MACów poprzez access pointy.

domain reputation - openDNS przez cloudflare może ci filtrować

symptom bycia botem to dziwne połączenia na serwerze i dziwny ruch wychodzący. Można to widzieć po baseline do jakich krajów normalnie wysyłany jest ruch. Jeśli pojawia się jakiś inny, to znaczy że ktoś jest botem.

jesli chcesz sie chronić przed ddosami to postaw jakiś ngfw przed serwerem, żeby do samego serwera nie dochodziły requesty

host  based intrusion system, disable kazda usluge ktorej nie uzywasz

single sign on ?

### Threat actors
shadow it - jak ktoś implementuje coś na własną rękę bez informowania IT o tym 

state actor - to aktor rządowy, jest to APT czli advanced persistant threat

criminal sydnicates 

jednym ze sposobów unikania hakownia wifi moze byc uzycie direct antenas i ograniczanie radio frequency

ttp - tactics techniques procedures

isao - info sharing and analisys organisation - organizacja zbierajaca dane na temat zagrozen i potem je sprzedajaca

https://cve.mitre.org/
https://nvd.nist.gov/
cisa.gov
https://talosintelligence.com/

IOC - indicator of compromise
AIS - automated indicator sharing
CISA -

Predictive analisys

What is the acronym for the system used to share files in Automated Information Sharing?

D. TAXII

Palo Alto, Checkpoint oferują rozwiązania w chmurze, które analizują ci ruch. Cisco tez

fireye.com
cybermap.kaspersky.com
akamai threat map
a10networks
trzeba miec jakis feed na newsy, ktore dotyczą firm ktorych oprogramowania uzywasz

ttp tactics

konfiguracje też powinny mieć swój baseline i powinny być sprawdzane przed wdrożeniem

netstatem możesz zbadać sobie otwarte porty

software innego vendora to też supply chain, wazne zeby miec w umowie okreslony support bo jak bedzie compromised to musi wypuścić łatkę

exploit-db.com

DLP - data loss prevention - system ngfw zapobiejagacy wycieku danych
