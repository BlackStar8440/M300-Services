# M300-Services
M300 Serverübergreifende Dienste

### Vorwissen
Vor diesem Modul wusst ich nichts über Vagrant, IaC, was für Funktionen GitHub alles bietet und vieles weitere. Weshalb ich sehr gespannt auf dieses Moudl und auf was ich alles neues lernen kann war.

### Wissenzuwachs
Alles, unten dokumentiert ist, war neu für mich. Ich habe in diesem Modul bis jetzt sehr viel gelernt. Vorallem Vagrant und die Möglichkeiten, welche es mit sich bringt war sehr interessant. Das man VMs mit verschiedenen Dingen automatisc und vor allem so schnell ohnen grossen Aufwand aufsetzen kann ist schon toll.

## 10 Toolumgebung aufsetzen
### GitHub Bash
Konfigurieren:

`git config --global user.name "username"`\
`git confug --global user.email "E-Mail"`

#### Befehle
##### Repository klonen
Cd in gewünschtes Repository, in das es kopiert werden sollte:

`git clone "URL vom Repository"`

Cd in das geklone Repository und Status abfragen:

`git pull`\
`git status`

##### Lokale Änderungen in Repository pushen
Cd in Repository\
`git add - A "Filename"`\
`git commit -m Kommentar (falls gewünscht)`\
`git push`\

### VSC
#### Extensions
Auf das Zeichen mit den 4 Vierecken auf der linken Seite oder ctrl + , --> Extension suchen und installieren

#### Lokale Änderungen in Repository pushen
Änderung mit ctrl + s speichern --> auf der linken Seite wird bei einem Icon eine 1 erscheinen --> Text Message eingeben --> auf Hacken drücken --> drei Punkte --> Push

### Vagrant
#### Vorbereitung
VirtualBox, Cmder und Vagranat herunterladen

#### Vagrant File erstellen
Voranmerkung: https://app.vagrantup.com/boxes/search hier findet man die VM Vorlagen, welche man benutzen kann.

cd Ort, an dem die VM abgelegt werden sollte und einen neuen Ordner erstellen:

`vagrant init ubuntu/xenial64` --> War das Beispiel in der Aufgabe

#### vagrant installieren und starten
`vagrant up --provider virtualbox`

Info: Dannach ist die VM in VirtualBox eingerichtet. Mit VmWare hat es nicht geklappt, da es noch eine extra Lizenz für VMWare gebraucht hätte

#### SSH Zugriff
Cd in das Verzeichnis der VM:
`vagrant ssh`

#### Vagrant Befehlesübersicht

Befehl | Erklärung | Beispiel
------------ | ------------- | -------------
`vagrant init` | Initalisiert im aktuellen Verzeichnis eine Vagrant Umgebung und falls noch kein Vagrant File vorhanden ist erstellt es dieses | `vagrant init ubuntu/xenial64`
`vagrant up` | Fährt diese VM hoch, in welchem Verzeichnis man das ausführt | `vagrant up --provider virtualbox` --> oder nur vagrant up
`vagrant ssh` | Baut eine SSH Verbindung mit dem VM auf | -
`vagrant status` | Zeigt den Status der VM an| -
`vagrant port` | Zeigt Port von PortForwarding an | -
`vagrant halt` | Stoppt VM | -
`vagrant destroy` | Löscht VM | -
`vagrant -v` | Zeigt Vagrant Version an | -


## 20 Infrastruktur-Autorsierung 
### Markdown

Markdownlight Extension heruntegeladen und, wie man an diesem Beispiel hier sieht eingerichtet.

#### Markdwon Tabellen machen
First Header | Second Header
------------ | -------------
Content cell 1 | Content cell 2
Content column 1 | Content column 2

#### Markdown Bild einfügen
\!\[alt text]\(url)

### Vagrant | Boxen
Boxen sind VM Vorlagen. Man kann diese direkt im cmder mit folgendem Befehl herunterladen:

`vagrant box add "box-name"` oder  `vagrant box add "box-url"`

Mit folgendem Befehl kann man Sie wieder entfernen

`vagrant box remove "box-name"`\
--> Der Box name wird folgendermassen aufgebaut: Entwickler/Box (z.B. ubuntu/xenial64)

##### Box Befehle
Box hinzufügen:\
`vagrant add "box-name"`

Lokal vorhandene Boxen anzeigen:\
`vagrant  box list`

### Packer
Packer: Tool, mit dem man Images bzw. Boxen für eine Vielzahl von Dynamic Infrastructure Platforms mit einer Konfdatei erstellen kann

#### Funktionsweise

Provisioning: Anweisung auf ein anderes Programm --> Eine Shell, wie z.B. Bash
Builder: Erstellt Image, für eine bestimmte dynamische Infrastruktur Plattform --> z.B. VirtualBoc
Post-processors: Bestandteile von Packer, welche das ERgebnis eines Bilders übernehmen

#### Installation 
Herunterladen und entpacken. Normalerweise müsste man den "Variabel-Pfad" ergänzen, jedoch hat es im cmd automatisch gepackt und mit "packer --version" kommt die Versions Angabe

### Vagrant Umgebung

#### Umgebung 
VM mit einem Vagrant Fiele ein Ubuntu Webserver aufsetzen. Folgenden Zeilen aus dem Script sind wichtig für den Webserver

Portforwarding für den Host lauft über den Port 3000:\
`config.vm.network "forwarded_port", guest:80, host:3000, auto_correct: true`

Ubuntu VM updaten und Webserver installieren:\
`sudo apt-get update`
`sudo apt-get -y install apache2 `

