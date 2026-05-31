# ☁️ Virtualisation

## 📑 Sommaire

- [[#🎯 Pourquoi virtualiser ? (question jury)|🎯 Pourquoi virtualiser ?]]
- [[#🖥️ Hyperviseur Type 1 vs Type 2|🖥️ Hyperviseur Type 1 vs Type 2]]
- [[#🆚 Machine Virtuelle vs Conteneur|🆚 Machine Virtuelle vs Conteneur]]
- [[#🛠️ VMware Workstation — Opérations exam|🛠️ VMware Workstation — Opérations]]
- [[#📸 Snapshots — Détail|📸 Snapshots — Détail]]
- [[#💾 RAID — Redondance disques|💾 RAID — Redondance disques]]
- [[#☁️ IaaS / PaaS / SaaS|☁️ IaaS / PaaS / SaaS]]
- [[#🔄 Redondance & Haute Disponibilité|🔄 Redondance & Haute Disponibilité]]
- [[#🩺 Maintenance d'une VM|🩺 Maintenance d'une VM]]
- [[#🔗 Connexions inter-thèmes|🔗 Connexions inter-thèmes]]

---

> **Concept clé :** Faire fonctionner **plusieurs systèmes d'exploitation** sur une seule machine physique, grâce à un logiciel appelé **hyperviseur** qui crée des **machines virtuelles (VM)**.

> **Analogie :** Un immeuble (machine physique) avec plusieurs appartements (VMs). Chacun a sa cuisine, sa chambre, ses prises électriques (CPU, RAM, disque), mais tous partagent les fondations et l'arrivée d'électricité (le hardware).

---

## 🎯 Pourquoi virtualiser ? (question jury)

| Avantage | Explication |
|----------|------------|
| **Économie matérielle** | 1 serveur physique = 10+ VMs au lieu de 10 machines physiques |
| **Isolation** | Si une VM plante, les autres continuent |
| **Réversibilité** | Snapshots → on revient à un état antérieur |
| **Déploiement rapide** | Cloner une VM = quelques minutes |
| **Migration à chaud** | Déplacer une VM d'un serveur à un autre sans interruption (vMotion) |
| **Tests / Formation** | Casser une VM = pas grave, on la recrée |
| **Optimisation énergétique** | Moins de serveurs physiques = moins de conso/clim |
| **Continuité d'activité** | VM redondées sur 2 sites = HA |

### Phrase jury

> "La virtualisation permet d'exécuter plusieurs systèmes d'exploitation sur une même machine physique. Cela optimise les ressources matérielles, isole les services entre eux, facilite les sauvegardes via les snapshots, et permet une bascule rapide en cas de panne."

---

## 🖥️ Hyperviseur Type 1 vs Type 2

| | Type 1 (Bare Metal) | Type 2 (Hosted) |
|--|---------------------|-----------------|
| **Fonctionne sur** | Directement sur le hardware | Sur un OS existant |
| **Performance** | ⚡ Élevée | 🐢 Moins élevée (overhead OS hôte) |
| **Exemples** | VMware ESXi, Microsoft Hyper-V (rôle), Proxmox VE, Citrix XenServer, KVM | VMware Workstation, Oracle VirtualBox, VMware Player, Parallels |
| **Usage** | Production, datacenter, serveurs | Dev, tests, formation, poste de travail |

> **Anglais exam :** "A Type 1 hypervisor runs directly on the hardware. A Type 2 hypervisor runs on top of an existing operating system."

> **À l'examen TSSR :** la VM tourne sous **VMware Workstation** (Type 2) — c'est l'environnement de la Phase 1.

---

## 🆚 Machine Virtuelle vs Conteneur

| | VM (Virtualisation) | Conteneur (Docker, LXC) |
|--|---------------------|------------------------|
| **Ce qui est virtualisé** | OS complet | Seulement l'application |
| **Taille** | Plusieurs Go | Quelques Mo à centaines de Mo |
| **Démarrage** | Plusieurs minutes | Quelques secondes |
| **Isolation** | Très forte (OS complet) | Plus légère (partage le noyau) |
| **Usage** | Serveur applicatif complet | Microservices, applis cloud-native |

> **À retenir :** une VM **inclut un OS**, un conteneur **utilise l'OS de l'hôte**.

---

## 🛠️ VMware Workstation — Opérations exam

### Créer une VM

`File > New Virtual Machine`
- **Typical** ou **Custom** (Custom = plus de contrôle)
- Choisir l'**ISO** (image d'installation) ou installer plus tard
- **OS Guest** : Windows Server, Debian, etc.
- **Nom et emplacement** de la VM
- **Disque** : 40-60 Go pour un serveur, allocation dynamique conseillée
- **Personnaliser le hardware** : RAM (4-8 Go pour serveur), CPU (2-4 vCPU), réseau

### Modes réseau VMware (à connaître absolument)

| Mode | Comment ça marche | Usage |
|------|------------------|-------|
| **Bridged** | La VM est directement sur le réseau physique, comme une vraie machine | VM accessible depuis tout le LAN |
| **NAT** | La VM passe par l'IP de l'hôte pour sortir, isolée du LAN | Internet OK, pas accessible depuis le LAN |
| **Host-only** | Réseau privé entre l'hôte et les VMs uniquement, pas d'Internet | Labs isolés, tests sécurité |
| **LAN segment / Custom** | Réseau virtuel isolé entre plusieurs VMs | Simuler une infra avec plusieurs VMs |

> **Phase 1 exam :** souvent les VMs sont sur un **LAN segment** ou **host-only** pour qu'elles puissent se voir entre elles (AD, client Win, Linux).

### Paramétrer la VM après création

`VM > Settings` :
- **Memory** : ajuster la RAM
- **Processors** : ajuster les vCPU
- **Hard Disk** : agrandir, ajouter un disque
- **Network Adapter** : changer de mode réseau
- **CD/DVD** : monter une ISO
- **USB Controller** : passer une clé USB physique

### Snapshots — la fonction qui sauve

```
VM > Snapshot > Take Snapshot       → Photo de l'état actuel
VM > Snapshot > Revert              → Revenir à l'état précédent
VM > Snapshot > Snapshot Manager    → Voir l'arborescence des snapshots
```

> **Bonne pratique exam :** snapshot **avant** chaque manipulation risquée (modif sshd, install rôle Windows...).

> ⚠️ Les snapshots **consomment de l'espace disque** et **dégradent les performances** si on en accumule. Supprimer les anciens snapshots quand on est sûr que ça marche.

### Cloner une VM

`VM > Manage > Clone`
- **Linked clone** : rapide, dépend de la VM source (économise l'espace)
- **Full clone** : indépendante, prend tout l'espace

### Exporter / Importer une VM

- **Export OVF** : `File > Export to OVF` → fichier portable `.ovf`/`.ova`
- **Import OVF** : `File > Open` → sélectionner le `.ovf`
- Utile pour partager une VM ou la déplacer entre hyperviseurs.

---

## 📸 Snapshots — Détail

| | Snapshot | Sauvegarde (backup) |
|--|----------|---------------------|
| **Quoi** | État instantané de la VM (disque + RAM + config) | Copie indépendante du disque |
| **Où** | Sur le même stockage que la VM | Sur un stockage différent |
| **Usage** | Revenir avant une manip | Restaurer après sinistre |
| **Bonne pratique** | Court terme (heures/jours) | Long terme (jours/semaines) |

> ⚠️ **Un snapshot n'est PAS une sauvegarde.** Si le disque physique de la VM meurt, le snapshot meurt aussi.

---

## 💾 RAID — Redondance disques

| Niveau | Disques min | Tolérance panne | Performance | Capacité utile |
|--------|-------------|----------------|-------------|----------------|
| **RAID 0** | 2 | ❌ Aucune | ⚡⚡⚡ Très rapide | 100% (= somme) |
| **RAID 1** | 2 | ✅ 1 disque | Lecture rapide | 50% (miroir) |
| **RAID 5** | 3 | ✅ 1 disque | Bonne | (N-1)/N |
| **RAID 6** | 4 | ✅ 2 disques | Correcte | (N-2)/N |
| **RAID 10** | 4 | ✅ 1 par paire | ⚡⚡ Excellente | 50% |

### Quand utiliser quoi

- **RAID 0** = vitesse pure, **JAMAIS en prod** (aucune tolérance)
- **RAID 1** = miroir, simple et fiable pour 2 disques (serveur de base)
- **RAID 5** = bon compromis prix/sécurité pour 3-5 disques
- **RAID 6** = pour gros volumes (> 6 disques), supporte 2 pannes simultanées
- **RAID 10** = performance + fiabilité pour bases de données, virtualisation

### Phrase jury

> "Le RAID est un mécanisme de redondance au niveau des disques. Le RAID 1 fait un miroir entre 2 disques : si l'un meurt, l'autre prend le relais. Le RAID 5 utilise la parité pour tolérer la perte d'un disque sur 3 ou plus. Le RAID 10 combine miroir et stripping pour performance et fiabilité."

> ⚠️ **Le RAID n'est PAS une sauvegarde.** Si tu supprimes un fichier, il est supprimé sur tous les disques du RAID.

---

## ☁️ IaaS / PaaS / SaaS

| Modèle | Acronyme | Le fournisseur gère | Tu gères | Exemple |
|--------|----------|--------------------|---------|---------|
| **IaaS** | Infrastructure as a Service | Réseau, stockage, virtualisation, serveurs | OS, runtime, app, données | AWS EC2, Azure VM, OVH Public Cloud |
| **PaaS** | Platform as a Service | IaaS + OS + runtime + middleware | App + données | Azure App Service, Heroku, App Engine |
| **SaaS** | Software as a Service | Tout | Rien (que l'usage) | Office 365, Gmail, Salesforce, GLPI SaaS |

> **Mémo :** Plus on monte (IaaS → PaaS → SaaS), moins on gère, plus le fournisseur gère.

### Phrase jury

> "L'IaaS fournit l'infrastructure brute — serveurs, stockage, réseau. On installe et gère l'OS et les applications. Le PaaS fournit en plus une plateforme prête (OS, runtime), on ne gère que l'application. Le SaaS, c'est une application prête à l'emploi, on l'utilise sans rien gérer."

---

## 🔄 Redondance & Haute Disponibilité

| Concept | Définition |
|---------|-----------|
| **Clustering** | Groupe de serveurs qui travaillent ensemble — si l'un tombe, les autres prennent le relais |
| **Load Balancing** | Répartition de charge entre plusieurs serveurs |
| **Failover** | Basculement automatique vers un serveur de secours |
| **HA (High Availability)** | Architecture conçue pour minimiser les interruptions |
| **vMotion** (VMware) | Migration d'une VM d'un hôte à un autre **sans interruption** |
| **DRS** (VMware) | Distribution dynamique automatique des VMs sur plusieurs hôtes |
| **RAID** | Redondance au niveau des disques |

> 📎 Voir aussi [[13 - Questions Jury Théorie]] pour Clustering et Redondance en détail.

---

## 🩺 Maintenance d'une VM

### Opérations courantes

```
- Mettre à jour l'OS Guest    → apt upgrade / Windows Update
- Installer les VMware Tools  → meilleure intégration souris/réseau/copier-coller
- Surveiller les ressources   → Task Manager (Win) / top, free, df (Linux)
- Snapshots réguliers          → avant chaque grosse manip
- Sauvegarde du fichier .vmdk → vers un stockage externe
```

### VMware Tools (à connaître)

> Pack à installer **dans la VM** pour améliorer les performances et l'intégration avec l'hôte.
> Bénéfices : meilleure résolution, copier-coller entre hôte et VM, partage de dossiers, synchro heure, drivers optimisés.

`VM > Install VMware Tools` (l'ISO se monte automatiquement dans la VM).

### Diagnostic VM lente

```
1. Vérifier RAM allouée  → augmenter si besoin
2. Vérifier vCPU         → pas trop ni pas assez
3. Vérifier disque hôte  → snapshots à supprimer ?
4. VMware Tools installés ?
5. OS Guest à jour ?
```

---

## 🔗 Connexions inter-thèmes

> **VMware Workstation** (outil exam) = hyperviseur Type 2
> **ESXi** (production) = Type 1
> **Snapshots ↔ Sauvegardes** → snapshots = court terme dans la VM ; sauvegardes = long terme à l'extérieur. **Pas la même chose !**
> **RAID ↔ Sauvegardes** → RAID protège du crash disque, pas de la suppression. Toujours backup en plus.
> **Clustering ↔ vMotion** → vMotion = clustering appliqué aux VMs : on déplace une VM sans interruption
> **VM ↔ Conteneur** → VM = OS complet ; conteneur = juste l'application qui partage le noyau hôte
> **Modes réseau VMware ↔ VLAN** → en prod, les VMs sont reliées à des **vSwitches** qui peuvent porter des VLANs (équivalent virtuel d'un switch physique)
