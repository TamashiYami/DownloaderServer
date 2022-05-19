# DownloaderServer

## Sommaire
- [DownloaderServer](#downloaderserver)
  - [Sommaire](#sommaire)
  - [I. Installation](#i-installation)
    - [A. Plex](#a-plex)
    - [B. Radarr](#b-radarr)
    - [C. Sonarr](#c-sonarr)
    - [D. Jacket](#d-jacket)
    - [E. Transmission](#e-transmission)

## I. Installation 

Pour commencer la mise en place de notre service, il faut d'abord tout installer. Quand on dit tout installer on parle de:

- Plex : pour jouer le rôle de lecteur et de repertoire
- Radarr : pour installer des films
- Sonarr : pour installer des séries
- Jacket : pour indéxer

<br>

### A. Plex
<br>
Plex est service qui nous permet d'organiser les films, séries et musiques téléchargés de manière propre. De plus Plex nous sert également de  lecteur multimédia.

Afin de l'installer, il faut suivre les commandes suivantes: 

-> ```sudo apt update && sudo apt upgrade -y```

-> ```sudo apt install apt-transport-https curl wget -y```

```
tamashi-infra@InfraServer:~$ sudo apt install apt-transport-https curl wget -y
Lecture des listes de paquets... Fait
Construction de l'arbre des dépendances... Fait
Lecture des informations d'état... Fait
wget est déjà la version la plus récente (1.21-1+deb11u1).
Les NOUVEAUX paquets suivants seront installés :
  apt-transport-https curl
0 mis à jour, 2 nouvellement installés, 0 à enlever et 0 non mis à jour.
Il est nécessaire de prendre 427 ko dans les archives.
Après cette opération, 602 ko d'espace disque supplémentaires seront utilisés.
Réception de :1 http://deb.debian.org/debian bullseye/main amd64 apt-transport-https all 2.2.4 [160 kB]
Réception de :2 http://deb.debian.org/debian bullseye/main amd64 curl amd64 7.74.0-1.3+deb11u1 [267 kB]
427 ko réceptionnés en 0s (1 373 ko/s)
Sélection du paquet apt-transport-https précédemment désélectionné.
(Lecture de la base de données... 139872 fichiers et répertoires déjà installés.)
Préparation du dépaquetage de .../apt-transport-https_2.2.4_all.deb ...
Dépaquetage de apt-transport-https (2.2.4) ...
Sélection du paquet curl précédemment désélectionné.
Préparation du dépaquetage de .../curl_7.74.0-1.3+deb11u1_amd64.deb ...
Dépaquetage de curl (7.74.0-1.3+deb11u1) ...
Paramétrage de apt-transport-https (2.2.4) ...
Paramétrage de curl (7.74.0-1.3+deb11u1) ...
Traitement des actions différées (« triggers ») pour man-db (2.9.4-2) ...
```

-> sudo wget -O- https://downloads.plex.tv/plex-keys/PlexSign.key | gpg --dearmor | sudo tee /usr/share/keyrings/plex.gpg

```
tamashi-infra@InfraServer:~$ sudo wget -O- https://downloads.plex.tv/plex-keys/PlexSign.key | gpg --dearmor | sudo tee /usr/share/keyrings/plex.gpg
--2022-04-19 10:56:09--  https://downloads.plex.tv/plex-keys/PlexSign.key
Résolution de downloads.plex.tv (downloads.plex.tv)… 104.18.34.20, 172.64.153.236, 2606:4700:4400::ac40:99ec, ...
Connexion à downloads.plex.tv (downloads.plex.tv)|104.18.34.20|:443… connecté.
requête HTTP transmise, en attente de la réponse… 200 OK
Taille : 3072 (3,0K) [binary/octet-stream]
Sauvegarde en : « STDOUT »

-                             100%[=================================================>]   3,00K  --.-KB/s    ds 0s

2022-04-19 10:56:10 (40,2 MB/s) — envoi vers sortie standard [3072/3072]

[...]

d&��( ��S�W��L}����K�����NĘ�t|(D�����=���O��H"���~��7���ez�ŵO;�ʘe� �S�����F�Z;ZԼ�@�B�e�̔�U����3��J��(l�x��9��i�VM�Y��v�}C��a�Z��hB`Ď�A?-��p��hl�@��@CG���;��ީ�?Hi���8
```
-> ```echo deb [signed-by=/usr/share/keyrings/plex.gpg] https://downloads.plex.tv/repo/deb public main | sudo tee /etc/apt/sources.list.d/plexmediaserver.list```

```
tamashi-infra@InfraServer:~$ echo deb [signed-by=/usr/share/keyrings/plex.gpg] https://downloads.plex.tv/repo/deb public main | sudo tee /etc/apt/sources.list.d/plexmediaserver.list
deb [signed-by=/usr/share/keyrings/plex.gpg] https://downloads.plex.tv/repo/deb public main
```

-> ```sudo apt update```

```
tamashi-infra@InfraServer:~$ sudo apt update
Atteint :1 http://security.debian.org/debian-security bullseye-security InRelease
Atteint :2 http://deb.debian.org/debian bullseye InRelease
Atteint :3 http://deb.debian.org/debian bullseye-updates InRelease
Réception de :4 https://downloads.plex.tv/repo/deb public InRelease [6 685 B]
Réception de :5 https://downloads.plex.tv/repo/deb public/main amd64 Packages [464 B]
7 149 o réceptionnés en 1s (12,2 ko/s)
Lecture des listes de paquets... Fait
Construction de l'arbre des dépendances... Fait
Lecture des informations d'état... Fait
Tous les paquets sont à jour.
```

-> ```sudo apt install plexmediaserver```
    Faire Oui à la première question et Non à la seconde.

-> ```systemctl status plexmediaserver```

```
tamashi-infra@InfraServer:~$ systemctl status plexmediaserver
● plexmediaserver.service - Plex Media Server
     Loaded: loaded (/lib/systemd/system/plexmediaserver.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2022-04-19 11:00:39 CEST; 3min 6s ago
    Process: 3949 ExecStartPre=/bin/sh -c /usr/bin/test -d "${PLEX_MEDIA_SERVER_APPLICATION_SUPPORT_DIR}" || /bin/mkdir -p "${PLEX_MEDIA_SERVER_APPLICATION_SUPPORT_DIR}" (code=exited, status=0/SUCCESS)
   Main PID: 3954 (Plex Media Serv)
      Tasks: 110 (limit: 9267)
     Memory: 221.8M
        CPU: 20.775s
     CGroup: /system.slice/plexmediaserver.service
             ├─3954 /usr/lib/plexmediaserver/Plex Media Server
             ├─4022 Plex Plug-in [com.plexapp.system] /usr/lib/plexmediaserver/Resources/Plug-ins-965587f64/Framework.bundle/Contents/Resources/Versions/2/Python/bootstrap.py --server-version 1.25.9.5721-9655>
             ├─4079 /usr/lib/plexmediaserver/Plex Tuner Service /usr/lib/plexmediaserver/Resources/Tuner/Private /usr/lib/plexmediaserver/Resources/Tuner/Shared 1.25.9.5721-965587f64 32600
             ├─4099 Plex Plug-in [com.plexapp.agents.thetvdb] /usr/lib/plexmediaserver/Resources/Plug-ins-965587f64/Framework.bundle/Contents/Resources/Versions/2/Python/bootstrap.py --server-version 1.25.9.5>
             ├─4254 Plex Plug-in [org.musicbrainz.agents.music] /usr/lib/plexmediaserver/Resources/Plug-ins-965587f64/Framework.bundle/Contents/Resources/Versions/2/Python/bootstrap.py --server-version 1.25.9>
             ├─4261 Plex Plug-in [tv.plex.agents.music] /usr/lib/plexmediaserver/Resources/Plug-ins-965587f64/Framework.bundle/Contents/Resources/Versions/2/Python/bootstrap.py --server-version 1.25.9.5721-96>
             ├─4267 Plex Plug-in [tv.plex.agents.movie] /usr/lib/plexmediaserver/Resources/Plug-ins-965587f64/Framework.bundle/Contents/Resources/Versions/2/Python/bootstrap.py --server-version 1.25.9.5721-96>
             ├─4427 Plex Plug-in [tv.plex.agents.series] /usr/lib/plexmediaserver/Resources/Plug-ins-965587f64/Framework.bundle/Contents/Resources/Versions/2/Python/bootstrap.py --server-version 1.25.9.5721-9>
             └─4497 Plex Plug-in [com.plexapp.agents.imdb] /usr/lib/plexmediaserver/Resources/Plug-ins-965587f64/Framework.bundle/Contents/Resources/Versions/2/Python/bootstrap.py --server-version 1.25.9.5721>
lines 1-18/18 (END)
```

Et voilà, notre logiciel Plex est installé.

<br>

### B. Radarr

<br>

Radarr est un service qu'il nous permet de rechercher et d'installer des films. 
Pour l'installer:

-> ```sudo apt install curl sqlite3```

```
tamashi-infra@InfraServer:~$ sudo apt install curl sqlite3
[sudo] Mot de passe de tamashi-infra :
Lecture des listes de paquets... Fait
Construction de l'arbre des dépendances... Fait
Lecture des informations d'état... Fait
curl est déjà la version la plus récente (7.74.0-1.3+deb11u1).
Paquets suggérés :
  sqlite3-doc
Les NOUVEAUX paquets suivants seront installés :
  sqlite3
0 mis à jour, 1 nouvellement installés, 0 à enlever et 0 non mis à jour.
Il est nécessaire de prendre 1 201 ko dans les archives.
Après cette opération, 3 155 ko d'espace disque supplémentaires seront utilisés.
Souhaitez-vous continuer ? [O/n] O
Réception de :1 http://deb.debian.org/debian bullseye/main amd64 sqlite3 amd64 3.34.1-3 [1 201 kB]
1 201 ko réceptionnés en 1s (1 893 ko/s)
Sélection du paquet sqlite3 précédemment désélectionné.
(Lecture de la base de données... 142454 fichiers et répertoires déjà installés.)
Préparation du dépaquetage de .../sqlite3_3.34.1-3_amd64.deb ...
Dépaquetage de sqlite3 (3.34.1-3) ...
Paramétrage de sqlite3 (3.34.1-3) ...
Traitement des actions différées (« triggers ») pour man-db (2.9.4-2) ...
```

-> Exécuter le script [ArrInstall](./ArrInstall.sh)

```
tamashi-infra@InfraServer:~$ sudo bash ArrInstall.sh
Running \*Arr Install Script - Version [3.0.5] as of [2022-04-03]
Select the application to install:
1) lidarr
2) prowlarr
3) radarr
4) readarr
5) whisparr
6) quit
#? 3
It is critical that the user and group you select to run Radarr as will have READ and WRITE access to your Media Library and Download Client Completed Folders
What user should Radarr run as? (Default: radarr):
What group should Radarr run as? (Default: media):
Radarr selected
This will install [Radarr] to [/opt/Radarr] and use [/var/lib/radarr/] for the AppData Directory
Radarr will run as the user [radarr] and group [media]. By continuing, you've confirmed that that user and group will have READ and WRITE access to your Media Library and Download Client Completed Download directories
Continue with the installation [Yes/No]?
1) Yes
2) No
#? 1
Ajout de l'utilisateur système « radarr » (UID 118) ...
Ajout du nouvel utilisateur « radarr » (UID 118) avec pour groupe d'appartenance « media » ...
Le répertoire personnel « /home/radarr » n'a pas été créé.
Created and added User [radarr] to Group [media]
User [radarr] did not exist in Group [media]
Added User [radarr] to Group [media]
Directories created
[...]


Install complete
Browse to http://10.0.2.15:7878 for the Radarr GUI
```

Pour l'utiliser il faut se rendre sur *localhost:7878*

Dans un second temps, nous allons créer un dossier dans l'espace /home/radarr. Nous allons créer le dossier movie (dans /home/radarr) qui doit être accesible à l'utilisateur radarr.

<br>

### C. Sonarr

<br>

Sonarr, à l'instar de Radarr, est un service de recherches et de téléchargements mais cette fois-ci de séries et non de films. 
Pour l'installer:

-> `sudo apt install gnupg ca-certificates`

```
tamashi-infra@InfraServer:~$ sudo apt install gnupg ca-certificates
Lecture des listes de paquets... Fait
Construction de l'arbre des dépendances... Fait
Lecture des informations d'état... Fait
ca-certificates est déjà la version la plus récente (20210119).
gnupg est déjà la version la plus récente (2.2.27-2+deb11u1).
0 mis à jour, 0 nouvellement installés, 0 à enlever et 0 non mis à jour.
```

-> ```sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF```

```
tamashi-infra@InfraServer:~$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
Warning: apt-key is deprecated. Manage keyring files in trusted.gpg.d instead (see apt-key(8)).
Executing: /tmp/apt-key-gpghome.nRRtQJ2SrI/gpg.1.sh --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
gpg: clef A6A19B38D3D831EF : clef publique « Xamarin Public Jenkins (auto-signing) <releng@xamarin.com> » importée
gpg:       Quantité totale traitée : 1
gpg:                     importées : 1
```

-> ```wget https://mediaarea.net/repo/deb/repo-mediaarea_1.0-19_all.deb && sudo dpkg -i repo-mediaarea_1.0-19_all.deb```

```
tamashi-infra@InfraServer:~$ wget https://mediaarea.net/repo/deb/repo-mediaarea_1.0-19_all.deb && sudo dpkg -i repo-mediaarea_1.0-19_all.deb
--2022-04-19 12:20:15--  https://mediaarea.net/repo/deb/repo-mediaarea_1.0-19_all.deb
Résolution de mediaarea.net (mediaarea.net)… 51.75.207.234, 2001:41d0:305:2100::7e38
Connexion à mediaarea.net (mediaarea.net)|51.75.207.234|:443… connecté.
requête HTTP transmise, en attente de la réponse… 200 OK
Taille : 4908 (4,8K) [application/vnd.debian.binary-package]
Sauvegarde en : « repo-mediaarea_1.0-19_all.deb »

repo-mediaarea_1.0-19_all.deb 100%[=================================================>]   4,79K  --.-KB/s    ds 0s

2022-04-19 12:20:15 (133 MB/s) — « repo-mediaarea_1.0-19_all.deb » sauvegardé [4908/4908]

Sélection du paquet repo-mediaarea précédemment désélectionné.
(Lecture de la base de données... 142463 fichiers et répertoires déjà installés.)
Préparation du dépaquetage de repo-mediaarea_1.0-19_all.deb ...
Dépaquetage de repo-mediaarea (1.0-19) ...
Paramétrage de repo-mediaarea (1.0-19) ...
ERROR: unable to identify debian version, guessing buster
```

-> ```sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 2009837CBFFD68F45BC180471F4F90DE2A9B4BF8```

```
tamashi-infra@InfraServer:~$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 2009837CBFFD68F45BC180471F4F90DE2A9B4BF8
Warning: apt-key is deprecated. Manage keyring files in trusted.gpg.d instead (see apt-key(8)).
Executing: /tmp/apt-key-gpghome.YIAwTtuTaU/gpg.1.sh --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 2009837CBFFD68F45BC180471F4F90DE2A9B4BF8
gpg: clef 1F4F90DE2A9B4BF8 : clef publique « Sonarr <hello@sonarr.tv> » importée
gpg:       Quantité totale traitée : 1
gpg:                     importées : 1
```

-> ```echo "deb https://apt.sonarr.tv/ubuntu focal main" | sudo tee /etc/apt/sources.list.d/sonarr.list```

```
tamashi-infra@InfraServer:~$ echo "deb https://apt.sonarr.tv/ubuntu focal main" | sudo tee /etc/apt/sources.list.d/sonarr.list
deb https://apt.sonarr.tv/ubuntu focal main
```

-> ```sudo apt update```

-> ```sudo apt install sonarr```

```
tamashi-infra@InfraServer:~$ sudo apt install sonarr
Lecture des listes de paquets... Fait
Construction de l'arbre des dépendances... Fait
Lecture des informations d'état... Fait
Les paquets supplémentaires suivants seront installés :
  binfmt-support binutils binutils-common binutils-x86-64-linux-gnu ca-certificates-mono cli-common libbinutils
  libctf-nobfd0 libctf0 libgdiplus libmediainfo0v5 libmono-accessibility4.0-cil libmono-btls-interface4.0-cil
  libmono-corlib4.5-cil libmono-corlib4.5-dll libmono-i18n-west4.0-cil libmono-i18n4.0-cil libmono-ldap4.0-cil
  libmono-messaging4.0-cil libmono-microsoft-csharp4.0-cil libmono-posix4.0-cil libmono-security4.0-cil
  libmono-sqlite4.0-cil libmono-system-componentmodel-dataannotations4.0-cil
  libmono-system-configuration-install4.0-cil libmono-system-configuration4.0-cil libmono-system-core4.0-cil
  libmono-system-data-datasetextensions4.0-cil libmono-system-data4.0-cil libmono-system-design4.0-cil
  libmono-system-drawing4.0-cil libmono-system-enterpriseservices4.0-cil libmono-system-identitymodel-selectors4.0-cil
  libmono-system-identitymodel4.0-cil libmono-system-io-compression4.0-cil libmono-system-ldap4.0-cil
  libmono-system-messaging4.0-cil libmono-system-net-http4.0-cil libmono-system-numerics4.0-cil
  libmono-system-runtime-serialization-formatters-soap4.0-cil libmono-system-runtime-serialization4.0-cil
  libmono-system-security4.0-cil libmono-system-servicemodel-activation4.0-cil
  libmono-system-servicemodel-internals0.0-cil libmono-system-servicemodel4.0a-cil
  libmono-system-serviceprocess4.0-cil libmono-system-transactions4.0-cil
  libmono-system-web-applicationservices4.0-cil libmono-system-web-services4.0-cil libmono-system-web4.0-cil
  libmono-system-windows-forms4.0-cil libmono-system-xml-linq4.0-cil libmono-system-xml4.0-cil libmono-system4.0-cil
  libmono-webbrowser4.0-cil libzen0v5 mono-4.0-gac mono-gac mono-runtime mono-runtime-common mono-runtime-sgen
Paquets suggérés :
  binutils-doc libmono-i18n4.0-all libgnomeui-0 libgamin0 mediainfo
Paquets recommandés :
  libgluezilla
Les NOUVEAUX paquets suivants seront installés :
  binfmt-support binutils binutils-common binutils-x86-64-linux-gnu ca-certificates-mono cli-common libbinutils
  libctf-nobfd0 libctf0 libgdiplus libmediainfo0v5 libmono-accessibility4.0-cil libmono-btls-interface4.0-cil
  libmono-corlib4.5-cil libmono-corlib4.5-dll libmono-i18n-west4.0-cil libmono-i18n4.0-cil libmono-ldap4.0-cil
  libmono-messaging4.0-cil libmono-microsoft-csharp4.0-cil libmono-posix4.0-cil libmono-security4.0-cil
  libmono-sqlite4.0-cil libmono-system-componentmodel-dataannotations4.0-cil
  libmono-system-configuration-install4.0-cil libmono-system-configuration4.0-cil libmono-system-core4.0-cil
  libmono-system-data-datasetextensions4.0-cil libmono-system-data4.0-cil libmono-system-design4.0-cil
  libmono-system-drawing4.0-cil libmono-system-enterpriseservices4.0-cil libmono-system-identitymodel-selectors4.0-cil
  libmono-system-identitymodel4.0-cil libmono-system-io-compression4.0-cil libmono-system-ldap4.0-cil
  libmono-system-messaging4.0-cil libmono-system-net-http4.0-cil libmono-system-numerics4.0-cil
  libmono-system-runtime-serialization-formatters-soap4.0-cil libmono-system-runtime-serialization4.0-cil
  libmono-system-security4.0-cil libmono-system-servicemodel-activation4.0-cil
  libmono-system-servicemodel-internals0.0-cil libmono-system-servicemodel4.0a-cil
  libmono-system-serviceprocess4.0-cil libmono-system-transactions4.0-cil
  libmono-system-web-applicationservices4.0-cil libmono-system-web-services4.0-cil libmono-system-web4.0-cil
  libmono-system-windows-forms4.0-cil libmono-system-xml-linq4.0-cil libmono-system-xml4.0-cil libmono-system4.0-cil
  libmono-webbrowser4.0-cil libzen0v5 mono-4.0-gac mono-gac mono-runtime mono-runtime-common mono-runtime-sgen sonarr
0 mis à jour, 62 nouvellement installés, 0 à enlever et 0 non mis à jour.
Il est nécessaire de prendre 28,7 Mo dans les archives.
Après cette opération, 113 Mo d'espace disque supplémentaires seront utilisés.
Souhaitez-vous continuer ? [O/n]O
[...]
```

Suite à cette commande, une interface s'ouvre. A titre personnel, nous avons nommé l'utilisateur sonarr et il fera parti du groupe media (tout comme radarr)

Vérifier que le service est activé et *enabled*

Pour l'utiliser il faut se rendre sur *localhost:8989*

<br>

### D. Jacket

<br>


Jackett est un logiciel d'indéxation nous permettant d’ajouter Sonarr et Radarr à seul endroit (Jackett), sans avoir à visiter chaque site individuellement chaque fois que l'on recherche une émission ou un film.

-> Se rendre dans /opt en root

-> `dpkg --print-architecture`

```
root@InfraServer:/opt# dpkg --print-architecture
amd64
```

-> `v=$(wget -q https://api.github.com/repos/Jackett/Jackett/releases/latest -O - | grep -E  tag_name | awk -F '[""]' '{print $4}')`

-> `wget https://github.com/Jackett/Jackett/releases/download/$v/Jackett.Binaries.LinuxAMDx64.tar.gz`

```
root@InfraServer:/opt# wget https://github.com/Jackett/Jackett/releases/download/$v/Jackett.Binaries.LinuxAMDx64.tar.gz
--2022-04-22 10:03:48--  https://github.com/Jackett/Jackett/releases/download/v0.20.933/Jackett.Binaries.LinuxAMDx64.tar.gz
Résolution de github.com (github.com)… 140.82.121.3
Connexion à github.com (github.com)|140.82.121.3|:443… connecté.
requête HTTP transmise, en attente de la réponse… 302 Found
Emplacement : https://objects.githubusercontent.com/github-production-release-asset-2e65be/45195614/d2f626a1-0a56-4995-b909-a37bbac83bde?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20220422%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20220422T080348Z&X-Amz-Expires=300&X-Amz-Signature=1a7ba26e2a414c2cce131b76b86df78a710c7f60356512566743e44664305ba7&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=45195614&response-content-disposition=attachment%3B%20filename%3DJackett.Binaries.LinuxAMDx64.tar.gz&response-content-type=application%2Foctet-stream [suivant]
--2022-04-22 10:03:48--  https://objects.githubusercontent.com/github-production-release-asset-2e65be/45195614/d2f626a1-0a56-4995-b909-a37bbac83bde?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20220422%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20220422T080348Z&X-Amz-Expires=300&X-Amz-Signature=1a7ba26e2a414c2cce131b76b86df78a710c7f60356512566743e44664305ba7&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=45195614&response-content-disposition=attachment%3B%20filename%3DJackett.Binaries.LinuxAMDx64.tar.gz&response-content-type=application%2Foctet-stream
Résolution de objects.githubusercontent.com (objects.githubusercontent.com)… 185.199.108.133, 185.199.109.133, 185.199.110.133, ...
Connexion à objects.githubusercontent.com (objects.githubusercontent.com)|185.199.108.133|:443… connecté.
requête HTTP transmise, en attente de la réponse… 200 OK
Taille : 45897486 (44M) [application/octet-stream]
Sauvegarde en : « Jackett.Binaries.LinuxAMDx64.tar.gz »

Jackett.Binaries.LinuxAMDx64. 100%[=================================================>]  43,77M  12,3MB/s    ds 3,6s

2022-04-22 10:03:52 (12,3 MB/s) — « Jackett.Binaries.LinuxAMDx64.tar.gz » sauvegardé [45897486/45897486]
```

-> `tar xvf Jackett.Binaries.LinuxAMDx64.tar.gz`

-> `rm Jackett.Binaries.LinuxAMDx64.tar.gz`

-> `adduser jackett --system --group --shell=/bin/sh`

```
root@InfraServer:~# adduser jackett --system --group --shell=/bin/sh
Ajout de l'utilisateur système « jackett » (UID 120) ...
Ajout du nouveau groupe « jackett » (GID 126) ...
Ajout du nouvel utilisateur « jackett » (UID 120) avec pour groupe d'appartenance « jackett » ...
Création du répertoire personnel « /home/jackett »...
```

-> `chown -R jackett:jackett /opt/Jackett`

->  `./install_service_systemd.sh`

```
root@InfraServer:/opt/Jackett# ./install_service_systemd.sh
Checking if the service 'jackett.service' is running ...
Service 'jackett.service' is not running
Jackett will be installed in '/opt/Jackett'
Jackett will be executed with the user 'jackett'
Creating Jackett unit file in '/etc/systemd/system/jackett.service' ...
Installing Jackett service ...
Created symlink /etc/systemd/system/multi-user.target.wants/jackett.service → /etc/systemd/system/jackett.service.
Service successfully installed and launched!
```

-> Se rendre sur *localhost:9117*

-> Aller dans  *Add indexer*

-> Puis selectionner _1337X_

-> Copier le *Torznab Seed*

-> Se rendre sur Sonarr et Radarr

-> *Settings*

-> Dans indexer *ADD*

-> Selectionner Torznab

-> Dans URL coller le lien copier plus tôt

-> Revenir dans Jacket

-> Copier l'API KEY (en haut à droite)

-> Revenir dans Radarr/Sonarr

-> Coller l'API KEY dans *API KEY*

-> Nommer votre indexer

Et voilà, l'installation de Jacket est terminé.

<br>

### E. Transmission

<br>

Cette technologie permet le téléchargement et la création de torrents.

-> sudo apt-get install transmission-daemon

```
tamashi-infra@InfraServer:~$ sudo apt-get install transmission-daemon
Lecture des listes de paquets... Fait
Construction de l'arbre des dépendances... Fait
Lecture des informations d'état... Fait
Les paquets supplémentaires suivants seront installés :
  transmission-cli
Les NOUVEAUX paquets suivants seront installés :
  transmission-cli transmission-daemon
0 mis à jour, 2 nouvellement installés, 0 à enlever et 0 non mis à jour.
Il est nécessaire de prendre 650 ko dans les archives.
Après cette opération, 3 332 ko d'espace disque supplémentaires seront utilisés.
Souhaitez-vous continuer ? [O/n] O
Réception de :1 http://deb.debian.org/debian bullseye/main amd64 transmission-daemon amd64 3.00-1 [228 kB]
Réception de :2 http://deb.debian.org/debian bullseye/main amd64 transmission-cli amd64 3.00-1 [422 kB]
650 ko réceptionnés en 0s (1 658 ko/s)
Sélection du paquet transmission-daemon précédemment désélectionné.
(Lecture de la base de données... 143546 fichiers et répertoires déjà installés.)
Préparation du dépaquetage de .../transmission-daemon_3.00-1_amd64.deb ...
Dépaquetage de transmission-daemon (3.00-1) ...
Sélection du paquet transmission-cli précédemment désélectionné.
Préparation du dépaquetage de .../transmission-cli_3.00-1_amd64.deb ...
Dépaquetage de transmission-cli (3.00-1) ...
Paramétrage de transmission-daemon (3.00-1) ...
Created symlink /etc/systemd/system/multi-user.target.wants/transmission-daemon.service → /lib/systemd/system/transmission-daemon.service.
Paramétrage de transmission-cli (3.00-1) ...
Traitement des actions différées (« triggers ») pour man-db (2.9.4-2) ...
```

-> sudo useradd -d /home/MrDownload -m MrDownload

-> sudo usermod -a -G debian-transmission MrDownload

->  sudo mkdir /home/MrDownload/download

-> sudo mkdir /home/MrDownload/incomplete

-> sudo chown -R MrDownload:debian-transmission /home/MrDownload

-> sudo chown -R MrDownload:debian-transmission /home/MrDownload/incomplete

-> sudo chown -R MrDownload:debian-transmission /home/MrDownload/download

-> sudo chmod -R 775 /home/MrDownload

-> sudo systemctl stop transmission-daemon.service

-> sudo nano /etc/transmission-daemon/settings.json

-> Modifier les lignes suivantes:

  - rpc-password -> y mettre ce que vous souhaitez
  - rpc-username -> celui que vous souhaitez
  - rcp-whitelist-enabled -> false

-> sudo systemctl status transmission-daemon.service

-> Se rendre sur Radarr/Sonarr (*localhost:7878*)

-> *Settings*

-> *Download Client*


-> *Add*

-> Remplir les champs *username* et *password* avec ce que vous avez mis dans le fichiers config de transmission-daemon

Maintenant vous avez Transmission d'installer et de configuré, vous pouvez vous rendre sur *localhost:9091*