Ganzes Script:\
`Vagrant.configure(2) do |config|`\
  `config.vm.box = "ubuntu/\xenial64"`\
  `config.vm.network "forwarded_port", guest:80, host:3000, auto_correct: true`\
 `config.vm.synced_folder ".", "/var/www/html"  `\
`config.vm.provider "virtualbox" do |vb|`\
 `vb.memory = "512"`\
`end`\
`config.vm.provision "shell", inline: <<-SHELL`\
  `sudo apt-get update`\
  `sudo apt-get -y install apache2`\
`SHELL`\
`end`

#### Netzwerkplan
![alt text](https://github.com/BlackStar8440/M300-Services/blob/main/images/20_Netzwerkplan.PNG)

#### Funktionsweise/Testfälle

Zugriff via den Port 3000:
![alt text](https://github.com/BlackStar8440/M300-Services/blob/main/images/WebserverPort.PNG)

Wenn man das index.html File gelöscht wird sieht man das auf dem Host auch:
![alt text](https://github.com/BlackStar8440/M300-Services/blob/main/images/Webserver_ohneIndex.PNG)

Port Forwarding Port mit einer Shell anzeigen:
![alt text](https://github.com/BlackStar8440/M300-Services/blob/main/images/WebPortVagrant.png)

# 25 Infrastruktur-Sicherheit
Ich habe diesen ganzen Block in einem einzigen Vagrant File gemacht. Das beinhaltet Firewall Rules, Benutzer- und Rechtvergabe, SSH Zgriff via Putty und ein Reverse Proxy.

Das Vagrant File sieht folgendermassen aus:

```
Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/xenial64"
  config.vm.network "forwarded_port", guest:80, host:8080, auto_correct: true
  config.vm.synced_folder ".", "/var/www/html"  
config.vm.provider "virtualbox" do |vb|
  vb.memory = "512"  
end
config.vm.provision "shell", inline: <<-SHELL
	#VM Update
	sudo apt-get update
	#Apache Webserver installieren
	sudo apt-get -y install apache2
	#Firewall "enablen"
	sudo ufw --force enable
	#Port 22 und 2222 erlaunben/freischalten
	sudo ufw allow 22
	sudo ufw allow 2222
	#SSH-Service starten
	sudo systemctl start ssh
	#Im sshd_config FIle zwei "Statments" mit Ja austauschen, damit keine Fehlermeldung kommnt
	sudo sed -i "s/.*PasswordAuthentication.*/PasswordAuthentication yes/g" /etc/ssh/sshd_config
	sudo sed -i "s/.*ChallengeResponseAuthentication.*/ChallengeResponseAuthentication yes/g" /etc/ssh/sshd_config
	#Owner von edm Verzeichis /var/mail an den User vagrant geben
	sudo chown -c vagrant /var/mail
	#Nur noch den Besitzer (in diesem Fall vagrant) auf das Verzeichnis erlauben
	sudo chmod -R 700 /var/mail
	#Nginx --> Reverse-proxy installieren
	sudo apt-get -y install nginx
	#Virtual Host deaktivieren
	sudo unlink /etc/nginx/sites-enabled/default
	#File für Reverse-proxy erstellen
	sudo touch /etc/nginx/sites-available/reverse-proxy.conf
	#Inhalt in File einfügen
	cat <<%EOF% | sudo tee -a /etc/nginx/sites-available/reverse-proxy.conf
	server {
		listen 8080;
		location / {
			proxy_pass http://127.0.0.1;
		}
	}
%EOF%
	sudo ln -s /etc/nginx/sites-available/reverse-proxy.conf /etc/nginx/sites-enabled/reverse-proxy.conf
	#Apache Service stoppen
	sudo systemctl stop apache2
	#Nginx Service starten
	sudo systemctl restart nginx
	#VM rebooten
	sudo reboot
SHELL
end
```
### SSH
Um via Putty auf die VM zugreifen muss man localhost als IP angeben und den Port 2222, da SSH zu diesem "geforwardet" wird:
![alt text](https://github.com/BlackStar8440/M300-Services/blob/main/images/SSH_1.PNG)

 Danach kann man isch mit einem User in der Shell anmelden:
![alt text](https://github.com/BlackStar8440/M300-Services/blob/main/images/SSH_2.PNG)

### Reverse-Proxy
Der Reverse-Proxy kann man mit folgendem Befehl testen:

`sudo service nginx configtest`\

Wenn "Done" als Meldung kommt funktioniert der Reverse-Proxy:
![alt text](https://github.com/BlackStar8440/M300-Services/blob/main/images/Reverse-Proxy.PNG)

### Benutzer- und Rechtvergabe
`chown` = Besitzer von einem Verzeichnis/Ordner wechseln

Bsp. Vagrant File oben (-c gibt den User an): 
`sudo chown -c vagrant /var/mail`

`chmod` = Berechtigung auf ein Verzeichnis Ordner wechseln

Bsp. Vagrant File oben (Parameter unten erklärt):
`sudo chmod -R 700 /var/mail`

#### chmod Parameter
-R = Rekursiv --> Auf das Verzechis/Ordner und alles darunter

Nach -R wird mit den Drei Zahlen die Rechte vergeben.

Ziffer | Wer?
------------ | -------------
1 | Besitzer
2 | Gruppe
3 | Andere

Ziffer Bedeutung:

Zahl | Rechte
------------ | -------------
0 | Keine
1 | Nur Ausführen
2 | Nur Schreiben
3 | Schreiben und Ausführen
4 | Nur Lesen
5 | Lesen und Ausführen
6 | Lesen und Schreiben
7 | Voll

### Sicherheiten
In den Script wurden Sicherheiten in Form Benutzer- und Rechtvergabe, Port Rules auf der FW und ein Zugriff via SSH eingerichtet.