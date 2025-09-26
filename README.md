# asterisk-debian-labo

## Description
Laboratoire Asterisk 20 sur Debian 12 (Bookworm) configuré pour tester des extensions SIP depuis un iPhone et des clients locaux sur un réseau LAN.  
Ce projet inclut l’installation, la compilation, la configuration de base et la préparation des règles de pare-feu nécessaires pour Asterisk.

---

## Système utilisé
- OS : Debian GNU/Linux 12 (Bookworm)
- Asterisk : 20.15.2
- Virtualisation : VirtualBox (mode réseau Pont pour accès LAN)
- Client SIP : iPhone ou autre softphone

---

## Étapes réalisées

### 1. Installation des dépendances
```bash
sudo apt update
sudo apt install -y build-essential libncurses5-dev libssl-dev libxml2-dev libsqlite3-dev wget git
sudo apt install -y sngrep
2. Téléchargement et compilation d’Asterisk
bash
Copier le code
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
bash
Copier le code
sudo systemctl enable --now asterisk
sudo systemctl status asterisk
sudo asterisk -rvvv
core show version
4. Configuration réseau et pare-feu
Utilisation de VirtualBox en mode Pont pour que la VM soit sur le même LAN que les clients.

Ouverture des ports nécessaires pour SIP et RTP :

bash
Copier le code
sudo ufw enable
sudo ufw allow 5060/udp        # SIP
sudo ufw allow 10000:20000/udp # RTP
Ping LAN autorisé : (si UFW supporte ICMP)

bash
Copier le code
sudo iptables -A INPUT -p icmp -s 192.168.1.0/24 -j ACCEPT
sudo netfilter-persistent save


