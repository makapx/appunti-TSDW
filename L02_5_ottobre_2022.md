## Utilizzo di `make`: basi

``make`` è una utility per la compilazione e gestione dei programmi.
Diciamo informalmente che ``make`` "riesce a scoprire" del file specificato.

```bash
make -n fingerc 	# dry run
make fingerc 		# esecuzione effettiva
```

Usare `make` è come eseguire i comandi:

```bash
cc -Wall -c -o -mkaddr.o mkadrr.c	# solo se la dipendenza mkaddr.c non è ancora stata compilata
cc -Wall fingerc.c mkaddr.o -o fingerc
```

I file oggetto vengono linkati insieme dal linker `cc`.

Flag utili:

- `-n`  dry run di `make`
- `-o` per specificare l'output name

Note interessanti su `make`:

- Se si specifica di nuovo make ma il file è up to date non viene ricompilato
- Se parti del programma sono già compilate e non modificate ``make`` non le ricompila
- `make` è molto utile quando si ha a che fare con applicazioni complesse / grossi progetti visto che ricompila selettivamente solo i file necessari. ( Es: compilare i kernel dei sistemi operativi dopo che si è cambiato solo qualche file )

Intuiamo quindi che `make` per operare ha bisogno di:

- saper valutare i timestamp dei file
- accedere i metadati dei file
- conoscere le dipendenze

Le dipendenze possono essere rappresentate come grafi (preferibilmente aciclici) o alberi. Il **makefile** serve per riconoscere i grafi/alberi di dipendenza.

## Il Makefile: analisi

- **`CFLAGS`**: i flag
- **`EXES`**: l'elenco di stringhe che indica gli eseguibili che si vogliono creare
- struttura  ***target : dipendenze***

`make` di solito prende come argomento di default l' ``all``

```makefile
 **all : $(EXES)**
```

Notare che in questo caso tutti gli eseguibili dipendono da mkaddr.o

```makefile
**$(EXES): mkaddr.o** 
```

- **`clean`**: target clean per la pulizia. Azioni associate:

  - `rm *.o`: rimuove tutti i file oggetto

  - `rm $(EXES)`: rimuove tutti gli eseguibili


```makefile
CFLAGS=-Wall

EXES=fingerc fingers addrcnv byaddr byname gethost \
unclnt unsrvr inclnt insrvr inskelsrvr inparsrvr mboxclnt mboxsrvr nblksock \
sndrcvClessClnt sndrcvClessSrvr \
sndrcvConnClnt sndrcvConnClntStream sndrcvConnSrvrStream

all: $(EXES)

$(EXES): mkaddr.o

byaddr: printaddr.o byaddr.o
byname: printaddr.o byname.o
gethost: printaddr.o gethost.o
getmsgTout.o: getmsg.o
inclnt: getmsg.o getmsgTout.o inclnt.o
insrvr: insrvr.o doserv.o
inskelsrvr: inskelsrvr.o doserv.o
inparsrvr: inparsrvr.o doserv.o
sndrcvConnSrvrStream: sndrcvConnSrvrStream.o doserv.o
mboxsrvr: mboxsrvr.o
mboxclnt: mboxclnt.o

clean:
	-rm *.o
	-rm $(EXES)
```

Note utili:

[GNU make guide](https://www.gnu.org/software/make/manual/make.html)
[Materiale tutorato 2021](https://studentiunict.sharepoint.com/sites/TecnologieperiSistemiDistribuitiedilweb/Shared%20Documents/Forms/AllItems.aspx?id=%2Fsites%2FTecnologieperiSistemiDistribuitiedilweb%2FShared%20Documents%2FTutorato&p=true&ga=1)
[Repo del corso 2021/2022](https://studentiunict.sharepoint.com/:f:/s/TecnologieperiSistemiDistribuitiedilweb/EvlY_FXUEuJIka60ZRZZjNwBnBrsc2YvQXnv4Uco98Qrfg?e=W7lKUb)

------

## Fingerc

### Analisi del comando fingerc

Ogni programma ben scritto o si invoca così com'è o deve indicare quali siano i parametri iniziali richiesti, eventualmente dando degli esempi.

Esempio di uso di fingerc

```bash
./fingerc directory.mit.edu smith
```

Note sull'output: il server inoltra uno stream di dati frammentati (o vengono frammentati dalla rete sottostante), per questo motivo si possono leggere eventuali messaggi, proveniente dal programma, che indicano la fine del flusso.

Vedi anche:

```bash
telnet directory.mit.edu 79
```

```bash
finger smith@directory.mit.edu
```

finger è un client che serve per parlare con gli altri servizi finger. Ad oggi non è più molto utilizzato a causa della cattiva fama dovuta ad un vecchio [worm che colpiva i server finger](https://it.wikipedia.org/wiki/Morris_worm).

### Note sul codice sorgente di fingerc

```bash
# da una buona overview sulle stringhe "letti tot bytes" tornate da fingerc, è anche possibile usare regex
./fingerc directory.mit.edu smith | grep -i letti 
```

[Note utili sul comando grep](https://stackoverflow.com/questions/25443339/how-to-grep-in-linux-grep-unrecognized-option)

Mai assumere la dimensione minima dei pacchetti, potrebbero non arrivare della dimensione stimata/voluta.

