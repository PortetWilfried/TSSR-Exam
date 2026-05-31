# ⚡ PowerShell — Scripts Exam

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

Quand on te donne un script à commenter :

1. **Ligne `$var = ...`** → c'est une variable qui stocke quelque chose
2. **`Get-WmiObject` / `Get-CimInstance`** → récupère des infos système
3. **`foreach ($x in $collection)`** → boucle sur chaque élément
4. **`Write-Host "..."`** → affiche à l'écran
5. **`-Filter "DriveType=3"`** → filtre les disques locaux (3=fixe)

**Exemple de commentaire attendu :**
```powershell
# Récupère les infos des disques locaux
$diskInfo = Get-WmiObject Win32_LogicalDisk -Filter "DriveType=3"

# Pour chaque disque trouvé
foreach ($disk in $diskInfo) {
    # Convertit les octets en Go (arrondi 2 décimales)
    $freeGB = [math]::Round($disk.FreeSpace / 1GB, 2)
    $totalGB = [math]::Round($disk.Size / 1GB, 2)
    # Affiche le résultat
    Write-Host "Lecteur $($disk.DeviceID) - Libre: $freeGB Go / Total: $totalGB Go"
}
```

---

## 🔗 Connexions inter-thèmes

> **Import-CSV** → même logique que créer des users manuellement, juste automatisé
> **Tâches planifiées PS** = équivalent de `crontab` sous Linux
> **GPO + PS** → les deux servent à appliquer des configs en masse
