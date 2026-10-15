# Inhoud
- [Begrippen](#begrippen)
- [Install](#install)
    * [Pannel](#pannel)
    * [Configuratie in de pannel](#configuratie-in-de-pannel)
        - [Locatie toevoegen](#locatie-toevoegen)
        - [Node toevoegen](#node-toevoegen)
            * [Basic Details](#basic-details)
            * [Configuration](#configuration)
        - [Instellen van Wings](#instellen-van-wings)
        - [Poorten range](#poorten-range)
        - [Controle op node health](#controle-op-node-health)
        - [Servers](#servers)
    * [Wings](#wings)

# Begrippen
pterodactyl terminologie [ZIE HIER](https://pterodactyl.io/project/terms.html)
- **Pannel**: De pterodactyl pannel of ook de web-interface van pterodactyl
- **Node**: Een physieke machine die een instance van wings geïnstalleerd heeft
- **Wings**: De service die een connectie met docker en de pannel maakt en toegang geeft om servers te bewerken
- **Nest**: Het type server die geïnstalleerd wordt
- **Eggs**: Het sub-type van de server bv. Vanilla, Spigot of Bungeecord voor Minecraft
- **Yolks**: Een collectie docker images

# Install
Vooraleer er begonnen wordt met installeren is er enkele informatie nodig voor de installatie

- Installatie gebeurt via de super user (su)
- Opstarten van de wings gebeurt via de gewone gebruiker met sudo wings of via sudo systemctl start wings.service
- De pannel en wings(Node) kunnen op volledig apparte servers draaien
- Er kunnen meerder Eggs(Game Server) geïnstalleerd worden per wing(Node)

Voor de installatie gebruiken we een script die afkomstig is van github van de gebruiker [Pterodactyl Installer](https://github.com/pterodactyl-installer).

Deze installer maakt het verzamelen van dependencies veel gemakkelijker, de installer maakt het ook mogelijk om beide pannel en wings op dezelfde server te installeren in een keer.

Om dus de installer uit te voeren moeten we eerst als super-user inloggen met ```su```

---
**LET OP!** <br> als de installer niet wordt uitgevoerd als ```su```, maar als ```sudo``` krijg je de volgende error:

```sh
user@debian:~$ sudo bash <(curl -s https://pterodactyl-installer.se)
[sudo] password for user: 
bash: /dev/fd/63: No such file or directory
```
---


Vervolgens is het mogelijk om het commando ```bash <(curl -s https://pterodactyl-installer.se)``` uit te voeren, de installer detecteerd automatisch de OS versie en gebruikt de correcte packages.

Vervolgens wordt de keuze gegeven wat er wordt geïnstalleerd:

```sh
* Retrieving release information...
######################################################################
* Pterodactyl panel installation script @ v1.3.0
* 
* Copyright (C) 2018 - 2026, Vilhelm Prytz, <vilhelm@prytznet.se>
* https://github.com/pterodactyl-installer/pterodactyl-installer
* 
* This script is not associated with the official Pterodactyl Project.
* 
* Running debian version 13.
######################################################################
* What would you like to do?
* [0] Install the panel
* [1] Install Wings
* [2] Install both [0] and [1] on the same machine (wings script runs after panel)
* [3] Install panel with canary version of the script (the versions that lives in master, may be broken!)
* [4] Install Wings with canary version of the script (the versions that lives in master, may be broken!)
* [5] Install both [3] and [4] on the same machine (wings script runs after panel)
* [6] Uninstall panel or wings with canary version of the script (the versions that lives in master, may be broken!)
* Input 0-6: 
```

## Pannel
Het script zal achter enkele items vragen:
```sh
* Database name (panel): 
* Database username (pterodactyl): 
* Password (press enter to use randomly generated password): 
* List of valid timezones here https://www.php.net/manual/en/timezones.php
* Select timezone [Europe/Stockholm]: Europe/Brussels
* Provide the email address that will be used to configure Let's Encrypt and Pterodactyl:
* Email address for the initial admin account:
* Username for the initial admin account:
* First name for the initial admin account:
* Last name for the initial admin account:
* Password for the initial admin account:
########################################################################
* Set the FQDN of this panel (panel.example.com):
* Do you want to automatically configure UFW (firewall)? (y/N): 
* Do you want to automatically configure HTTPS using Let's Encrypt? (y/N): 
* Let's Encrypt is not going to be automatically configured by this script (user opted out).
* You can 'assume' Let's Encrypt, which means the script will download a nginx configuration that is configured to use a Let's Encrypt certificate but the script won't obtain the certificate for you.
* If you assume SSL and do not obtain the certificate, your installation will not work.
* Assume SSL or not? (y/N): 
* Pterodactyl Panel collects anonymous telemetry data to help steer the development.
* More Info: https://pterodactyl.io/panel/1.0/additional_configuration.html#telemetry
* Enable sending anonymous telemetry data? (yes/no) [yes]: no
##############################################################
* Pterodactyl panel v1.15.1 with nginx on debian
* Database name: [database name]
* Database user: [database user]
* Database password: (censored)
* Timezone: [Timezone]
* Email: [email]
* User email: [user email]
* Username: [username]
* First name: [first name]
* Last name: [last name]
* User password: (censored)
* Hostname/FQDN: [FQDN]
* Configure Firewall? false
* Configure Let's Encrypt? false
* Assume SSL? false
* Telemetry: false
##############################################################

* Initial configuration completed. Continue with installation? (y/N):
```

## Configuratie in de pannel
Open het pterodactyl pannel in de browser met ```http://[host]``` en log in met de aangemaakte user.

Druk nu op de 3 tandwieltjes in de rechterboven hoek om de admin instellingen te openen.

### Locatie toevoegen
Voor we beginnen aan een node aan de pannel te koppelen moeten we eerst een locatie toevoegen.

Onder ***Management -> Locations*** creëer een nieuwe locatie. Vul een korte code in die gemakkelijk te herkennen is welke locatie het is onder "short code" en druk op **Create**. (Als het nodig is kan je ook nog een beschrijving erbij voegen.)

---

### Node toevoegen
Ga naar ***Management -> Nodes*** en druk op *Create New* bij "Node List".

Nu komt de lastigste stap van all, hiering gaan we de resources van de node gaan definiëeren, **LET OP** Je kan meer memmory gaan toekennen dan de VM toegang tot heeft.

#### Basic Details
Onder *Basic Details* geef de node een naam, en plaats deze onder de locatie die net werd aangemaakt in de vorige stap. 

Voor gemak zet de *Node Visibility* op Public.

Voor de FQDN vul het host address van de node waarop **Wings** is geïnstalleerd, indien er geen SSL wordt gebruikt kan er ook gebruik gemaakt worden van een IP-address.

In het geval dat de Pannel en Wings op dezelfde host actief zijn kan je zelf gebruik maken van de localhost (127.0.0.1).

#### Configuration
Onder *Configuration* zijn instellingen te vinden omtrent memmory, disk en management poorten

Afhankelijk hoe de partities van het systeem waarop wings is geïnstalleerd is het mischien nodig om de *Server File Directory* aan te passen. Als alles in 1 partitie is geïnstalleerd is het niet noodzakelijk om dit aan te passen.

De *Total Memory* instelling wordt gebruikt om aan te duiden hoeveel memmory alle geïnstalleerde servers in totaal mogen gebruiken. *Memory Over-Allocation* staat toe om iets meer memmory te gebruiken te gaan toekennen voor de servers (stel er is nog 3GB aan memmory over en je wil nog een server met 4GB opstarten, *Over-Allocation* staat toe om toch nog die 4GB te gaan toekennen).

*Total Disk Space* is de totale schijfruimte die alle servers mogen gebruiken. *Disk Over-Allocation* doet opnieuw hetzelfde als *Memory Over-Allocation* maar met de schijfruimte.

**LET OP** bij gebruik van SSL is het mogelijk dat de *Daemon Port* moet veranderd worden naar 443(HTTPS).

Druk op **Create Node**.

---

### Instellen van Wings
**Voordat deze stap wordt uitgevoerd, zorg ervoor dat [Wings](#wings) geïnstallerd is.**

In de nieuwe aangemaakte node navigeer naar de tab *Configuration* en kopiëer de inhoud van het *Configuration File* veld.

Op het systeem van wings navigeer naar ```/etc/pterodactyl/``` en maak/edit het config.yml bestand. Plak de gekopiëerde inhoud in dit bestand en sla het op.

Start wings op met ```sudo systemctl start wings```.

---

### Poorten range
In de tab *Allocation* staan alle poorten die gereserveerd zijn voor servers.

Om nieuwe poorten toe te voegen, vul onder *Assign New Allocations* het ip address van de wings server in en in het veld *Ports* vul de range in, in de vorm van [start]-[einde] (bv. 32000-32099).
En druk **Submit**.

---

### Controle op node health
Ga terug naar ***Management -> Nodes***

Zorg ervoor dat het hartje naast de node naam groen is.

Als het hartje rood is controleer de voorgaande instellingen. Klik op de node naam en controleer ofdat er informatie over de server in de *About* tab staan, indien dit zo is herlaad de pagina en controleer of het hartje veranderd is naar groen.

---

### Servers
Onder ***Management -> Servers*** is waar alle servers zullen worden ingesteld. De instellingen van servers kunnen verschillen, dus wordt dit niet besproken in dit document.

---

## Wings

Installeren van de Wing is minder straight forward.
Om de wings correct in te stellen moet eerst de configuratie aan de pannel kant voltooid worden, maar installatie kan wel al.

```sh
* The installer will install Docker, required dependencies for Wings
* as well as Wings itself. But it's still required to create the node
* on the panel and then place the configuration file on the node manually after
* the installation has finished. Read more about this process on the
* official documentation: https://pterodactyl.io/wings/1.0/installing.html#configure
* 
* Note: this script will not start Wings automatically (will install systemd service, not start it).
* Note: this script will not enable swap (for docker).
##########################################
* Do you want to automatically configure UFW (firewall)? (y/N): 
* Do you want to automatically configure a user for database hosts? (y/N): 

* WARNING: You cannot use Let's Encrypt with your hostname as an IP address! It must be a FQDN (e.g. node.example.org).

* Do you want to automatically configure HTTPS using Let's Encrypt? (y/N): 
* Proceed with installation? (y/N): y
```

Om te testen of wings geïnstallerd is kan je gebruik maken van ```sudo wings```, **LET OP** zonder configuratie zal dit een error geven zoals:
```sh
Error: Configuration File Not Found

Wings was not able to locate your configuration file, and therefore is not
able to complete its boot process. Please ensure you have copied your instance
configuration file into the default location below.

Default Location: /etc/pterodactyl/config.yml

This is not a bug with this software. Please do not make a bug report
for this issue, it will be closed.
```

---

# Sources
- [pterodactyl docs](https://pterodactyl.io/panel/1.0/getting_started.html#installation)
- [pterodactyl-installer](https://github.com/pterodactyl-installer/pterodactyl-installer)