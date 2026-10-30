# ⛏️ Minecraft Server – Network Experience 26-27

![Server icon](server-icon.png)

Dit project is ontstaan binnen het vak **Network Experience** van de opleiding **Elektronica-ICT** aan **VIVES Brugge**. Het doel was om een volledige Minecraft (survival) server op te zetten voor de studenten van Elektronica-ICT, inclusief beheerpaneel, permissiesysteem, moderatietools en automatisering.

Deze repository bevat de volledige documentatie van de opzet: van de hardware en het beheerpaneel tot de plugins, de configuratie en de n8n-automatisering.

---

## 📑 Inhoud

- [Overzicht](#-overzicht)
- [Architectuur](#-architectuur)
- [Structuur van de repository](#-structuur-van-de-repository)
- [1. Installatie](#1-installatie)
- [2. Server Settings](#2-server-settings)
- [3. n8n Automatisering](#3-n8n-automatisering)
- [Pluginoverzicht](#-pluginoverzicht)
- [Ranks & permissies](#-ranks--permissies)
- [Belangrijke aandachtspunten](#-belangrijke-aandachtspunten)
- [Auteurs](#-auteurs)

---

## 🧭 Overzicht

| Onderdeel | Details |
|---|---|
| **Vak** | Network Experience |
| **Opleiding** | Elektronica-ICT, VIVES Brugge |
| **Serversoftware** | Paper |
| **Minecraft-versie** | 26.1.2 |
| **Beheerpaneel** | Pterodactyl (Panel + Wings) |
| **Virtualisatie** | Proxmox VE |
| **Automatisering** | n8n (samenwerking met het project *n8n-automation-hub*) |
| **Plugins** | 18 plugins, gedownload via [Modrinth](https://modrinth.com/) |

---

## 🏗️ Architectuur

```text
┌──────────────────────────────────────────────┐
│  Proxmox node (i7-9700KF, 2 SSD's in RAID1)  │
│  ┌────────────────────────────────────────┐  │
│  │  VM: Debian                            │  │
│  │   ├─ Pterodactyl Panel (web-interface) │  │
│  │   └─ Wings  ──►  Docker  ──►  Minecraft│  │
│  │                              (Paper)   │  │
│  └────────────────────────────────────────┘  │
└──────────────────────────────────────────────┘
                      ▲
                      │ API (Pterodactyl API Key)
                      │
                  ┌───┴───┐
                  │  n8n  │  whitelist + moderatie
                  └───────┘
```

- **Proxmox** levert de virtualisatielaag.
- **Pterodactyl** beheert de Minecraft-server via een webpaneel. Wings start de server in een Docker-container.
- **n8n** praat via de Pterodactyl API met de server om commando's uit te voeren.

---

## 📂 Structuur van de repository

```text
.
├── 1.Installatie/
│   ├── nieuwe_proxmox_setup.md
│   └── install_pterodactyl.md
├── 2.Server_Settings/
│   ├── 0.Handige_Commands.md
│   ├── 1.Plugins_Installeren.md
│   ├── 2.LuckPerms_Instellen.md
│   ├── 3.Server_Settings.md
│   ├── 4_0.Plugins_Settings.md
│   ├── 4_1.TAB_Plugin.md
│   ├── 4_2.GriefPrevention.md
│   ├── 4_3.EssentialsX.md
│   ├── 4_4.WorldGuard&WorldEdit.md
│   ├── 4_5.Chunky.md
│   ├── 4_6.PlaceholderAPI.md
│   ├── 4_7.SuperVanish_TAB.md
│   └── Plugins 26.1.2/
│       ├── Plugins.txt       (Links van de plugins)
│       └── 26.1.2/           (de .jar-bestanden van alle plugins)
├── 3.n8n_Automation/
│   ├── 1.n8n_Automation.md
│   ├── 2.n8n_API_Key.md
│   └── *.png                 (screenshots van het Pterodactyl-paneel)
├── server-icon.png
└── README.md
```

---

## 1. Installatie

📁 [`1.Installatie`](1.Installatie)

| Document | Inhoud |
|---|---|
| [nieuwe_proxmox_setup.md](1.Installatie/nieuwe_proxmox_setup.md) | Waarom er een nieuwe Proxmox-node kwam (een desktop-CPU met betere single-core score dan de oude server-CPU) en hoe het opstartprobleem van de installer (`Loading init ramfs ...`) werd opgelost door de BIOS te updaten. |
| [install_pterodactyl.md](1.Installatie/install_pterodactyl.md) | Installatie van het Pterodactyl Panel en Wings met het community-installatiescript, plus het aanmaken van een locatie, node, poortenrange en het controleren van de node health. |

**Kort samengevat:**

1. Proxmox installeren op de nieuwe node (eerst de BIOS updaten naar de laatste versie).
2. Een Debian-VM aanmaken.
3. Pterodactyl Panel en Wings installeren met het script `bash <(curl -s https://pterodactyl-installer.se)` (uitvoeren als `su`, **niet** met `sudo`).
4. In het paneel een locatie en node aanmaken, de Wings-configuratie kopiëren naar `/etc/pterodactyl/config.yml` en Wings starten.
5. Poorten toewijzen en de Minecraft-server aanmaken.

---

## 2. Server Settings

📁 [`2.Server_Settings`](2.Server_Settings)

| Document | Inhoud |
|---|---|
| [0.Handige_Commands.md](2.Server_Settings/0.Handige_Commands.md) | Overzicht van de belangrijkste commando's voor staff en admins. |
| [1.Plugins_Installeren.md](2.Server_Settings/1.Plugins_Installeren.md) | Plugins downloaden, uploaden via Pterodactyl en de server correct herstarten. |
| [2.LuckPerms_Instellen.md](2.Server_Settings/2.LuckPerms_Instellen.md) | De volledige rankstructuur en permissies met LuckPerms. |
| [3.Server_Settings.md](2.Server_Settings/3.Server_Settings.md) | Paper-instellingen, server-icon, commando-aliassen, `/info` en `/help`, anti-xray en command blocks. |
| [4_0.Plugins_Settings.md](2.Server_Settings/4_0.Plugins_Settings.md) | Overzicht van de kernplugins en de locatie van hun configuratiebestanden. |
| [4_1.TAB_Plugin.md](2.Server_Settings/4_1.TAB_Plugin.md) | Tablijst, header/footer en de geanimeerde VIVES-website. |
| [4_2.GriefPrevention.md](2.Server_Settings/4_2.GriefPrevention.md) | Land claims en claim blocks. |
| [4_3.EssentialsX.md](2.Server_Settings/4_3.EssentialsX.md) | Starterkit en welkomstbericht voor nieuwe spelers. |
| [4_4.WorldGuard&WorldEdit.md](2.Server_Settings/4_4.WorldGuard&WorldEdit.md) | De spawn beveiligen met regio's en flags. |
| [4_5.Chunky.md](2.Server_Settings/4_5.Chunky.md) | De wereld vooraf genereren (pre-generation) om lag te vermijden. |
| [4_6.PlaceholderAPI.md](2.Server_Settings/4_6.PlaceholderAPI.md) | PlaceholderAPI installeren en de Server-module activeren. |
| [4_7.SuperVanish_TAB.md](2.Server_Settings/4_7.SuperVanish_TAB.md) | `[V]`-tag in de tablijst en de vanish-hiërarchie tussen staff-ranks. |

---

## 3. n8n Automatisering

📁 [`3.n8n_Automation`](3.n8n_Automation)

Voor het automatiseren van beheertaken werd samengewerkt met de studenten van het project **n8n-automation-hub**.

**Geautomatiseerd whitelist-proces**
De docent genereert via n8n een tijdelijke aanmeldlink en deelt die met de studenten. Wie zijn Minecraft-gebruikersnaam invult, komt automatisch op de whitelist. De docent bepaalt:

- hoelang de link geldig blijft;
- hoeveel studenten zich maximaal kunnen registreren;
- hoelang de whitelist geldig is (met automatische *unwhitelist* na afloop).

**Moderatie via n8n**
Kicken, muten, warnen en bannen (op gebruikersnaam) kunnen uitgevoerd worden zonder in de serverconsole te werken.

> ⚠️ **Geen IP-bans.** Het verkeer van alle VIVES-campussen loopt via de centrale router van VIVES Kortrijk en Cloudflare. Door Source NAT ziet de server voor elke speler hetzelfde IP-adres. Eén IP-ban zou dus **alle** spelers blokkeren.

| Document | Inhoud |
|---|---|
| [1.n8n_Automation.md](3.n8n_Automation/1.n8n_Automation.md) | Uitleg over de whitelist-automatisering en de moderatiemogelijkheden. |
| [2.n8n_API_Key.md](3.n8n_Automation/2.n8n_API_Key.md) | Stappenplan (met screenshots) om een Pterodactyl API Key aan te maken voor n8n. |

---

## 🔌 Pluginoverzicht

Alle plugins zijn gedownload van Modrinth voor **Paper 26.1.2** (kanaal *Release*). De bronlinks staan in [`Plugins.txt`](<2.Server_Settings/Plugins 26.1.2/Plugins.txt>) en de `.jar`-bestanden in de map [`Plugins 26.1.2`](<2.Server_Settings/Plugins 26.1.2/26.1.2>).

| Categorie | Plugin | Functie |
|---|---|---|
| **Compatibiliteit** | [ViaVersion](https://modrinth.com/plugin/viaversion) | Nieuwere clients kunnen verbinden. |
| | [ViaBackwards](https://modrinth.com/plugin/viabackwards) | Oudere clients kunnen verbinden. |
| **Permissies & basis** | [LuckPerms](https://modrinth.com/plugin/luckperms) | Ranks, groepen, prefixes en rechten. |
| | [EssentialsX](https://modrinth.com/plugin/essentialsx) | Basiscommando's (`/home`, `/tpa`, `/msg`, ...). |
| | [EssentialsX Chat](https://modrinth.com/plugin/essentialsx-chat-module) | Chatopmaak met prefixes. |
| | [EssentialsX Spawn](https://modrinth.com/plugin/essentialsx-spawn) | `/spawn` en de startlocatie. |
| | [VaultUnlocked](https://modrinth.com/plugin/vaultunlocked) | Brug tussen economie-, chat- en permissieplugins. |
| | [PlaceholderAPI](https://modrinth.com/plugin/placeholderapi) | Gedeelde variabelen tussen plugins. |
| **Moderatie** | [LibertyBans](https://modrinth.com/plugin/libertybans) | Bans, mutes, warns, kicks en geschiedenis. |
| | [CoreProtect](https://modrinth.com/plugin/coreprotect) | Logging en rollbacks tegen griefing. |
| | [SuperVanish](https://modrinth.com/plugin/supervanish) | Onzichtbare staff. |
| | [GrimAC](https://modrinth.com/plugin/grimac) | Anti-cheat. |
| **Bescherming & bouwen** | [WorldGuard](https://modrinth.com/plugin/worldguard) | Beschermde regio's (bv. de spawn). |
| | [WorldEdit](https://modrinth.com/plugin/worldedit) | Bouwtools (vereist voor WorldGuard). |
| | [GriefPrevention](https://modrinth.com/plugin/griefprevention) | Land claims met een gouden schep. |
| **Prestatie & info** | [Chunky](https://modrinth.com/plugin/chunky) | Wereld vooraf genereren. |
| | [Plan](https://modrinth.com/plugin/plan) | Statistieken en spelersanalyse via webpagina. |
| | [TAB](https://modrinth.com/plugin/tab-was-taken) | Tablijst, prefixes en geanimeerde website. |

---

## 🛡️ Ranks & permissies

De rechten zijn opgebouwd via **overerving** in LuckPerms. Elke rank erft alles van de rank eronder.

```text
[default] (0)
   └── [player] (10)
         └── [helper] (30)
               └── [mod] (40)
                     └── [admin] (50)
```

| Rank | Doel |
|---|---|
| `default` | Nieuwe bezoekers zonder rank. Zeer beperkt: enkel `/help`, `/info`, privéberichten en `/report`. |
| `player` | Reguliere survival-spelers: `/spawn`, homes en teleportatieverzoeken. |
| `helper` | Eerste lijn van moderatie: warnen, muten, kicken, inspecteren en vanishen (level 1). |
| `mod` | Ervaren staff: rollbacks, strengere sancties, spy-tools en vanish level 2. |
| `admin` | Volledige controle via de `*` wildcard en vanish level 3. |

Het volledige overzicht staat in [2.LuckPerms_Instellen.md](2.Server_Settings/2.LuckPerms_Instellen.md).

---

## ⚠️ Belangrijke aandachtspunten

- **Gebruik nooit `/reload` of `/rl`.** Dit veroorzaakt geheugenlekken en kan plugins breken. Gebruik de plugin-specifieke reload (bv. `/tab reload`, `/ess reload`) of de **Restart**-knop in Pterodactyl.
- **Pterodactyl installeren als `su`**, niet met `sudo`. Anders krijg je de fout `bash: /dev/fd/63: No such file or directory`.
- **Geen IP-bans** door de Source NAT-configuratie van het schoolnetwerk (zie [n8n Automatisering](#3-n8n-automatisering)).
- **Moderatiecommando's van EssentialsX staan uitgeschakeld** (`ban`, `mute`, `kick`, `unban`) zodat LibertyBans ze kan overnemen. Dit wordt aangevuld met aliassen in `commands.yml`.
- **Na wijzigingen aan `paper-global.yml` of `commands.yml`** is een volledige herstart van de server nodig.
- **Bewaar API Keys veilig** en beperk ze tot het IP-adres van de n8n-server.

---

## 👥 Auteurs

- *Bram De Bevere*
- *Hengda Lin*
- *Kevin Bakeland*
- *Miel Villyn*

- *Jason De Smet* `n8n automation`

**Opleiding:** Elektronica-ICT, VIVES Brugge
**Vak:** Network Experience
**Academiejaar:** 2026-2027

---

## 📚 Bronnen

- [Pterodactyl documentatie – Panel installeren (1.x)](https://docs.pterodactyl.io/v1/panel/getting-started)
- [Pterodactyl documentatie – Wings installeren (1.x)](https://docs.pterodactyl.io/v1/wings/installing)
- [Pterodactyl Installer](https://github.com/pterodactyl-installer/pterodactyl-installer)
- [Modrinth](https://modrinth.com/)
- [PaperMC](https://papermc.io/)

