# ⚡ PowerShell — Scripts Exam

## 📑 Sommaire

- [[#👤 Créer un utilisateur AD|👤 Créer un utilisateur AD]]
- [[#📋 Import CSV → AD|📋 Import CSV → AD]]
- [[#👥 Groupes|👥 Groupes]]
- [[#🗺️ Mappage lecteur|🗺️ Mappage lecteur]]
- [[#⏱️ Tâche planifiée (automatiser mise à jour / backup)|⏱️ Tâche planifiée]]
- [[#💾 Backup logs (tâche planifiée)|💾 Backup logs (tâche planifiée)]]
- [[#🔍 Commenter / Analyser un script PS (méthode exam)|🔍 Commenter / Analyser un script PS]]
- [[#🎯 Script type 1 — Ajouter un utilisateur à une OU|🎯 Script type 1 — User vers OU]]
- [[#🎯 Script type 2 — Déplacer un utilisateur vers une autre OU|🎯 Script type 2 — Déplacer user]]
- [[#🎯 Script type 3 — Créer plusieurs users depuis un CSV vers une OU|🎯 Script type 3 — CSV vers OU]]
- [[#🎯 Script type 4 — Ajouter un user à un groupe|🎯 Script type 4 — User vers groupe]]
- [[#🎯 Script type 5 — Désactiver un utilisateur|🎯 Script type 5 — Désactiver user]]
- [[#🎯 Script type 6 — Script à commenter (espace disque, type questionnaire Studi)|🎯 Script type 6 — Espace disque]]
- [[#🧠 Méthode pour commenter un script inconnu (à dire au jury)|🧠 Méthode commenter script inconnu]]
- [[#🔗 Connexions inter-thèmes|🔗 Connexions inter-thèmes]]

---

## 👤 Créer un utilisateur AD

```powershell
New-ADUser `
  -Name "Jean Dupont" `
  -GivenName "Jean" `
  -Surname "Dupont" `
  -SamAccountName "j.dupont" `
  -UserPrincipalName "j.dupont@tvs.lan" `
  -Path "OU=RH,DC=tvs,DC=lan" `
  -AccountPassword (ConvertTo-SecureString "Azerty123!" -AsPlainText -Force) `
  -Enabled $true
```

---

## 📋 Import CSV → AD

**Fichier CSV (users.csv) :**
```csv
Prenom,Nom,Login,OU
Jean,Dupont,j.dupont,RH
Marie,Martin,m.martin,Compta
```

**Script import :**
```powershell
Import-Csv "C:\users.csv" | ForEach-Object {
    New-ADUser `
      -Name "$($_.Prenom) $($_.Nom)" `
      -SamAccountName $_.Login `
      -Path "OU=$($_.OU),DC=tvs,DC=lan" `
      -AccountPassword (ConvertTo-SecureString "Azerty123!" -AsPlainText -Force) `
      -Enabled $true
    Write-Host "Créé : $($_.Login)"
}
```

---

## 👥 Groupes

```powershell
# Créer un groupe
New-ADGroup -Name "GG_RH" -GroupScope Global -Path "OU=Groupes,DC=tvs,DC=lan"

# Ajouter un membre
Add-ADGroupMember -Identity "GG_RH" -Members "j.dupont"

# Voir les membres
Get-ADGroupMember -Identity "GG_RH"

# Désactiver un user
Disable-ADAccount -Identity "j.dupont"
```

---

## 🗺️ Mappage lecteur

```powershell
# Mapper le lecteur M: (persistant)
New-PSDrive -Name "M" -PSProvider FileSystem -Root "\\SRV-AD-01\RH" -Persist

# Déconnecter
Remove-PSDrive -Name "M"
```

---

## ⏱️ Tâche planifiée (automatiser mise à jour / backup)

```powershell
# Créer une tâche qui s'exécute tous les jours à 2h
$action = New-ScheduledTaskAction -Execute "PowerShell.exe" -Argument "-File C:\Scripts\backup.ps1"
$trigger = New-ScheduledTaskTrigger -Daily -At "02:00"
Register-ScheduledTask -TaskName "BackupQuotidien" -Action $action -Trigger $trigger -RunLevel Highest

# Voir les tâches planifiées
Get-ScheduledTask

# Supprimer une tâche
Unregister-ScheduledTask -TaskName "BackupQuotidien" -Confirm:$false
```

---

## 💾 Backup logs (tâche planifiée)

```powershell
# Script : C:\Scripts\backup-logs.ps1
$date = Get-Date -Format "yyyyMMdd"
$dest = "C:\Backup\Logs"
New-Item -ItemType Directory -Force -Path $dest

foreach ($log in @("Application","System","Security")) {
    wevtutil epl $log "$dest\$log`_$date.evtx"
    Write-Host "Sauvegardé : $log"
}
```

---

## 🔍 Commenter / Analyser un script PS (méthode exam)

Quand on te donne un script à commenter, identifie ces éléments :

| Élément | Signification |
|---------|--------------|
| `$var = ...` | Variable qui stocke une valeur |
| `Get-WmiObject` / `Get-CimInstance` | Récupère des infos système |
| `Get-ADUser` / `Get-ADGroup` | Récupère des objets AD |
| `New-ADUser` / `Add-ADGroupMember` | Crée ou modifie des objets AD |
| `foreach ($x in $coll)` | Boucle sur chaque élément |
| `if (condition) { ... }` | Test conditionnel |
| `Import-Csv "fichier"` | Lit un fichier CSV ligne par ligne |
| `$_.NomColonne` | Accède à une colonne du CSV dans une boucle |
| `Write-Host "..."` | Affiche à l'écran |
| `Out-File $path -Append` | Écrit dans un fichier (ajoute à la suite) |
| `-Filter "..."` | Filtre les résultats |
| `\| ` (pipe) | Passe le résultat à la commande suivante |
| Backtick `` ` `` en fin de ligne | Continue la commande sur la ligne suivante |

---

## 🎯 Script type 1 — Ajouter un utilisateur à une OU

```powershell
# Crée un utilisateur "dupont" dans l'OU "RH" du domaine tvs.lan
New-ADUser `
  -Name "Dupont Jean" `
  -SamAccountName "dupont" `
  -UserPrincipalName "dupont@tvs.lan" `
  -Path "OU=RH,DC=tvs,DC=lan" `
  -AccountPassword (ConvertTo-SecureString "Azerty123!" -AsPlainText -Force) `
  -Enabled $true
```

**Explication ligne par ligne (à dire au jury) :**

> "Ce script utilise la commande `New-ADUser` pour créer un utilisateur dans l'Active Directory.
> Le paramètre `-Name` définit le nom affiché, `-SamAccountName` le nom de connexion.
> `-Path` indique où placer l'utilisateur — ici dans l'OU `RH` du domaine `tvs.lan`.
> `-AccountPassword` reçoit un mot de passe converti en chaîne sécurisée avec `ConvertTo-SecureString`.
> `-Enabled $true` active le compte immédiatement."

---

## 🎯 Script type 2 — Déplacer un utilisateur vers une autre OU

```powershell
# Déplace l'utilisateur dupont vers l'OU Compta
Get-ADUser dupont | Move-ADObject -TargetPath "OU=Compta,DC=tvs,DC=lan"
```

**Explication :**
> "`Get-ADUser dupont` récupère l'objet utilisateur. Le pipe `|` envoie cet objet à `Move-ADObject` qui le déplace vers le chemin cible `OU=Compta`."

---

## 🎯 Script type 3 — Créer plusieurs users depuis un CSV vers une OU

```powershell
# Importe des users depuis un CSV et les crée dans une OU
Import-Csv "C:\users.csv" | ForEach-Object {
    New-ADUser `
      -Name "$($_.Prenom) $($_.Nom)" `
      -SamAccountName $_.Login `
      -Path "OU=$($_.OU),DC=tvs,DC=lan" `
      -AccountPassword (ConvertTo-SecureString "Azerty123!" -AsPlainText -Force) `
      -Enabled $true
    Write-Host "Créé : $($_.Login)"
}
```

**Explication :**
> "`Import-Csv` lit le fichier CSV ligne par ligne. Chaque ligne devient un objet avec ses colonnes.
> `ForEach-Object` boucle sur chaque ligne. À l'intérieur, `$_` représente la ligne en cours.
> `$_.Prenom` accède à la colonne Prenom, `$_.OU` à la colonne OU, etc.
> Pour chaque ligne, on crée un nouvel utilisateur dans l'OU correspondante."

---

## 🎯 Script type 4 — Ajouter un user à un groupe

```powershell
# Ajoute dupont au groupe GG_RH
Add-ADGroupMember -Identity "GG_RH" -Members "dupont"
```

**Variante avec plusieurs utilisateurs :**
```powershell
# Ajoute plusieurs users au groupe
Add-ADGroupMember -Identity "GG_RH" -Members "dupont","martin","durand"
```

**Variante depuis un CSV :**
```powershell
Import-Csv "C:\membres.csv" | ForEach-Object {
    Add-ADGroupMember -Identity $_.Groupe -Members $_.Login
    Write-Host "$($_.Login) ajouté à $($_.Groupe)"
}
```

---

## 🎯 Script type 5 — Désactiver un utilisateur

```powershell
# Désactive un compte (sans le supprimer)
Disable-ADAccount -Identity "durand"

# Vérifier le statut
Get-ADUser durand -Properties Enabled | Select-Object Name, Enabled
```

---

## 🎯 Script type 6 — Script à commenter (espace disque, type questionnaire Studi)

```powershell
# Récupère les infos des disques locaux (DriveType=3 = disque fixe)
$diskInfo = Get-WmiObject Win32_LogicalDisk -Filter "DriveType=3" | `
            Select-Object DeviceID, FreeSpace, Size

# Pour chaque disque trouvé
foreach ($disk in $diskInfo) {
    $driveLetter = $disk.DeviceID
    # Convertit les octets en Go (arrondi 2 décimales)
    $freeSpaceGB  = [math]::Round($disk.FreeSpace / 1GB, 2)
    $totalSpaceGB = [math]::Round($disk.Size / 1GB, 2)
    # Affiche les infos
    Write-Host "Lecteur $driveLetter - Libre: ${freeSpaceGB} Go / Total: ${totalSpaceGB} Go"
}
```

---

## 🧠 Méthode pour commenter un script inconnu (à dire au jury)

```
1. Identifier les variables → "ce script définit des variables : $X, $Y..."
2. Identifier la commande principale → "il utilise Get-XXX qui sert à..."
3. Identifier les boucles/conditions → "puis il parcourt chaque élément..."
4. Identifier la sortie → "et affiche/sauvegarde le résultat dans..."
5. Conclure sur le but → "donc ce script sert à..."
```

> **Phrase universelle si tu sèches :** "Ce script semble parcourir une collection d'éléments avec une boucle `foreach`, applique un traitement à chaque élément, et affiche le résultat. Je vais détailler ligne par ligne…"

---

## 🔗 Connexions inter-thèmes

> **Import-CSV** → même logique que créer des users manuellement, juste automatisé
> **Tâches planifiées PS** = équivalent de `crontab` sous Linux
> **GPO + PS** → les deux servent à appliquer des configs en masse
> **`$_` dans ForEach-Object** = équivalent de la variable de boucle en bash
