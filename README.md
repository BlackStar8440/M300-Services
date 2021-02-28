# M300-Services
M300 Serverübergreifende Dienste

## 10 Toolumgebung aufsetzen
### GitHub Bash
Konfigurieren:

git config --global user.name "username"
git confug --global user.email "E-Mail"

#### Befehle
##### Repository klonen
Cd in gewünschtes Repository, in das es kopiert werden sollte:

git clone "URL vom Repository"

Cd in das geklone Repository und Status abfragen:

git pull
git status

##### Lokale Änderungen in Repository pushen
Cd in Repository
git add - A "Filename"
git commit -m Kommentar (falls gewünscht)
git push

### VSC
#### Extensions
Auf das Zeichen mit den 4 Vierecken auf der linken Seite oder ctrl + , --> Extension suchen und installieren

#### Lokale Änderungen in Repository pushen
Änderung mit ctrl + s speichern --> auf der linken Seite wird bei einem Icon eine 1 erscheinen --> Text Message eingeben --> auf Hacken drücken --> drei Punkte --> Push

### Vagrant
#### Vorbereitung
VirtualBox, Cmder und Vagranat herungerladen

#### Vagrant File erstellen
Voranmerkung: https://app.vagrantup.com/boxes/search hier findet man die VM Vorlagen, welche man benutzen kann.

cd Ort, an dem die VM abgelegt werden sollte und einen neuen Ordner erstellen:

vagrant init ubuntu/xenial64 |War das Beispiel in der Aufgabe

#### vagrant installieren und starten
vagrant up --provider virtualbox

Info: Dannach ist die VM in VirtualBox eingerichtet. Mit VmWare hat es nicht geklappt, da es noch eine extra Lizenz für VMWare gebraucht hätte

#### SSH Zugriff
Cd in das Verzeichnis der VM:

vagrant ssh

## 20 Infrastruktur-Autorsierung 
### Markdown

Markdownlight Extension heruntegeladen und, wie man an diesem Beispiel hier sieht eingerichtet.

