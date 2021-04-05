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

## Aufgaben Docker
### Bestehende Container als Backend, Desktop-App als Frontend  einsetzen

docker run --rm -d -p 8080:80 -v /web:/var/www/html --name WebTest fd47849720dc