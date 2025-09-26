Asterisk Debian Labo

Laboratoire Asterisk 20 sur Debian 12 (Bookworm) configuré pour tester des extensions SIP depuis un iPhone et un client Android ou d’autres softphones sur un réseau LAN.

Ce projet inclut l’installation, la compilation, la configuration de base et la préparation des règles de pare-feu nécessaires pour Asterisk.

Système utilisé

OS : Debian GNU/Linux 12 (Bookworm)

Asterisk : 20.15.2

Virtualisation : VirtualBox (mode réseau Pont pour accès LAN)

Clients SIP : iPhone et Android

Étapes réalisées
1. Installation des dépendances
sudo apt update
sudo apt install -y build-essential libncurses5-dev libssl-dev libxml2-dev libsqlite3-dev wget git
sudo apt install -y sngrep

2. Téléchargement et compilation d’Asterisk
cd /usr/src
wget http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-20.15.2.tar.gz
tar xvfz asterisk-20.15.2.tar.gz
cd asterisk-20.15.2
sudo ./configure
sudo make
sudo make install
sudo make samples
sudo make config
sudo ldconfig

3. Démarrage et vérification d’Asterisk
sudo systemctl enable --now asterisk
sudo systemctl status asterisk
sudo asterisk -rvvv
core show version

4. Configuration réseau et pare-feu

VirtualBox en mode Pont pour que la VM soit sur le même LAN que les clients.

Ouverture des ports SIP et RTP :

sudo ufw enable
sudo ufw allow 5060/udp        # SIP
sudo ufw allow 10000:20000/udp # RTP


Autoriser le ping depuis le LAN (ICMP) :

sudo iptables -A INPUT -p icmp -s 192.168.1.0/24 -j ACCEPT
sudo netfilter-persistent save

Structure du projet
asterisk-debian-labo/
│
├─ configs/              # fichiers de configuration Asterisk
│   ├─ pjsip.conf
│   └─ extensions.conf
│
├─ scripts/              # scripts pour démarrage, tests
│   └─ start-asterisk.sh
│
├─ docs/                 # documentation et guides
│   └─ README.md
│
└─ .gitignore            # fichiers à ignorer (logs, backups)

Configuration des endpoints (pjsip.conf)
Client	Nom d’utilisateur	Mot de passe	IP/Serveur	Port	Transport	Numéro à composer
iPhone	alice	Alice1234!	IP_VM	5060	UDP	1001 → Bob
Android	bob	Bob1234!	IP_VM	5060	UDP	1000 → Alice
Dialplan (extensions.conf)
[from-internal]
; iPhone (Alice) appelle Android (Bob)
exten => 1001,1,Dial(PJSIP/bob,20)

; Android (Bob) appelle iPhone (Alice)
exten => 1000,1,Dial(PJSIP/alice,20)

Instructions pour tester

Copier les fichiers de configs/ dans /etc/asterisk/ :

sudo cp configs/*.conf /etc/asterisk/


Redémarrer ou recharger Asterisk :

sudo systemctl restart asterisk
sudo asterisk -rx "core reload"


Configurer vos clients SIP (iPhone / Android) avec les paramètres du tableau.

Composer le numéro interne de l’autre client pour tester les appels.
