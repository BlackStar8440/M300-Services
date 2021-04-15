# M300 LB02

## 30-Docker
### Docker Befehle
Befehl | Erklärung 
------------ | ------------- | 
`docker run` | Befehl um neuen Container zu starten 
`docker ps` |Gibt Überblick über die aktuellen Container an. Z.B. Name, IDs oder Status 
`docker images` | Gibt Liste lokaler images aus. Dazu gibt es auch nocht Informationen aus
`docker rm` | Entfernt einen oder mehrer Container
`docker rmi` | Löscht ein oder mehrere images
`docker start` | Startet gestoppte Container
`docker stop` | Stopt Container
`docker kill` | Stopt Ccontainer sofort
`docker logs`| Gibt Logs für einen COntainer aus
`docker inspect` | Gibt umfangreichloche Informationen zu Container aus
`docker diff` | Gibt Änderungen am Dateisystem des Containers im verleich zum Image, mit dem er gestartet wurde
`docker top` | Gibt Informationen zu laufenden Prozessen in eimnem Container aus
`docker build` | Baut ein Container anhanend eines Docker Files

### Dockerfile
#### Was ist ein Dockerfile?
Ein Dockerfile ist eine Textdatei, in dem die "Befehle" definiert sind, anhanden von denen ein Container erstellt wird

#### Anweisungen im Dockerfile
Anweisung | Erklärung 
------------ | ------------- | 
`FROM` | Gibt an welches "Base" Image verwendet werden soll
`ADD` | Kopiert die Datei, aus dem Build Context oder von URLs in das Image
`CMD` | Führt die angegebenen Anweisungen aus, wenn der Container gestartet wurde
`COPY` | Kopiert Dateien aus dem Build Context in das Image
`ENTRYPOINT` | Legt eine ausführbare Datei fest, welche beim Start des Containers laufen soll
`ENV` | Setzt Umgebungsvariablen im Image
`HEASLTCHECK` | Docker prüft den Status der Anwendungen in einem Conatainer
`MAINTAINER` | Setzt die "Autor-Metadaten" des Image auf den angegebenen Wert
`RUN` | Führt die angegebenen Anweisungen im Container aus und bestätigt das Ergebnis
`SHELL` | Erlaubt der Shell für folgenden RUN-BEfehk zu setzen.
`USER` | Setzt User, welcher in folgeden RUN-, CMD- oder ENTRYPOINT-ANweisungen genutzt werden soll.
`VOLUME` | Deklariert die angegebene Datei oder das Verzeichnis als Volumen
`WORKDIR` | Setzt Arbeitsverzeichnis für alle folgende RUN-, CMD-, ENTRYPOINT-, ADD oder COPY-Anweisung

docker pull IMAGENAME --> holt image
docker image --> zeigt heruntergeladene Images an
docker run -d -t --name ContainerName ImageName --> Container mit bestimmten Namen starten
docker container exec -it ContainerName /bin/bash --> geht in VM

## Apache Webserver Projekt
### Übersicht
In diesem Projekt geht es darum, dass ein Container auf Docker erstellt wird und man diesen zu einem Apache Webserver macht. Das Ziel ist, dass man vom Host (mein Laptop) auf den Webserver via den lokal installierten Browser zugreofen kann

