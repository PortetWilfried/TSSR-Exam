# 🐧 Linux Debian — Commandes Exam

## 📑 Sommaire

- [[#👤 Utilisateurs & Groupes|👤 Utilisateurs & Groupes]]
- [[#🔒 Droits — chmod & setfacl|🔒 Droits — chmod & setfacl]]
- [[#📁 Créer fichiers / dossiers + droits|📁 Créer fichiers / dossiers + droits]]
- [[#🔐 SSH — sshd_config|🔐 SSH — sshd_config]]
- [[#⏰ Crontab|⏰ Crontab]]
- [[#📊 Vérifier Processus / RAM / Disque|📊 Vérifier Processus / RAM / Disque]]
- [[#🌐 Réseau Linux|🌐 Réseau Linux]]
- [[#🔥 Pare-feu|🔥 Pare-feu]]
- [[#📦 Paquets|📦 Paquets]]
- [[#💾 Sauvegarde / Copie|💾 Sauvegarde / Copie]]
- [[#⚙️ Services (systemctl)|⚙️ Services (systemctl)]]
- [[#🔄 Redémarrer le système|🔄 Redémarrer le système]]
- [[#🌐 Stack LAMP — Installation rapide|🌐 Stack LAMP — Installation rapide]]
- [[#🔧 Stack LAMP — Troubleshooting|🔧 Stack LAMP — Troubleshooting]]
- [[#🩺 Méthode diagnostic Linux — Bloc complet Phase 2|🩺 Méthode diagnostic Linux — Phase 2]]
- [[#🔗 Connexions inter-thèmes|🔗 Connexions inter-thèmes]]

---

## 👤 Utilisateurs & Groupes

```bash
adduser martin                    # Créer user (interactif)
passwd martin                     # Changer mot de passe
usermod -aG sudo martin           # Ajouter au groupe sudo
usermod -aG www-data martin       # Ajouter au groupe web
id martin                         # Vérifier groupes
deluser martin                    # Supprimer user
groupadd devs                     # Créer un groupe
```

---

## 🔒 Droits — chmod & setfacl

```bash
# chmod
chmod 755 fichier      # proprio=rwx, groupe=r-x, autres=r-x
chmod 644 fichier      # proprio=rw-, groupe=r--, autres=r--
chmod 770 dossier      # proprio+groupe=rwx, autres=rien
chmod 771 dossier      # proprio+groupe=rwx, autres=--x (traverser)
chown user:groupe fic  # Changer propriétaire
chmod -R 755 dossier/  # Récursif

# Table mémo
# r=4  w=2  x=1
# 7=rwx  6=rw-  5=r-x  4=r--  0=---

# setfacl (droits fins)
setfacl -m u:martin:rwx /data/projet        # Donner rwx à martin (existant)
setfacl -R -m u:martin:rwx /data/projet     # Récursif sur fichiers existants
setfacl -d -m u:martin:rwx /data/projet     # Par défaut pour futurs fichiers
setfacl -m g:devs:rx /data/projet           # Donner rx au groupe devs
getfacl /data/projet                         # Voir les ACL
setfacl -x u:martin /data/projet            # Supprimer ACL martin
```

> **Mémo setfacl :** `-m` = règle actuelle | `-R` = récursif sur l'existant | `-d` = droits par défaut pour le futur

> ⚠️ `chmod 777` = faille de sécurité. À ne **jamais** utiliser en prod.

---

## 📁 Créer fichiers / dossiers + droits

```bash
# Dossiers
mkdir /data/projet              # Créer un dossier
mkdir -p /data/projet/sub/test  # Créer une arborescence complète (-p)
rmdir /data/vide                # Supprimer un dossier vide
rm -rf /data/projet             # Supprimer un dossier et son contenu

# Fichiers
touch fichier.txt               # Créer un fichier vide
echo "texte" > fichier.txt      # Créer/écraser avec contenu
echo "ligne 2" >> fichier.txt   # Ajouter à la fin
nano fichier.txt                # Éditer
cat fichier.txt                 # Afficher
rm fichier.txt                  # Supprimer

# Appliquer droits + propriétaire dans la foulée
mkdir /data/rh
chown root:rh /data/rh          # Propriétaire = root, groupe = rh
chmod 770 /data/rh              # rwx pour proprio+groupe, rien pour autres
setfacl -m u:martin:rwx /data/rh

# Vérifier ce qu'on vient de faire
ls -la /data/rh                 # Voir droits + propriétaire
getfacl /data/rh                # Voir les ACL
```

> **Séquence type exam :**
> 1. Créer (`mkdir` ou `touch`)
> 2. Donner propriétaire (`chown`)
> 3. Donner droits (`chmod` ou `setfacl`)
> 4. Vérifier (`ls -la` + `getfacl`)
> 5. **Screenshot du `ls -la`** comme preuve

---

## 🔐 SSH — sshd_config

### ⚠️ Toujours sauvegarder avant de modifier

```bash
cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
```

> **Phrase jury :** "Avant de modifier un fichier de configuration critique, je crée une copie de sauvegarde. Si la modification casse le service, je peux restaurer rapidement."

### Fichiers critiques à sauvegarder systématiquement

```bash
cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
cp /etc/apache2/apache2.conf /etc/apache2/apache2.conf.bak
cp /etc/mysql/mariadb.conf.d/50-server.cnf /etc/mysql/mariadb.conf.d/50-server.cnf.bak
```

### Modifier la config

```bash
nano /etc/ssh/sshd_config
```

```bash
Port 2222                        # Changer le port (défaut 22)
PermitRootLogin no               # Interdire connexion root
PasswordAuthentication no        # Forcer clés SSH uniquement
PubkeyAuthentication yes         # Activer clés publiques
DenyUsers test alice             # Blacklister des users
MaxAuthTries 3                   # Limiter tentatives
```

### Séquence propre après modification

```bash
sshd -t                          # 1. Tester la syntaxe AVANT restart
systemctl restart ssh            # 2. Redémarrer
systemctl status ssh             # 3. Vérifier l'état
ss -tlnp | grep ':2222'          # 4. Confirmer que SSH écoute sur le bon port
journalctl -u ssh -n 50          # 5. Vérifier les logs
```

### Si la config est cassée → restaurer

```bash
cp /etc/ssh/sshd_config.bak /etc/ssh/sshd_config
sshd -t
systemctl restart ssh
```

### Clés SSH — emplacement et permissions

```bash
mkdir -p /home/martin/.ssh
nano /home/martin/.ssh/authorized_keys   # Coller la clé publique ici

chown -R martin:martin /home/martin/.ssh
chmod 700 /home/martin/.ssh
chmod 600 /home/martin/.ssh/authorized_keys
```

> La clé publique doit être dans `/home/user/.ssh/authorized_keys` avec des droits stricts (700/600). Sans ça, SSH refuse l'authentification par clé même si la config est correcte.

---

## ⏰ Crontab

```bash
crontab -e    # Éditer (pour l'utilisateur courant)
crontab -l    # Lister
crontab -u martin -e   # Éditer pour un autre user
```

**Syntaxe : `minute heure jour_mois mois jour_semaine commande`**

```bash
# Exemples
0 2 * * *     /usr/bin/apt-get upgrade -y           # Tous les jours à 2h
0 0 1 * *     /scripts/backup.sh                    # 1er du mois à minuit
*/5 * * * *   /scripts/check.sh                     # Toutes les 5 min
0 8-18 * * 1-5 /scripts/task.sh                    # 8h-18h, lun-ven

# Bonne pratique : rediriger les logs
0 4 * * * /usr/local/bin/backup.sh >> /var/log/backup.log 2>&1
```

> ⚠️ **Piège du `%`** : dans une crontab, `%` doit être échappé `\%`. Le plus propre = utiliser un script externe.

**Vérifications cron :**
```bash
crontab -l
systemctl status cron
cat /var/log/backup.log
```

---

## 📊 Vérifier Processus / RAM / Disque

```bash
# Processus
top                              # Vue temps réel (q pour quitter)
ps aux --sort=-%cpu | head       # Top processus CPU
ps aux --sort=-%mem | head       # Top processus RAM
ps aux | grep apache2            # Chercher un process spécifique
kill PID                         # Arrêt propre d'abord
kill -9 PID                      # Forcer seulement si nécessaire

# RAM
free -h                          # Mémoire libre/utilisée

# Disque
df -h                            # Espace disque par partition
du -sh /var/log                  # Taille d'un dossier

# Vue combinée rapide
echo "=RAM="; free -h; echo "=DISQUE="; df -h
```

> Tenter d'abord `kill PID` (arrêt propre), puis `kill -9` seulement si le process ne répond pas.

---

## 🌐 Réseau Linux

```bash
ip a                         # Voir les adresses IP
ip r                         # Voir la route/passerelle
ping 8.8.8.8                 # Tester connectivité IP
ping google.com              # Tester DNS + réseau
cat /etc/resolv.conf         # Voir DNS configuré
nslookup intranet.local      # Tester résolution DNS interne
curl -I http://localhost      # Tester service web local
```

> Si l'IP fonctionne mais pas le nom → penser **DNS** en premier.

---

## 🔥 Pare-feu

```bash
ufw status                   # Voir les règles actives
iptables -L -n -v            # Vue détaillée (si ufw non utilisé)

# Ajouter des règles ufw
ufw allow 22/tcp             # ⚠️ TOUJOURS en premier !
ufw allow 2222/tcp
ufw allow 80/tcp
ufw allow 443/tcp
ufw enable
ufw status
```

> ⚠️ Ne jamais `ufw enable` sans avoir d'abord autorisé le port SSH → sinon tu te coupes de la machine.

---

## 📦 Paquets

```bash
apt update               # Mettre à jour la liste
apt upgrade -y           # Mettre à jour les paquets
apt install apache2 -y   # Installer
apt remove apache2       # Supprimer (garde config)
apt purge apache2        # Supprimer + config
```

---

## 💾 Sauvegarde / Copie

```bash
cp fichier.txt /backup/                     # Copier un fichier
cp -r /data/ /backup/data_$(date +%F)/      # Copier dossier avec date
tar -czf backup.tar.gz /data/               # Archiver + compresser
tar -tzf backup.tar.gz                      # Vérifier le contenu sans extraire
tar -xzf backup.tar.gz -C /tmp/restore      # Restaurer dans un dossier
rsync -av /source/ /destination/            # Synchro intelligente
```

> Une sauvegarde est validée seulement si on sait la **restaurer**. Toujours tester avec `tar -tzf` puis `tar -xzf -C`.

---

## ⚙️ Services (systemctl)

```bash
systemctl start apache2      # Démarrer
systemctl stop apache2       # Arrêter
systemctl restart apache2    # Redémarrer
systemctl reload apache2     # Recharger config sans restart
systemctl enable apache2     # Démarrer au boot
systemctl status apache2     # Vérifier l'état
systemctl list-units --type=service   # Tous les services
```

> `enable` ≠ `start` — toujours faire les deux.

---

## 🔄 Redémarrer le système

```bash
reboot                  # Redémarrer
shutdown -r now         # Redémarrer immédiatement
shutdown -h now         # Éteindre
```

---

## 🌐 Stack LAMP — Installation rapide

```bash
apt update && apt upgrade -y

# Apache
apt install apache2 -y
systemctl enable apache2 && systemctl start apache2

# MariaDB
apt install mariadb-server -y
systemctl enable mariadb && systemctl start mariadb
mysql_secure_installation

# PHP + module Apache (important !)
apt install php libapache2-mod-php php-mysql php-curl php-gd php-mbstring php-xml php-zip -y
systemctl restart apache2

# Vérifier PHP
php -v
dpkg -l | grep php
dpkg -l | grep libapache2-mod-php
```

---

## 🔧 Stack LAMP — Troubleshooting

### Diagnostic rapide

```bash
# Vérifier l'état des services
systemctl status apache2
systemctl status mariadb
php -v                               # PHP installé ?
dpkg -l | grep libapache2-mod-php   # Module PHP Apache présent ?

# Tester Apache
curl -I localhost                    # Doit retourner HTTP/1.1 200 OK
curl localhost                       # Doit afficher la page HTML

# Créer une page de test HTML
echo "<h1>Apache fonctionne !</h1>" > /var/www/html/test.html
curl localhost/test.html             # Vérifier en CLI
# Ou dans le navigateur : http://IP_DU_SERVEUR/test.html
rm /var/www/html/test.html           # Supprimer après vérification

# Tester PHP
echo "<?php phpinfo(); ?>" > /var/www/html/test.php
curl localhost/test.php              # Si le code PHP s'affiche brut → module PHP absent
rm /var/www/html/test.php

# Tester MariaDB
mysql -u root -p
SHOW DATABASES;
SELECT user, host FROM mysql.user;
SHOW GRANTS FOR 'user'@'localhost';
EXIT;
```

> Si le code PHP s'affiche brut → Apache n'a pas le module PHP → `apt install libapache2-mod-php -y` puis `systemctl restart apache2`.

### Vérifier la config Apache

```bash
apache2ctl configtest               # Tester la config avant restart
systemctl restart apache2
journalctl -u apache2 -n 50
```

> Si Apache ne démarre pas → toujours commencer par `apache2ctl configtest`.

### Logs — où regarder

```bash
tail -f /var/log/apache2/error.log      # Erreurs Apache (le plus utile)
tail -f /var/log/apache2/access.log     # Accès Apache
tail -f /var/log/mysql/error.log        # Erreurs MariaDB
journalctl -u apache2 -xe
journalctl -u mariadb -xe
```

### Problèmes fréquents

| Symptôme | Cause probable | Correction |
|----------|---------------|-----------|
| Page blanche / erreur 500 | PHP mal installé ou erreur script | `tail -f /var/log/apache2/error.log` |
| PHP affiché brut | Module `libapache2-mod-php` absent | `apt install libapache2-mod-php -y` + restart apache2 |
| "Connection refused" port 80 | Apache arrêté | `systemctl start apache2` |
| "Can't connect to MySQL" | MariaDB arrêté | `systemctl start mariadb` |
| Apache ne démarre pas | Erreur config ou port 80 occupé | `apache2ctl configtest` puis `ss -tlnp \| grep 80` |
| GLPI erreur base de données | Mauvais user/mdp BDD | Vérifier `/var/www/html/glpi/config/config_db.php` |

### Vérifications ports

```bash
ss -tlnp | grep ':80'
ss -tlnp | grep ':3306'
ss -tlnp | grep ':22'
ss -tlnp | grep ':2222'
```

### Permissions web (souvent oubliées)

```bash
chown -R www-data:www-data /var/www/html/
chmod -R 755 /var/www/html/
```

---

## 🩺 Méthode diagnostic Linux — Bloc complet Phase 2

```bash
# 1. Réseau
ip a && ip r
ping 8.8.8.8
nslookup intranet.local

# 2. Services
systemctl status ssh
systemctl status apache2
systemctl status mariadb

# 3. Ports
ss -tlnp | grep -E ':22|:2222|:80|:443|:3306'

# 4. Ressources
free -h && df -h
ps aux --sort=-%cpu | head
ps aux --sort=-%mem | head

# 5. Logs
journalctl -u ssh -n 50
journalctl -u apache2 -n 50
journalctl -u mariadb -n 50
```

---

## 🔗 Connexions inter-thèmes

> **chmod ↔ NTFS** → même besoin, philosophies différentes. Linux = octal, Windows = ACL graphique
> **sshd_config ↔ ANSSI** → chaque directive a une raison sécuritaire (pas de root, port non-standard)
> **crontab ↔ Tâches planifiées Windows** → même concept, syntaxe opposée
> **GLPI ↔ Stack LAMP** → GLPI tourne sur Apache + MariaDB + PHP → si l'un tombe, GLPI est KO
