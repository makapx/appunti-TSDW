# TOMCAT 9 - QUICK START SU DISTRO DEBIAN-BASED

[TOC]

Oltre ai modi visti a lezione (scompattando lo zip fornito dal sito ufficiale e via Docker), è possibile installare Tomcat su tutte le distro Debian-based attraverso APT, il package manager di Debian.

Per il setup su distro Arch-based si rimanda invece alla [wiki](https://wiki.archlinux.org/title/tomcat)

## Download via APT

Da terminale:

```bash
$ apt-get install tomcat9 tomcat9-admin tomcat9-common tomcat9-docs tomcat9-examples tomcat9-user
```

Non tutti i pacchetti sono necessari, soprattutto se si desidera un'installazione minimale (user ed exaples sono ad esempio trascurabili).

## Creare un'istanza

Una volta terminata l'installazione è possibile lanciare un'istanza di Tomcat attraverso il comando:

```bash
$ tomcat9-instance-create <nome-instanza>
```

Verrà creata nella posizione corrente una directory con <nome-istanza> all'interno della quale sarà presente un boilerplate di progetto.

Durante il comando è possibile specificare alcuni flag, come ad esempio: `-p <httpport>`, che permette di impostare la porta di ascolto del servizio. Se non specificata, la porta di default è la `8080`

```bash
$ tomcat9-instance-create test

You are about to create a Tomcat instance in directory 'test'
Type <ENTER> to continue, <CTRL-C> to abort.

* New Tomcat instance created in test
* You might want to edit default configuration in test/conf
* Run test/bin/startup.sh to start your Tomcat instance
```

A questo punto è possibile avviare l'istanza attraverso lo script di startup.

```bash
$ test/bin/startup.sh
```

## Struttura del progetto

È possibile avere una visione di insieme del progetto attraverso il comando `ls`, utilizzando il flag `recursive`.

```bash
$ ls -lR
```

```bash
.:
totale 28
drwxr-xr-x 2 makapx makapx 4096 dic  6 10:48 bin
drwxrwxr-x 3 makapx makapx 4096 dic  6 10:48 conf
drwxrwxr-x 2 makapx makapx 4096 dic  6 10:48 logs
drwxr-xr-x 2 makapx makapx 4096 dic  6 10:48 policy
drwxrwxr-x 2 makapx makapx 4096 dic  6 10:48 temp
drwxrwxr-x 2 makapx makapx 4096 dic  6 10:48 webapps
drwxrwxr-x 2 makapx makapx 4096 dic  6 10:48 work

./bin:
totale 12
-rwxr-xr-x 1 makapx makapx 228 dic  6 10:48 setenv.sh
-rwxrwxr-x 1 makapx makapx 118 dic  6 10:48 shutdown.sh
-rwxrwxr-x 1 makapx makapx 117 dic  6 10:48 startup.sh

./conf:
totale 208
drwxr-x--- 3 makapx makapx   4096 dic  6 10:48 Catalina
-rw-r--r-- 1 makapx makapx   7308 dic  6 10:48 catalina.properties
-rw-r--r-- 1 makapx makapx   1400 dic  6 10:48 context.xml
-rw-r--r-- 1 makapx makapx   1149 dic  6 10:48 jaspic-providers.xml
-rw-r--r-- 1 makapx makapx   2799 dic  6 10:48 logging.properties
-rw-r--r-- 1 makapx makapx   7580 dic  6 10:48 server.xml
-rw-r--r-- 1 makapx makapx   2756 dic  6 10:48 tomcat-users.xml
-rw-r--r-- 1 makapx makapx 172439 dic  6 10:48 web.xml

./conf/Catalina:
totale 4
drwxr-x--- 2 makapx makapx 4096 dic  6 10:48 localhost

./conf/Catalina/localhost:
totale 0

./logs:
totale 32
-rw-r----- 1 makapx makapx 15675 dic  6 11:16 catalina.2022-12-06.log
-rw-r----- 1 makapx makapx 16217 dic  6 11:16 catalina.out
-rw-r----- 1 makapx makapx     0 dic  6 10:48 localhost.2022-12-06.log
-rw-r----- 1 makapx makapx     0 dic  6 10:48 localhost_access_log.2022-12-06.txt

./policy:
totale 16
-rw-r--r-- 1 makapx makapx 12990 dic  6 10:48 catalina.policy

./temp:
totale 0

./webapps:
totale 0

./work:
totale 0
```

## Risultato finale

È quindi possibile visualizzare la pagina di interesse andando su `localhost` alla porta precedentemente indicata.

Verrà inizialmente visualizzata una pagina di 404, questo perché sotto `webapps/` non è presente ancora alcun progetto. Da questo momento in poi, è possibile creare una directory `ROOT` (sotto `webapps`) e dare vita alla propria servlet.

## Note per il troubleshooting

Assicurarsi di non avere altre istanze di Tomcat attive sulla stessa porta. Al setup dell'istanza viene tipicamente indicato tra i warning se le porte scelte sono occupate.