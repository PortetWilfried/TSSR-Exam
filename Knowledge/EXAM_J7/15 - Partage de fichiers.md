# 📁 Partage de fichiers — Windows + Linux

> **Concept clé :** Mettre à disposition un dossier sur le réseau pour que d'autres machines puissent y accéder.
> Côté Windows = **SMB/CIFS** (port 445).
> Côté Linux = **Samba** (interop avec Windows) ou **NFS** (Linux-Linux).

---

## 📑 Sommaire

- [[#🪟 Windows — Partage SMB|🪟 Windows — Partage SMB]]
- [[#🐧 Linux — Samba (partage avec accès Windows)|🐧 Linux — Samba (partage avec accès Windows)]]
- [[#🐧 Linux — NFS (partage entre Linux uniquement)|🐧 Linux — NFS (partage entre Linux uniquement)]]
- [[#🆚 SMB vs NFS — Quel choix ?|🆚 SMB vs NFS — Quel choix ?]]
- [[#🔒 Droits — rappel|🔒 Droits — rappel]]
- [[#🩺 Diagnostic partage — Phase 2|🩺 Diagnostic partage — Phase 2]]
- [[#🔗 Connexions inter-thèmes|🔗 Connexions inter-thèmes]]

---

## 🪟 Windows — Partage SMB

### Créer un dossier partagé (GUI)

1. Créer le dossier : `C:\Partages\Compta`
2. Clic droit → **Propriétés** → onglet **Partage**
3. **Partage avancé** → cocher "Partager ce dossier"
4. **Autorisations** → définir les permissions au niveau partage
5. Onglet **Sécurité** → définir les permissions NTFS

> ⚠️ **Deux couches de permissions** :
> - **Partage** = niveau réseau (qui peut voir le partage)
> - **NTFS** = niveau fichier (qui peut faire quoi sur les fichiers)
> La permission **la plus restrictive** des deux s'applique.

### Créer un partage en PowerShell

```powershell
# Créer le dossier
New-Item -ItemType Directory -Path "C:\Partages\Compta"

# Créer le partage SMB
New-SmbShare `
  -Name "Compta" `
  -Path "C:\Partages\Compta" `
  -FullAccess "Administrateurs" `
  -ChangeAccess "GG_Compta" `
  -ReadAccess "GG_Lecture"

# Vérifier
Get-SmbShare
Get-SmbShareAccess -Name "Compta"
```

### Définir les droits NTFS en PowerShell

```powershell
$acl = Get-Acl "C:\Partages\Compta"

# Donner Modify au groupe Compta
$rule = New-Object System.Security.AccessControl.FileSystemAccessRule(
    "TVS\GG_Compta", "Modify",
    "ContainerInherit,ObjectInherit", "None", "Allow")
$acl.SetAccessRule($rule)

Set-Acl "C:\Partages\Compta" $acl

# Vérifier
Get-Acl "C:\Partages\Compta" | Format-List
```

### Bonnes pratiques NTFS (méthode AGDLP)

```
A — Account (utilisateur)
G — Global Group (groupe global)
DL — Domain Local group (groupe local de domaine)
P — Permission (NTFS)

Logique : on met les USERS dans des GG, on met les GG dans des DL, et on attribue les permissions aux DL.
```

> ⚠️ **Jamais de droits directement sur un utilisateur** → toujours via un groupe.

### Accéder à un partage depuis un client Windows

```cmd
\\SRV-AD-01\Compta              # Dans l'explorateur
net use M: \\SRV-AD-01\Compta   # Mapper le lecteur
net use M: /delete              # Démapper
net view \\SRV-AD-01            # Lister les partages d'un serveur
```

### Diagnostic SMB

```powershell
Get-SmbShare                    # Liste tous les partages locaux
Get-SmbSession                  # Voir qui est connecté
Get-SmbConnection               # Voir les connexions actives
Test-NetConnection SRV-AD-01 -Port 445   # Le port SMB est-il joignable ?
```

---

## 🐧 Linux — Samba (partage avec accès Windows)

### Concept

> **Samba** = implémentation Linux du protocole SMB/CIFS. Permet à un serveur Linux de partager des dossiers que des **clients Windows** peuvent voir comme un partage Windows natif.

### 1. Installation

```bash
apt update
apt install samba -y
```

### 2. Créer le dossier à partager

```bash
mkdir -p /srv/partages/compta
chown -R nobody:nogroup /srv/partages/compta
chmod -R 0775 /srv/partages/compta
```

### 3. Sauvegarder et configurer Samba

```bash
cp /etc/samba/smb.conf /etc/samba/smb.conf.bak
nano /etc/samba/smb.conf
```

À la fin du fichier, ajouter :

```ini
[compta]
   comment = Partage Compta
   path = /srv/partages/compta
   browseable = yes
   read only = no
   writable = yes
   guest ok = no
   valid users = martin, alice, @grp_compta
   create mask = 0660
   directory mask = 0770
```

### 4. Créer les users Samba

> Un user Samba doit aussi exister en tant qu'utilisateur Linux.

```bash
adduser martin                  # User Linux
smbpasswd -a martin             # Créer le mot de passe Samba
smbpasswd -e martin             # Activer le compte Samba
```

### 5. Tester la config et redémarrer

```bash
testparm                        # ⚠️ Toujours tester avant de restart
systemctl restart smbd nmbd
systemctl status smbd
systemctl enable smbd nmbd
```

### 6. Vérifier côté serveur

```bash
smbclient -L //localhost -U martin     # Lister les partages
pdbedit -L                              # Lister les users Samba
```

### 7. Accéder au partage depuis Windows

Dans l'explorateur :
```
\\IP_DU_SERVEUR_LINUX\compta
```

Login : `martin` / mot de passe Samba défini.

### 8. Accéder depuis un autre Linux

```bash
# Lister
smbclient -L //192.168.1.20 -U martin

# Monter le partage
apt install cifs-utils -y
mkdir /mnt/compta
mount -t cifs //192.168.1.20/compta /mnt/compta -o username=martin
```

### Diagnostic Samba

```bash
testparm                         # Vérifier la syntaxe smb.conf
smbstatus                        # Voir les connexions actives
systemctl status smbd
journalctl -u smbd -n 50
ss -tlnp | grep -E ':445|:139'
tail -f /var/log/samba/log.smbd
```

---

## 🐧 Linux — NFS (partage entre Linux uniquement)

### Concept

> **NFS** = Network File System. Protocole Unix natif pour partager des dossiers entre **machines Linux/Unix**.
> Plus simple que Samba, mais **pas compatible Windows nativement**.

### 1. Côté serveur NFS

```bash
apt install nfs-kernel-server -y

# Créer le dossier
mkdir -p /srv/nfs/data
chown nobody:nogroup /srv/nfs/data
chmod 755 /srv/nfs/data

# Sauvegarder le fichier d'export
cp /etc/exports /etc/exports.bak

# Définir le partage
nano /etc/exports
```

```bash
# /etc/exports
/srv/nfs/data    192.168.1.0/24(rw,sync,no_subtree_check,no_root_squash)
```

```bash
# Appliquer les exports
exportfs -ra

# Démarrer
systemctl restart nfs-kernel-server
systemctl enable nfs-kernel-server

# Vérifier
exportfs -v
showmount -e localhost
```

### 2. Côté client NFS

```bash
apt install nfs-common -y

mkdir /mnt/data
mount -t nfs 192.168.1.20:/srv/nfs/data /mnt/data

# Vérifier
df -h | grep nfs
ls /mnt/data
```

### Montage permanent au boot — `/etc/fstab`

```bash
# Sauvegarder avant !
cp /etc/fstab /etc/fstab.bak

nano /etc/fstab
```

Ajouter :
```
192.168.1.20:/srv/nfs/data   /mnt/data   nfs   defaults   0   0
```

Tester sans rebooter :
```bash
mount -a
```

---

## 🆚 SMB vs NFS — Quel choix ?

| | SMB / Samba | NFS |
|--|-------------|-----|
| **Compatibilité** | Windows + Linux + Mac | Linux + Unix |
| **Port** | 445 (139 legacy) | 2049 |
| **Authentification** | Par user/mdp | Par IP (souvent) |
| **Performance LAN** | Bonne | Excellente |
| **Usage typique** | Partage hétérogène (Win + Linux) | Cluster Linux, NAS Unix |

> **À l'exam :** si on te demande de partager un dossier Linux pour un poste Windows → **Samba**.
> Si c'est Linux vers Linux → **NFS** (plus simple).

---

## 🔒 Droits — rappel

### Windows : NTFS

| Permission | Effet |
|-----------|-------|
| Lecture | Voir et ouvrir |
| Écriture | Créer, modifier |
| Modification | Lecture + Écriture + Suppression |
| Contrôle total | Tout + modifier les permissions |

### Linux : chmod

```bash
chmod 770 /srv/partages/compta     # proprio+groupe = rwx, autres = rien
chmod 755 /srv/partages/public     # tout le monde peut lire, seul le proprio peut écrire
setfacl -m g:grp_compta:rwx /srv/partages/compta   # Droits fins pour un groupe
```

---

## 🩺 Diagnostic partage — Phase 2

### Le client ne voit pas le partage

```
1. Le serveur de partage est-il joignable ?
   ping IP_serveur

2. Le port SMB est-il ouvert ?
   Test-NetConnection serveur -Port 445   (Windows)
   ss -tlnp | grep 445                     (Linux)
   nmap -p 445 serveur                     (depuis client)

3. Le service est-il actif ?
   Get-SmbShare                            (Windows)
   systemctl status smbd                   (Linux/Samba)

4. Pare-feu ?
   ufw allow Samba                         (Linux)
   netsh advfirewall ...                   (Windows)

5. Les credentials sont-ils corrects ?
   - User existe-t-il ?
   - Mot de passe Samba défini (smbpasswd -a) ?
   - Le user est-il dans `valid users` du smb.conf ?
```

### Le client voit mais ne peut pas écrire

→ Combinaison **NTFS + Partage** (Windows) ou **chmod + valid users** (Samba).
→ La permission la plus restrictive gagne.

---

## 🔗 Connexions inter-thèmes

> **Partage Windows ↔ Mappage lecteur M:** → le mappage GPO/PS pointe vers un partage SMB
> **Samba ↔ AD** → Samba peut s'intégrer à AD (`net ads join`) pour authentifier les users via AD
> **NTFS ↔ chmod/setfacl** → même besoin (qui peut faire quoi), philosophies différentes
> **Partage ↔ Sauvegardes** → un partage commun = un point unique de défaillance, à sauvegarder en priorité
> **SMB port 445 ↔ Sécurité** → un port 445 exposé à Internet = risque énorme (rappel WannaCry)