#### Netzwerkplan
![alt text](https://github.com/BlackStar8440/M300-Services/blob/main/LB02/images/DockerWebServer.PNG)

#### Apache Image erstellen
Es wurde ein neues Dockerfile angelegt mit folgendem Inhalt (Den Inhalt habe ich von den Vorlagen, welche uns unser Modullehrer zur Verfügung gestellt hat):

```
FROM ubuntu:14.04
MAINTAINER Marcel mc-b Bernet <marcel.bernet@ch-open.ch>

RUN apt-get update
RUN apt-get -q -y install apache2

# Konfiguration Apache
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2

RUN mkdir -p /var/lock/apache2 /var/run/apache2

EXPOSE 80

VOLUME /var/www/html

CMD /bin/bash -c "source /etc/apache2/envvars && exec /usr/sbin/apache2 -DFOREGROUND"
```

Dannach habe ich das Image anhand des Dockerfiles erstellt:
`docker build VerzeichnisInDemSichDasDockerfileBefindet` --> Bei der Erstellung sieht man direkt die ID des Images

#### Container mit Image starten
Mit folgendem Befehlt, wird ein COntainer mit dem erstellten Image erstellt. Ebenfalls beinhaltet es die Namenssetzung vom Container und das Portforwarding vom Port 80 zu 8080:
`docker run --rm -d -p 8080:80 -v /web:/var/www/html --name ContainerNameSetzen ImageID`

#### index.html File
Folgende Befehle mussen gemacht werden, damit das index.html File, welches ich gmeacht habe vom Host in den Container kopiert wird:
`docker cp /SpeicherortDerDatei/index.html ContainereName:/var/www/html/`

Das Index File sieht folgendermassen aus:
```
<!DOCTYPE html>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>M300</title>
</head>
<body>
<h1>LB02 GitHub Repository</h1>
<a>Das GitHub Repository für die LB02 ist unter folgendem Link erreichbar:</a>
<a href="https://github.com/BlackStar8440/M300-Services/tree/main/LB02">Klicken Sie hier</a>
</body>
</html>
```
In dem index.html File ist ein Link zu meinem GitHub Repository der LB02.

#### Testfall
Eigentlich gibt es hier leider nicht viel zu testen. Das einizige ist, dass man auf die index.html Datei, welche ich erstellt habe, von meinem Laptop via den lokalen Browser zugreifen kann:
![alt text](https://github.com/BlackStar8440/M300-Services/blob/main/LB02/images/WebServerBrowserAnsicht.png)

## Container Sicherheit
### Sicherheiten
#### Nicht als root in den Container gehen
Dazu muss man folgende Zeilen im Dockerfile machen:
```
RUN useradd -ms /bin/bash NeuerUserName

USER NeuerUserName

WORKDIR /homeNeuerUserName
```
Der unterste Befehl ist nicht nötigt. Dieser sagt nur aus in welchem Verzeichnis man sein sollte, wenn man sich mit dem Container verbindet (im Home Verzeihcnis hat der User Admin Berechtigung)

Wenn man dann den Container mit dem Image, welche die oben gennanten Zeilen enthält startet ist man als ein User angemledet und man muss ein Passwort eingeben um Befehle mit sudo auszuführen(siehe Bild unten):
![alt text](https://github.com/BlackStar8440/M300-Services/blob/main/LB02/images/UserLogin.PNG)

#### Read-Only
Wenn man den Docker mit der Option read-only startet, können keine Änderungen am Dateisystem vorgenommen werden (auch mit sudo nicht):
`docker run --read-only -d -t --name NameDesContainer Image`

Wenn man z.B. eine Datei erstellen möchte kommt dann folgende Meldung:
![alt text](https://github.com/BlackStar8440/M300-Services/blob/main/LB02/images/readonly.PNG)

#### Dockerfile's
Ein wichtiger Punkt bei dem man sich und seine Netzwerkumgebung schützen kann ist, dass man seine Dockerfile's selber schreibt. Wenn man das macht und nicht irgendwelche Images aus dem Internet herunterladet kann man sich sicher sein, dass diese nicht mit Malware oder sonst was verseucht sind.

### Volumes
#### Datencontainer
Container mit Datencontainer namens dbdata erstellen:
`docker create -v /dbdata --name dbstore busybox `

Es wurde nun ein Verzeichnis namens dbdata im Host angelegt:
![alt text]()
Als Test für später wurde eine Datei namens test.txt dort drin erstellt.

#### Zugriffs-Cotainer
Nun wurde ein anderer Container erstellt, welcher auf ds erstelt Verzeichnis zugreifen soll. Das erfolgt mit folgendem Befehl:
`docker run -it --volumes-from dbstore --name NameDesContainers busybox`

### Monitoring
#### Cadvisor
Cadvisor ist eine Überwachungs Tool von Google. Mit folgendem Befehl habe ich einen Container erstellt, welcher Cadvisor enthält:\
`docker run -d --name cadvisor -v /:/rootfs:ro -v /var/run:/var/run:rw -v /sys:/sys:ro -v /var/lib/docker/:/var/lib/docker:ro -p 8081:8080 google/cadvisor:latest`

Nach dem Aufsetzen des Containers konnte ich via den Port 8082 auf Cadvisor zugreifen:
![alt text](https://github.com/BlackStar8440/M300-Services/blob/main/LB02/images/Cadvisor.png)