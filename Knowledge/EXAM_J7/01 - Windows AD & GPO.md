# 🖥️ Windows AD & GPO

## 📑 Sommaire

- [[#⚡ Commandes snap (mémoriser)|⚡ Commandes snap (mémoriser)]]
- [[#👤 Créer un utilisateur AD|👤 Créer un utilisateur AD]]
- [[#🏗️ Créer une OU|🏗️ Créer une OU]]
- [[#📁 Partage réseau + Permissions|📁 Partage réseau + Permissions]]
- [[#🗺️ Mappage lecteur réseau (M:)|🗺️ Mappage lecteur réseau (M:)]]
- [[#⏰ Plage horaire de connexion|⏰ Plage horaire de connexion]]
- [[#🛡️ GPO — Les plus demandées à l'exam|🛡️ GPO — Les plus demandées à l'exam]]
- [[#📋 Logs Windows — Observateur d'événements|📋 Logs Windows — Observateur d'événements]]
- [[#💾 Backup des logs — Script + Tâche planifiée|💾 Backup des logs — Script + Tâche planifiée]]
- [[#🔄 Automatiser une mise à jour à des heures précises|🔄 Automatiser une mise à jour à des heures précises]]
- [[#🔗 Connexions inter-thèmes|🔗 Connexions inter-thèmes]]

---

## ⚡ Commandes snap (mémoriser)

| Outil | Commande |
|-------|----------|
| Utilisateurs AD | `dsa.msc` |
| GPO | `gpmc.msc` |
| DNS | `dnsmgmt.msc` |
| DHCP | `dhcpmgmt.msc` |
| Observateur d'événements | `eventvwr.msc` |
| Planificateur de tâches | `taskschd.msc` |
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

## 📋 Logs Windows — Observateur d'événements

> L'outil principal pour consulter les logs Windows : `eventvwr.msc`
> **Ne pas chercher les logs dans l'explorateur de fichiers** — passer par l'Observateur d'événements.

### Les 3 journaux principaux

| Journal | Rôle |
|---------|------|
| **Application** | Logs des logiciels et services |
| **Système** | Logs Windows, pilotes, services, démarrage, erreurs système |
| **Sécurité** | Connexions, échecs de connexion, audit, actions sensibles |

---

## 💾 Backup des logs — Script + Tâche planifiée

### Script PowerShell (`C:\Scripts\BackupLogs.ps1`)

```powershell
$Date = Get-Date -Format "yyyy-MM-dd_HH-mm"
$BackupPath = "C:\BackupLogs\$Date"
New-Item -ItemType Directory -Path $BackupPath -Force

wevtutil epl Application "$BackupPath\Application.evtx"
wevtutil epl System      "$BackupPath\System.evtx"
wevtutil epl Security    "$BackupPath\Security.evtx"

Write-Host "Logs sauvegardés dans : $BackupPath"
```

> `wevtutil epl` = **e**xport **l**og — exporte un journal au format `.evtx`

### Automatiser avec le Planificateur de tâches

```powershell
$action  = New-ScheduledTaskAction -Execute "powershell.exe" `
           -Argument "-ExecutionPolicy Bypass -File C:\Scripts\BackupLogs.ps1"
$trigger = New-ScheduledTaskTrigger -Daily -At "02:00"
Register-ScheduledTask -TaskName "BackupLogs" -Action $action -Trigger $trigger -RunLevel Highest
```

### Prouver que ça fonctionne — 3 screenshots exam

1. **Avant** : `eventvwr.msc` ouvert, journaux visibles
2. **Pendant** : exécution du script PowerShell
3. **Après** : dossier `C:\BackupLogs\` avec les fichiers `.evtx` + ouverture d'un `.evtx` dans l'Observateur d'événements

### Phrase jury
> "J'ai sauvegardé les journaux Application, Système et Sécurité au format `.evtx` avec PowerShell. Les fichiers sont horodatés dans un dossier de sauvegarde. Pour vérifier, j'ouvre le fichier `.evtx` exporté dans l'Observateur d'événements et je contrôle que les événements sont lisibles."

---

## 🔄 Automatiser une mise à jour à des heures précises

> Analogie : c'est comme programmer une machine à café pour 7h00.
> La machine = Windows | la recette = le script PS | l'heure = la tâche planifiée.

### Script PowerShell (`C:\Scripts\MajWindows.ps1`)

```powershell
$Date = Get-Date -Format "yyyy-MM-dd_HH-mm"
$LogPath = "C:\Logs\Maj_$Date.txt"
New-Item -ItemType Directory -Path "C:\Logs" -Force

"Début vérification MAJ : $(Get-Date)" | Out-File $LogPath -Append

# Installer le module si pas déjà présent
if (-not (Get-Module -ListAvailable -Name PSWindowsUpdate)) {
    Install-Module PSWindowsUpdate -Force -Scope CurrentUser
}
Import-Module PSWindowsUpdate

# Lister les mises à jour disponibles
Get-WindowsUpdate | Out-File $LogPath -Append

"Fin de vérification : $(Get-Date)" | Out-File $LogPath -Append
Write-Host "Log disponible : $LogPath"
```

> ⚠️ Éviter `-AutoReboot` à l'exam — ça peut redémarrer la VM au mauvais moment.

### Créer la tâche planifiée via GUI (`taskschd.msc`)

| Onglet | Ce qu'il faut mettre |
|--------|---------------------|
| Général | Nom : `MAJ Windows automatique` |
| Déclencheurs | Tous les jours à 18h00 (ou selon consigne) |
| Actions | Lancer un programme |
| Programme | `powershell.exe` |
| Arguments | `-ExecutionPolicy Bypass -File "C:\Scripts\MajWindows.ps1"` |
| Conditions | Décocher "démarrer uniquement si sur secteur" si besoin |
| Paramètres | Autoriser l'exécution à la demande |

### Créer la tâche via PowerShell

```powershell
$action  = New-ScheduledTaskAction -Execute "powershell.exe" `
           -Argument "-ExecutionPolicy Bypass -File C:\Scripts\MajWindows.ps1"
$trigger = New-ScheduledTaskTrigger -Daily -At "18:00"
Register-ScheduledTask -TaskName "MAJ Windows automatique" -Action $action -Trigger $trigger -RunLevel Highest
```

### Prouver que ça fonctionne — screenshots exam

1. Le script `C:\Scripts\MajWindows.ps1` ouvert
2. La tâche planifiée visible dans `taskschd.msc` avec son horaire
3. L'action qui lance `powershell.exe`
4. Le fichier log horodaté dans `C:\Logs\`

### Phrase jury
> "J'ai créé un script PowerShell qui vérifie les mises à jour, puis j'ai planifié son exécution avec le Planificateur de tâches à l'heure demandée. La preuve d'exécution est disponible dans un fichier de log horodaté."

---

## 🔗 Connexions inter-thèmes

> **GPO sans OU bien structurée** = GPO appliquée à tout le monde → chaos
> **AD dépend du DNS** → DNS cassé = authentification impossible (Kerberos port 88, LDAP port 389)
> **Mappage lecteur** → dépend du partage SMB ET des droits NTFS
> **Backup logs + MAJ automatique** = même mécanisme : script PS + Planificateur = `crontab` Windows
