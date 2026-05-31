# ☁️ Virtualisation

## 🖥️ Hyperviseur Type 1 vs Type 2

| | Type 1 (Bare Metal) | Type 2 (Hosted) |
|--|---------------------|-----------------|
| **Fonctionne sur** | Directement sur le hardware | Sur un OS existant |
| **Performance** | ⚡ Élevée | 🐢 Moins élevée |
| **Exemples** | VMware ESXi, Hyper-V, Proxmox | VMware Workstation, VirtualBox |
| **Usage** | Production, datacenter | Dev, tests, formation |

> **Exam anglais :** "A Type 1 hypervisor runs directly on the hardware. A Type 2 hypervisor runs on top of an existing operating system."

---

## 📸 Snapshots VMware

- **Snapshot** = photo instantanée de l'état de la VM (disque + RAM + config)
- Permet de **revenir en arrière** si une manip échoue
- **Bonne pratique exam :** prendre un snapshot avant chaque manipulation risquée

**Dans VMware Workstation :**
`VM > Prendre un snapshot` | `VM > Revenir au snapshot`

> ⚠️ Les snapshots consomment de l'espace disque — ne pas en accumuler

---

## ☁️ IaaS / PaaS / SaaS

| Modèle | Acronyme | Ce que le cloud fournit | Exemple |
|--------|----------|------------------------|---------|
| **IaaS** | Infrastructure as a Service | Serveurs, stockage, réseau | AWS EC2, Azure VM |
| **PaaS** | Platform as a Service | IaaS + OS + Runtime | Azure App Service, Heroku |
| **SaaS** | Software as a Service | Tout (app prête à l'emploi) | Office 365, Gmail, Salesforce |

**Mémo :** Plus on monte (IaaS→PaaS→SaaS), moins on gère, plus le fournisseur gère.

---

## 🔄 Redondance & Haute Disponibilité

| Concept | Définition |
|---------|-----------|
| **Clustering** | Groupe de serveurs qui travaillent ensemble — si l'un tombe, les autres prennent le relais |
| **Load Balancing** | Répartition de charge entre plusieurs serveurs |
| **Failover** | Basculement automatique vers un serveur de secours |
| **HA (High Availability)** | Architecture conçue pour minimiser les interruptions |
| **RAID** | Redondance au niveau des disques |

---

## 🔗 Connexions inter-thèmes

> **VMware Workstation** (l'outil de l'exam) = hyperviseur Type 2
> **ESXi** = ce qu'on utilise en prod = Type 1
> **Clustering ↔ Sauvegardes** → clustering = disponibilité ; sauvegardes = récupération après sinistre. Les deux sont complémentaires.
