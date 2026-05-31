# 🐧 Linux Debian — Commandes Exam

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
chmod 771 fichier      # tous droits proprio+groupe, exécution autres
chown user:groupe fic  # Changer propriétaire
chmod -R 755 dossier/  # Récursif

# Table mémo
# r=4  w=2  x=1
# 7=rwx  6=rw-  5=r-x  4=r--  0=---

# setfacl (droits fins)
setfacl -m u:martin:rwx /data/projet   # Donner rwx à martin
setfacl -m g:devs:rx /data/projet      # Donner rx au groupe devs
getfacl /data/projet                    # Voir les ACL
setfacl -x u:martin /data/projet       # Supprimer ACL martin
```

---

## 🔐 SSH — sshd_config

**Fichier :** `/etc/ssh/sshd_config`

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

```bash
systemctl restart ssh            # Appliquer les changements
systemctl status ssh             # Vérifier
```

> ⚠️ Après chaque modif sshd_config → **toujours restart ssh + screenshot**

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
0 2 * * *     /usr/bin/apt-get upgrade -y    # Tous les jours à 2h
0 0 1 * *     /scripts/backup.sh             # 1er du mois à minuit
*/5 * * * *   /scripts/check.sh              # Toutes les 5 min
0 8-18 * * 1-5 /scripts/task.sh             # 8h-18h, lun-ven
```

---

## 📊 Vérifier Processus / RAM / Disque

```bash
# Processus
top                     # Vue temps réel (q pour quitter)
ps aux                  # Tous les processus
ps aux | grep apache2   # Chercher un process spécifique
kill -9 PID             # Tuer un process

# RAM
free -h                 # Mémoire libre/utilisée (human readable)

# Disque
df -h                   # Espace disque par partition
du -sh /var/log         # Taille d'un dossier

# Vue combinée rapide
echo "=RAM="; free -h; echo "=DISQUE="; df -h
```

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
tar -xzf backup.tar.gz                      # Décompresser
rsync -av /source/ /destination/            # Synchro intelligente
```

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

# PHP
apt install php php-mysql php-curl php-gd php-mbstring php-xml php-zip -y

# Vérifier
curl localhost            # Apache répond ?
systemctl status mariadb  # MariaDB OK ?
```

---

## 🔗 Connexions inter-thèmes

> **chmod ↔ NTFS** → même besoin, philosophies différentes. Linux = octal, Windows = ACL graphique
> **sshd_config ↔ ANSSI** → chaque directive a une raison sécuritaire (pas de root, port non-standard)
> **crontab ↔ Tâches planifiées Windows** → même concept, syntaxe opposée
