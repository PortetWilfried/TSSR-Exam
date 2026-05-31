# 🔧 Scripting Bash — Exam

## 📑 Sommaire

- [[#📝 Structure d'un script Bash|📝 Structure d'un script Bash]]
- [[#💾 Script sauvegarde (le plus demandé)|💾 Script sauvegarde (le plus demandé)]]
- [[#📊 Script monitoring RAM/Disque|📊 Script monitoring RAM/Disque]]
- [[#👥 Script création users en masse|👥 Script création users en masse]]
- [[#🔒 Script permissions dossier|🔒 Script permissions dossier]]
- [[#📖 Lire / Commenter un script Bash (méthode exam)|📖 Lire / Commenter un script Bash (méthode exam)]]
- [[#⏰ Automatiser avec cron|⏰ Automatiser avec cron]]
- [[#🔗 Connexions inter-thèmes|🔗 Connexions inter-thèmes]]

---

## 📝 Structure d'un script Bash

```bash
#!/bin/bash
# Description du script

# Variables
DATE=$(date +%F)
BACKUP_DIR="/backup"

# Corps du script
echo "Début : $DATE"
```

---

## 💾 Script sauvegarde (le plus demandé)

```bash
#!/bin/bash
# Sauvegarde quotidienne de /var/www

DATE=$(date +%Y%m%d)
SRC="/var/www"
DEST="/backup/www_$DATE.tar.gz"

tar -czf $DEST $SRC

if [ $? -eq 0 ]; then
    echo "Sauvegarde OK : $DEST"
else
    echo "ERREUR sauvegarde" >&2
fi
```

---

## 📊 Script monitoring RAM/Disque

```bash
#!/bin/bash
echo "=== SYSTÈME $(date) ==="
echo "--- RAM ---"
free -h
echo "--- DISQUE ---"
df -h
echo "--- TOP 5 PROCESSUS RAM ---"
ps -eo pid,%mem,cmd --sort=-%mem | head -n 6
```

---

## 👥 Script création users en masse

```bash
#!/bin/bash
# Créer des users depuis un fichier liste.txt (un user par ligne)

while IFS= read -r user; do
    useradd -m -s /bin/bash "$user"
    echo "$user:Azerty123!" | chpasswd
    echo "Créé : $user"
done < liste.txt
```

---

## 🔒 Script permissions dossier

```bash
#!/bin/bash
# Appliquer des droits sur un dossier projet

DOSSIER="/data/projet"
GROUPE="devs"

chown -R :$GROUPE $DOSSIER
chmod -R 770 $DOSSIER
setfacl -R -m g:$GROUPE:rwx $DOSSIER

echo "Droits appliqués sur $DOSSIER pour le groupe $GROUPE"
ls -la $DOSSIER
```

---

## 📖 Lire / Commenter un script Bash (méthode exam)

| Élément | Signification |
|---------|---------------|
| `#!/bin/bash` | Shebang — indique l'interpréteur |
| `$variable` | Référence à une variable |
| `$(commande)` | Substitution de commande |
| `if [ condition ]` | Test conditionnel |
| `$?` | Code retour de la dernière commande (0=succès) |
| `>>` | Redirection en ajout (append) |
| `>` | Redirection (écrase) |
| `\|` | Pipe — chaîne les commandes |
| `&` | Lance en arrière-plan |
| `#` | Commentaire |

**Exemple d'explication orale :**
> "Ce script commence par définir la date du jour. Ensuite il crée une archive compressée de /var/www. Si la commande réussit (code retour 0), il affiche OK, sinon il affiche une erreur."

---

## ⏰ Automatiser avec cron

```bash
crontab -e

# Sauvegarde tous les jours à 3h du matin
0 3 * * * /scripts/backup.sh >> /var/log/backup.log 2>&1

# Monitoring toutes les heures
0 * * * * /scripts/monitoring.sh >> /var/log/monitoring.log
```

---

## 🔗 Connexions inter-thèmes

> **Bash cron** = équivalent PowerShell Scheduled Task
> **`$?`** = tester si une commande a réussi, crucial pour scripts robustes
> **`>> log 2>&1`** = rediriger stdout ET stderr dans un fichier de log
