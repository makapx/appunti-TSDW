## Architettura client-server semplice: cenni

Utilizziamo il **paradigma request - reply** nei sistemi client - server.
Bisogna avere competenze sullo scambio di messaggi attraverso le socket, sapere come scrivere una richiesta ed una risposta.

### Sistema ad invocazione remota

Anziché formulare una request viene invocata una funzione. La chiamata a funzione triggera sul server la computazione.
Punti a favore rispetto allo schema request - reply: **semplicità** e **trasparenza**.

Il ritorno della funzione utilizzata si comporta esattamente come il valore di ritorno di una comune chiamata a metodo. Il lato server deve ovviamente essere compliance con questo tipo di programmazione.

Vedi: **RPC o RMI (Java)**

------

## Architettura 3-tier

Oltre al client (tier 1) sono presenti altri due attori, entrambi legati al lato sever.
Il server è quindi diviso in due componenti:

- **server web (tier 2)**
- **database server (tier 3)**

Il database server ha una porta (3306 tipicamente), su cui accetta richieste scritte in linguaggio SQL (se relazionale).
Tipicamente le macchine dei due server sono diverse.

Esempi di server web sono:

- **apache**
- **tomacat**
- **PHP** (può anche essere inteso come modulo di un server oltre che come un linguaggio di programmazione)

Sul server web risiede la **business logic** (logica applicativa)

![Architettura 3-tier](./img/3tier.png)

Il client non deve poter mandare direttamente query al database. La logica di business forza quindi il client a comportarsi in un certo modo per ottenere i dati che gli interessano.

Tipicamente il browser, che fa fa client nel contesto web, manda richieste HTTP o HTTPS al web server.

### Scrivere la business logic

- **Serverlet**: una classe Java che fa da handler a richieste HTTP, con gestione di sessini e cookie

###  Tecnologie per il web dinamico:

- **Java server pages**
- **PHP**

### Le API

Se il sever web espone API è possibile scrivere codice in maniera **programmatic**.

Formato dei messaggi:

- **SOAP**: più vecchio, è un dialetto di XML
- **REST**: più diffuso. Se usiamo REST per il formato dei dati possiamo usare XML o JSON.

#### Consumare API lato client

- Thin client in Python

- Integrare javascript nel browser

### Note sui web server

**Tomcat**: può essere sia stand alone che con Apache. È un engine per le serverlet che si interfaccia con l'interprete Java;

**Laravel**: framework PHP, basato su paradigma MVC;

**Spring Boot**: framework Java, basato su MVC.

------

Note esame:

- ok alle prove in itinere, anche 6 - 7 all'anno, con possibilità di fallire o scartare alcune
- esame finale dove si chiedono solo le parti non superate/non scartate alle prove in itinere.
- tipicamente le prove in itinere sono di laboratorio / codice.
- orale incentrato su eventuali chiarimenti rispetto agli esami scritti / lab, se ce ne sono.

------

Altre note interessanti:

A differenza di Bash, che è orientato agli stream testuali, Powershell è orientato agli oggetti. I comandi di Powershell ritornano quindi un oggetto.

