# 💾 Sauvegardes

## 📑 Sommaire

- [[#📐 Règle 3-2-1 (à réciter en Phase 2)|📐 Règle 3-2-1 (à réciter en Phase 2)]]
- [[#📊 Types de sauvegardes|📊 Types de sauvegardes]]
- [[#🛠️ Outils|🛠️ Outils]]
- [[#💻 Commandes Windows|💻 Commandes Windows]]
- [[#🐧 Commandes Linux|🐧 Commandes Linux]]
- [[#📋 PRA / PCA|📋 PRA / PCA]]
- [[#🔗 Connexions inter-thèmes|🔗 Connexions inter-thèmes]]

---

## 📐 Règle 3-2-1 (à réciter en Phase 2)

> **3** copies des données
> **2** supports différents (ex : disque local + NAS)
> **1** copie hors site (cloud, autre bâtiment)

---

## 📊 Types de sauvegardes

| Type | Ce qu'elle sauvegarde | Temps backup | Temps restore | Espace |
|------|----------------------|--------------|---------------|--------|
| **Complète** | Tout | Long | Rapide | Beaucoup |
| **Différentielle** | Modifs depuis dernière **complète** | Moyen | Moyen | Moyen |
| **Incrémentielle** | Modifs depuis dernière **sauvegarde** (quelle qu'elle soit) | Rapide | Long | Peu |

> **Mémo différentielle vs incrémentielle :**
> - Différentielle = "tout ce qui a changé depuis le dernier **dimanche**"
> - Incrémentielle = "tout ce qui a changé depuis **hier**"

---

## 🛠️ Outils

| Outil | Type | Usage |
|-------|------|-------|
| **Cobian Reflector** | Windows | Sauvegarde fichiers, planifiable |
| **wbadmin** | Windows | Sauvegarde système/AD (CLI) |
| **Windows Server Backup** | Windows | GUI + System State |
| **tar / rsync** | Linux | Scripts bash |
| **Veeam** | Multi | Backup VMs |
| **FOG** | Open source | Clonage postes réseau |

---

## 💻 Commandes Windows

```powershell
# Sauvegarder le System State AD (NTDS, SYSVOL, DNS, registre)
wbadmin start systemstatebackup -backuptarget:D:

# Lister les sauvegardes disponibles
wbadmin get versions

# Restaurer (en mode DSRM)
wbadmin start systemstaterecovery -version:XX/XX/XXXX-XX:XX
```

> ⚠️ Pour restaurer AD → démarrer en **mode DSRM** (mot de passe noté lors promo DC)

---

## 🐧 Commandes Linux

```bash
# Archive compressée
tar -czf /backup/data_$(date +%F).tar.gz /var/www

# Copie miroir intelligente
rsync -av --delete /var/www/ /backup/www/

# Vérifier qu'une sauvegarde est OK
tar -tzf backup.tar.gz | head
```

---

## 📋 PRA / PCA

| Sigle | Signification | Objectif |
|-------|--------------|----------|
| **PCA** | Plan de Continuité d'Activités | Maintenir l'activité pendant l'incident |
| **PRA** | Plan de Reprise d'Activités | Reprendre l'activité après l'incident |
| **RTO** | Recovery Time Objective | Durée max d'interruption tolérée |
| **RPO** | Recovery Point Objective | Perte de données max tolérée |

---

## 🔗 Connexions inter-thèmes

> **Sauvegarde AD** → inclut NTDS.dit (base AD), SYSVOL (scripts/GPO), DNS
> **Règle 3-2-1 ↔ clustering** → clustering = éviter la panne ; sauvegarde = récupérer après la panne
> **Cobian ↔ wbadmin** → Cobian pour fichiers ; wbadmin pour system state AD
