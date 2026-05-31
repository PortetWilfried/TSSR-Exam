# 🖥️ Windows AD & GPO

## ⚡ Commandes snap (mémoriser)

| Outil | Commande |
|-------|----------|
| Utilisateurs AD | `dsa.msc` |
| GPO | `gpmc.msc` |
| DNS | `dnsmgmt.msc` |
| DHCP | `dhcpmgmt.msc` |
| Forcer GPO | `gpupdate /force` |
| GPO appliquées | `gpresult /r` |

---

## 👤 Créer un utilisateur AD

1. `dsa.msc` → clic droit sur l'OU cible → **Nouvel utilisateur**
2. Remplir : Prénom, Nom, Nom d'ouverture de session
3. Mot de passe complexe (Maj + min + chiffre + symbole)
4. **Screenshot après création**

**Désactiver un user :** clic droit → Désactiver le compte

**Ajouter à un groupe :** clic droit user → Ajouter à un groupe → taper le nom du groupe

---

## 🏗️ Créer une OU

`dsa.msc` → clic droit sur le domaine ou une OU parente → **Nouvelle Unité d'organisation**

---

## 📁 Partage réseau + Permissions

```powershell
# Créer le dossier
New-Item -ItemType Directory -Path "C:\Partages\Compta"

# Partager
New-SmbShare -Name "Compta" -Path "C:\Partages\Compta" -FullAccess "Administrateurs" -ReadAccess "GG_Compta"
```

**Via GUI :** clic droit dossier → Propriétés → Partage → Partage avancé
- Onglet **Partage** : permissions de partage (niveau réseau)
- Onglet **Sécurité** : permissions NTFS (niveau fichier)

> ⚠️ La permission la plus restrictive entre Partage et NTFS s'applique.

---

## 🗺️ Mappage lecteur réseau (M:)

**Via GPO (méthode exam) :**
1. `gpmc.msc` → GPO liée à l'OU → Modifier
2. Config Utilisateur → Préférences → Paramètres Windows → **Mappages de lecteurs**
3. Nouveau → Lecteur → Action : **Créer**
4. Emplacement : `\\SRV-AD-01\Compta` | Lettre : `M`

**Via PowerShell :**
```powershell
New-PSDrive -Name "M" -PSProvider FileSystem -Root "\\SRV-AD-01\Compta" -Persist
```

---

## ⏰ Plage horaire de connexion

```powershell
# Autoriser du lundi au vendredi, 8h-18h
Set-ADUser dupont -Replace @{logonHours = [byte[]](0,0,0,0,0,0,255,3,0,255,3,0,255,3,0,255,3,0,255,3,0,0,0,0,0,0,0)}
```

**Via GUI :** `dsa.msc` → user → Propriétés → onglet **Compte** → **Horaires d'accès**
- Sélectionner les plages → Autoriser / Refuser

---

## 🛡️ GPO — Les plus demandées à l'exam

**Ouvrir :** `gpmc.msc` → clic droit sur l'OU → Créer et lier une GPO

### Interdire CMD
`Config Utilisateur > Modèles d'admin > Système > Empêcher l'accès à l'invite de commandes` → **Activé**

### Fond d'écran imposé
`Config Utilisateur > Modèles d'admin > Bureau > Bureau Active Desktop > Papier peint du bureau Active Desktop` → Activé + chemin du fichier

### Interdire périphériques amovibles (clés USB)
`Config Utilisateur > Modèles d'admin > Système > Accès au stockage amovible > Disques amovibles : Refuser l'accès en lecture` → **Activé**

### Forcer l'application
```cmd
gpupdate /force
```

---

## 💾 Backup des logs

```powershell
# Sauvegarder le journal Application
wevtutil epl Application C:\Backup\Logs\Application_$(Get-Date -Format "yyyyMMdd").evtx

# Sauvegarder tous les journaux principaux
foreach ($log in @("Application","System","Security")) {
    wevtutil epl $log "C:\Backup\Logs\$log`_$(Get-Date -Format 'yyyyMMdd').evtx"
}
```

---

## 🔗 Connexions inter-thèmes

> **GPO sans OU bien structurée** = GPO appliquée à tout le monde → chaos
> **AD dépend du DNS** → DNS cassé = authentification impossible (Kerberos port 88, LDAP port 389)
> **Mappage lecteur** → dépend du partage SMB ET des droits NTFS
