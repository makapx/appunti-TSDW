## Ulteriori note su `fingerc`

Per definizione del protocollo finger smette di inoltrare dati quando il server chiude la connessione.
Nel codice di fingerc ciò si traduce in una `read()` che **legge 0 byte dal buffer**, ovvero un messaggio privo di payload, inoltrato dal server per indicare la chiusura della connessione.

Visto che la `read()` è una **chiamata bloccante**, nel caso in cui il server non mandi nulla il client rimarrebbe in attesa.

[RFC finger](https://www.rfc-editor.org/rfc/rfc1288)

Nello stack di rete le socket posso essere intese come **un layer che opera tra il piano di trasporto e quello applicativo**. Finger si serve di socket di tipo stream (si appoggia a TCP).

Consideriamo ora la socket come divisa in due parti, una bassa e una alta. La parte inferiore possiamo considerarla gestita dal sistema operativo.

Quando facciamo un'operazione di `read()` o `write()` su un buffer usato dalla socket i dati vengono spacchettati e impacchettati varie volte. Dal buffer del programma, a quelli del sistema operativo, della scheda di rete, dei router e poi delle altre macchine. Per questo motivo è sempre preferibile **utilizzare cicli** quando si devono fare `read()` e `write()`.

## Note su `fingers`

`fingers` fa da server per `fingerc`, ascolta sulla porta 2000 e reinoltra messaggi al client.

## Note su `unsrvr.c` e `unclnt.c` 

Il client crea una socket e costruisce l'indirizzo del server per poi fare la `connect()` verso di esso. Scrive poi con il `write()`, come se stesse agendo su un file ordinario.

Il server esegue anche lui la chiamata a `socket()`, effettua poi la `bind()`.

A differenza del caso precedente, visto che ci troviamo lato server la `accept()` ritorna come messaggio di errore `-1` e non ci dobbiamo preoccupare delle meccaniche di chiusura connessione viste invece lato client.

Lavorando con questi due file è possibile osservare un eventuale file `servsock`, creato eseguendo `unsrvr`. Questo file rappresenta la socket. Si noti anche la `s` a prefisso nella descrizione delle info del file.

**Note: errore nel Makefile. Questi due file non dipendono da mkadrr.o**

## Socket UNIX

Le socket UNIX sono lo strumento più utilizzato per la comunicazione tra processi interni alla stessa macchina. Il dominio delle socket in `unsrvr.c` è `PF_UNIX`. Le socket usate sono di tipo `struct sockaddr_un`

I due membri della `struct sockaddr_un` sono

- `sun_family`: la famiglia 
- `sun_path`: array di caratteri dove va il pathname della socket (va incluso il carattere terminatore)

Per ulteriori informazioni: `man unix` o `man 7 unix`.

Tipicamente i file che rappresentano le socket vengono messi nella directory `tmp`

Indirizzando le socket al loopback è possibile far parlare due processi attraverso la rete a senza effettivamente andare su internet.

## Note su `inclnt.c` e `insrvr.c`

Molto simili a `unclnt` e `unsrvr` ma **comunicano attraverso le interfacce di rete**.

Quando un server ascolta su una socket, anche dopo la sua terminazione, la socket può risultare occupata per un breve lasso di tempo, questo perché potrebbero essere ancora dati in viaggio e quindi di default non viene immediatamente chiusa. O si attende per circa un minuto o si settano delle opzioni specifiche per evitare questo comportamento.

```c
setsockopt(server_socket, SOL_SOCKET, SO_REUSEADDR, &reuse, sizeof(reuse));
```

Permette di non dover attendere e rendere subito la socket riutilizzabile.

Il file utilizza `doserv()` , una funzione definita nel file omonio, che fa una conversione lowercase / uppercase introducendo un leggero ritardo a seconda dell'input.

Utilizza anche la funzione `getmsg_max()` e altre ancora, definite nel file `getmsg`. Le funzioni in questione **utilizzano chiamate non bloccanti**, gestendo opportunamente l'errore `EAGAIN` (non ci sono dati da leggere).

Uno dei problemi delle read asincrone in loop è però consumo di risorse dato dal **pooling**.
Una read sincrona non spreca cicli di clock e viene semplicemente svegliata da un interrupt all'occorrenza.

`getmesgTouc.c` è un altro file utilizzato nel programma. Utilizza `select(),` un tipo di **lettura asincrona che non fa pooling ma su cui è possibile definire timeout**. Molto efficiente.

In questo file guardo il tipo di errore con cui esce la read per vedere se il motivo è l'assenza di dati. `select()` "sorveglia" quindi ciò che avviene su un certo numero di socket o file generici.

```c
int select(int nfds, fd_set *readfds, fd_set *writefds, fd_set *exceptfds, struct timeval *timeout);
```

Il primo argomento di `select()` è il l'ID della socket che si vuole monitorare + 1, che è anche l'ultimo elemento dell'insieme da monitorare +1, poi sono indicati i **descrittori osservati per eventi di lettura, scrittura ed eccezioni**, l'ultimo parametro è un timeout.

Il tipo `fd_set` è utilizzato per gli **insiemi di file descriptor**. Usiamo le macro `FD_ZERO()` ed `FD_SET()` per settare l'insieme dei file descriptor.

`select()` ritorna:

- **-1** in caso di errore
- **0** se ci sono 0 descrittori su cui ci sono stati eventi, quindi c'è stato un timeout (assenza di eventi)
- **\> 0** è il numero di eventi accaduti

La macro `FD_ISSET` ritorna true se la socket indicata come primo parametro fa parte dell'insieme dei descrittori passato come secondo parametro.

```c
int  FD_ISSET(int fd, fd_set *set);
```
