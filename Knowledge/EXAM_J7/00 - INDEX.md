# 🎯 TSSR — Filet de Sécurité Exam

> Accès rapide le jour J. Internet autorisé, IA autorisée. **Agis d'abord, consulte ensuite.**

---

## ⚡ Rappels critiques

- **Screenshot après CHAQUE action** — action non prouvée = non évaluée
- **Phase 2 :** constat → hypothèses → tests → résolution → validation responsable
- **Anglais :** au moins 1 question technique en anglais au questionnaire
- **Jury :** ils notent la MÉTHODE, pas seulement le résultat
- **Toujours sauvegarder un fichier de config avant modification** (`.bak`)

---

## 📂 Vue d'ensemble des fiches

| # | Fiche | Compétence |
|---|-------|-----------|
| [[01 - Windows AD & GPO]] | Users, OUs, GPO, Partages, Mappage, Logs, MAJ auto | C2 |
| [[02 - PowerShell]] | New-ADUser, Import-CSV, GPO, Tâches planifiées, scripts | C6 |
| [[03 - Linux Debian]] | Users, chmod, setfacl, SSH, crontab, services, LAMP | C3 |
| [[04 - Réseau IP & VLANs]] | Calculs IP, binaire CIDR, VLANs, dot1q, routage inter-VLAN | C4 |
| [[05 - Scripting Bash]] | Cron, droits, sauvegardes, monitoring | C6 |
| [[06 - Virtualisation]] | VMware, hyperviseurs, snapshots, IaaS/PaaS/SaaS | C5 |
| [[07 - Sauvegardes]] | 3-2-1, Cobian, wbadmin, types de sauvegardes | C8 |
| [[08 - Sécurité & Cyber]] | ANSSI, RGPD, CIA, OSI, ACL Cisco, pare-feu | C7 |
| [[09 - Téléphonie IP]] | FreePBX, SIP, softphone, appel test | C4 |
| [[10 - WDS & Déploiement]] | WDS, PXE, BYOD, FOG | C9 |
| [[11 - Phase 2 Diagnostic]] | Méthode, pannes types, questions jury | C1 |
| [[12 - Support GLPI]] | ITIL, tickets, GTR/GTI, cycle de vie | C1 |
| [[13 - Questions Jury Théorie]] | Redondance, Clustering, Cyber organismes, OSI/TCP-IP | Tous |
| [[14 - DHCP & Configuration IP]] | IP statique Win/Linux, serveur DHCP Win/Linux, relais DHCP | C2/C3/C4 |
| [[15 - Partage de fichiers]] | SMB Windows, Samba Linux, NFS, NTFS vs chmod | C2/C3 |

---

## 📑 Table des matières détaillée

### 🖥️ [[01 - Windows AD & GPO]]

- [[01 - Windows AD & GPO#⚡ Commandes snap (mémoriser)|⚡ Commandes snap (mémoriser)]]
- [[01 - Windows AD & GPO#👤 Créer un utilisateur AD|👤 Créer un utilisateur AD]]
- [[01 - Windows AD & GPO#🏗️ Créer une OU|🏗️ Créer une OU]]
- [[01 - Windows AD & GPO#📁 Partage réseau + Permissions|📁 Partage réseau + Permissions]]
- [[01 - Windows AD & GPO#🗺️ Mappage lecteur réseau (M:)|🗺️ Mappage lecteur réseau (M:)]]
- [[01 - Windows AD & GPO#⏰ Plage horaire de connexion|⏰ Plage horaire de connexion]]
- [[01 - Windows AD & GPO#🛡️ GPO — Les plus demandées à l'exam|🛡️ GPO — Les plus demandées à l'exam]]
- [[01 - Windows AD & GPO#📋 Logs Windows — Observateur d'événements|📋 Logs Windows — Observateur d'événements]]
- [[01 - Windows AD & GPO#💾 Backup des logs — Script + Tâche planifiée|💾 Backup des logs — Script + Tâche planifiée]]
- [[01 - Windows AD & GPO#🔄 Automatiser une mise à jour à des heures précises|🔄 Automatiser une mise à jour à des heures précises]]

---

### ⚡ [[02 - PowerShell]]

- [[02 - PowerShell#👤 Créer un utilisateur AD|👤 Créer un utilisateur AD]]
- [[02 - PowerShell#📋 Import CSV → AD|📋 Import CSV → AD]]
- [[02 - PowerShell#👥 Groupes|👥 Groupes]]
- [[02 - PowerShell#🗺️ Mappage lecteur|🗺️ Mappage lecteur]]
- [[02 - PowerShell#⏱️ Tâche planifiée (automatiser mise à jour / backup)|⏱️ Tâche planifiée]]
- [[02 - PowerShell#💾 Backup logs (tâche planifiée)|💾 Backup logs (tâche planifiée)]]
- [[02 - PowerShell#🔍 Commenter / Analyser un script PS (méthode exam)|🔍 Commenter / Analyser un script PS]]
- [[02 - PowerShell#🎯 Script type 1 — Ajouter un utilisateur à une OU|🎯 Script type 1 — Ajouter un utilisateur à une OU]]
- [[02 - PowerShell#🎯 Script type 2 — Déplacer un utilisateur vers une autre OU|🎯 Script type 2 — Déplacer un utilisateur]]
- [[02 - PowerShell#🎯 Script type 3 — Créer plusieurs users depuis un CSV vers une OU|🎯 Script type 3 — Créer users depuis CSV]]
- [[02 - PowerShell#🎯 Script type 4 — Ajouter un user à un groupe|🎯 Script type 4 — Ajouter un user à un groupe]]
- [[02 - PowerShell#🎯 Script type 5 — Désactiver un utilisateur|🎯 Script type 5 — Désactiver un utilisateur]]
- [[02 - PowerShell#🎯 Script type 6 — Script à commenter (espace disque, type questionnaire Studi)|🎯 Script type 6 — Script à commenter (espace disque)]]
- [[02 - PowerShell#🧠 Méthode pour commenter un script inconnu (à dire au jury)|🧠 Méthode pour commenter un script inconnu]]

---

### 🐧 [[03 - Linux Debian]]

- [[03 - Linux Debian#👤 Utilisateurs & Groupes|👤 Utilisateurs & Groupes]]
- [[03 - Linux Debian#🔒 Droits — chmod & setfacl|🔒 Droits — chmod & setfacl]]
- [[03 - Linux Debian#📁 Créer fichiers / dossiers + droits|📁 Créer fichiers / dossiers + droits]]
- [[03 - Linux Debian#🔐 SSH — sshd_config|🔐 SSH — sshd_config]]
- [[03 - Linux Debian#⏰ Crontab|⏰ Crontab]]
- [[03 - Linux Debian#📊 Vérifier Processus / RAM / Disque|📊 Vérifier Processus / RAM / Disque]]
- [[03 - Linux Debian#🌐 Réseau Linux|🌐 Réseau Linux]]
- [[03 - Linux Debian#🔥 Pare-feu|🔥 Pare-feu]]
- [[03 - Linux Debian#📦 Paquets|📦 Paquets]]
- [[03 - Linux Debian#💾 Sauvegarde / Copie|💾 Sauvegarde / Copie]]
- [[03 - Linux Debian#⚙️ Services (systemctl)|⚙️ Services (systemctl)]]
- [[03 - Linux Debian#🌐 Stack LAMP — Installation rapide|🌐 Stack LAMP — Installation rapide]]
- [[03 - Linux Debian#🔧 Stack LAMP — Troubleshooting|🔧 Stack LAMP — Troubleshooting]]
- [[03 - Linux Debian#🩺 Méthode diagnostic Linux — Bloc complet Phase 2|🩺 Méthode diagnostic Linux — Bloc complet Phase 2]]

---

### 🌐 [[04 - Réseau IP & VLANs]]

- [[04 - Réseau IP & VLANs#🧮 Calcul IP — Méthode express|🧮 Calcul IP — Méthode express]]
- [[04 - Réseau IP & VLANs#🔬 Comprendre le CIDR — La logique binaire|🔬 Comprendre le CIDR — La logique binaire]]
- [[04 - Réseau IP & VLANs#🧮 Exemple exam complet : 50 hôtes sur 192.168.1.0|🧮 Exemple exam complet : 50 hôtes]]
- [[04 - Réseau IP & VLANs#🏷️ VLANs — Commandes Cisco|🏷️ VLANs — Commandes Cisco]]
- [[04 - Réseau IP & VLANs#🔀 Routage inter-VLAN (Router-on-a-stick)|🔀 Routage inter-VLAN (Router-on-a-stick)]]
- [[04 - Réseau IP & VLANs#🔒 Port Security|🔒 Port Security]]
- [[04 - Réseau IP & VLANs#🧪 Diagnostic IP rapide|🧪 Diagnostic IP rapide]]
- [[04 - Réseau IP & VLANs#📡 Protocoles & Ports à connaître|📡 Protocoles & Ports à connaître]]

---

### 🔧 [[05 - Scripting Bash]]

- [[05 - Scripting Bash#📝 Structure d'un script Bash|📝 Structure d'un script Bash]]
- [[05 - Scripting Bash#💾 Script sauvegarde (le plus demandé)|💾 Script sauvegarde (le plus demandé)]]
- [[05 - Scripting Bash#📊 Script monitoring RAM/Disque|📊 Script monitoring RAM/Disque]]
- [[05 - Scripting Bash#👥 Script création users en masse|👥 Script création users en masse]]
- [[05 - Scripting Bash#🔒 Script permissions dossier|🔒 Script permissions dossier]]
- [[05 - Scripting Bash#📖 Lire / Commenter un script Bash (méthode exam)|📖 Lire / Commenter un script Bash (méthode exam)]]
- [[05 - Scripting Bash#⏰ Automatiser avec cron|⏰ Automatiser avec cron]]

---

### ☁️ [[06 - Virtualisation]]

- [[06 - Virtualisation#🎯 Pourquoi virtualiser ? (question jury)|🎯 Pourquoi virtualiser ? (question jury)]]
- [[06 - Virtualisation#🖥️ Hyperviseur Type 1 vs Type 2|🖥️ Hyperviseur Type 1 vs Type 2]]
- [[06 - Virtualisation#🆚 Machine Virtuelle vs Conteneur|🆚 Machine Virtuelle vs Conteneur]]
- [[06 - Virtualisation#🛠️ VMware Workstation — Opérations exam|🛠️ VMware Workstation — Opérations exam]]
- [[06 - Virtualisation#📸 Snapshots — Détail|📸 Snapshots — Détail]]
- [[06 - Virtualisation#💾 RAID — Redondance disques|💾 RAID — Redondance disques]]
- [[06 - Virtualisation#☁️ IaaS / PaaS / SaaS|☁️ IaaS / PaaS / SaaS]]
- [[06 - Virtualisation#🔄 Redondance & Haute Disponibilité|🔄 Redondance & Haute Disponibilité]]
- [[06 - Virtualisation#🩺 Maintenance d'une VM|🩺 Maintenance d'une VM]]

---

### 💾 [[07 - Sauvegardes]]

- [[07 - Sauvegardes#📐 Règle 3-2-1 (à réciter en Phase 2)|📐 Règle 3-2-1 (à réciter en Phase 2)]]
- [[07 - Sauvegardes#📊 Types de sauvegardes|📊 Types de sauvegardes]]
- [[07 - Sauvegardes#🛠️ Outils|🛠️ Outils]]
- [[07 - Sauvegardes#💻 Commandes Windows|💻 Commandes Windows]]
- [[07 - Sauvegardes#🐧 Commandes Linux|🐧 Commandes Linux]]
- [[07 - Sauvegardes#📋 PRA / PCA|📋 PRA / PCA]]

---

### 🛡️ [[08 - Sécurité & Cyber]]

- [[08 - Sécurité & Cyber#🏛️ Organismes à connaître (Phase 2 garanti)|🏛️ Organismes à connaître (Phase 2 garanti)]]
- [[08 - Sécurité & Cyber#🔺 Triade CIA (fondamental)|🔺 Triade CIA (fondamental)]]
- [[08 - Sécurité & Cyber#📋 Modèle OSI — 7 couches|📋 Modèle OSI — 7 couches]]
- [[08 - Sécurité & Cyber#🔥 Pare-feu — Lire des règles|🔥 Pare-feu — Lire des règles]]
- [[08 - Sécurité & Cyber#🔐 Sécurité SSH (ANSSI)|🔐 Sécurité SSH (ANSSI)]]
- [[08 - Sécurité & Cyber#🌐 802.1X|🌐 802.1X]]
- [[08 - Sécurité & Cyber#🔑 RGPD — Points clés|🔑 RGPD — Points clés]]

---

### 📞 [[09 - Téléphonie IP]]

- [[09 - Téléphonie IP#🧠 Concepts clés|🧠 Concepts clés]]
- [[09 - Téléphonie IP#🛠️ Créer un utilisateur FreePBX|🛠️ Créer un utilisateur FreePBX]]
- [[09 - Téléphonie IP#📱 Configurer un Softphone (Zoiper / Linphone / MicroSIP)|📱 Configurer un Softphone]]
- [[09 - Téléphonie IP#📞 Test d'appel|📞 Test d'appel]]
- [[09 - Téléphonie IP#🔧 Dépannage FreePBX|🔧 Dépannage FreePBX]]

---

### 🚀 [[10 - WDS & Déploiement]]

- [[10 - WDS & Déploiement#🧠 Concepts|🧠 Concepts]]
- [[10 - WDS & Déploiement#⚙️ Prérequis WDS (le jury adore cette question)|⚙️ Prérequis WDS (le jury adore cette question)]]
- [[10 - WDS & Déploiement#📋 Étapes déploiement WDS (à l'oral)|📋 Étapes déploiement WDS (à l'oral)]]
- [[10 - WDS & Déploiement#🆚 WDS vs FOG|🆚 WDS vs FOG]]
- [[10 - WDS & Déploiement#🔑 BYOD — Points clés|🔑 BYOD — Points clés]]

---

### 🔍 [[11 - Phase 2 Diagnostic]]

- [[11 - Phase 2 Diagnostic#🧠 La méthode (le jury note ÇA en priorité)|🧠 La méthode (le jury note ÇA en priorité)]]
- [[11 - Phase 2 Diagnostic#🚨 Panne type 1 — GLPI inaccessible depuis un poste client|🚨 Panne type 1 — GLPI inaccessible]]
- [[11 - Phase 2 Diagnostic#🚨 Panne type 2 — WiFi inaccessible|🚨 Panne type 2 — WiFi inaccessible]]
- [[11 - Phase 2 Diagnostic#🚨 Panne type 3 — Utilisateur ne peut pas se connecter au domaine|🚨 Panne type 3 — Connexion domaine impossible]]
- [[11 - Phase 2 Diagnostic#❓ Questions jury fréquentes (Phase 2)|❓ Questions jury fréquentes (Phase 2)]]
- [[11 - Phase 2 Diagnostic#💬 Phrases clés pour le jury|💬 Phrases clés pour le jury]]

---

### 🎫 [[12 - Support GLPI]]

- [[12 - Support GLPI#📚 ITIL|📚 ITIL]]
- [[12 - Support GLPI#🔄 Cycle de vie d'un ticket d'incident|🔄 Cycle de vie d'un ticket d'incident]]
- [[12 - Support GLPI#⏱️ GTR & GTI|⏱️ GTR & GTI]]
- [[12 - Support GLPI#🏗️ Niveaux de support|🏗️ Niveaux de support]]
- [[12 - Support GLPI#💻 GLPI — Actions courantes|💻 GLPI — Actions courantes]]

---

### 🎓 [[13 - Questions Jury Théorie]]

- [[13 - Questions Jury Théorie#🔄 Redondance|🔄 Redondance]]
- [[13 - Questions Jury Théorie#🏗️ Clustering|🏗️ Clustering]]
- [[13 - Questions Jury Théorie#🛡️ Cybersécurité — Organismes & Concepts|🛡️ Cybersécurité — Organismes & Concepts]]
- [[13 - Questions Jury Théorie#🌐 Modèles OSI & TCP/IP|🌐 Modèles OSI & TCP/IP]]

---

### 🌐 [[14 - DHCP & Configuration IP]]

- [[14 - DHCP & Configuration IP#🪟 Windows — Configurer une IP statique sur une interface|🪟 Windows — Configurer une IP statique]]
- [[14 - DHCP & Configuration IP#🪟 Windows Server — Installer & Configurer DHCP|🪟 Windows Server — Installer & Configurer DHCP]]
- [[14 - DHCP & Configuration IP#🐧 Linux Debian — Configurer une IP statique|🐧 Linux Debian — Configurer une IP statique]]
- [[14 - DHCP & Configuration IP#🐧 Linux Debian — Installer & Configurer un serveur DHCP|🐧 Linux Debian — Installer & Configurer un serveur DHCP]]
- [[14 - DHCP & Configuration IP#🩺 Diagnostic DHCP — Phase 2|🩺 Diagnostic DHCP — Phase 2]]

---

### 📁 [[15 - Partage de fichiers]]

- [[15 - Partage de fichiers#🪟 Windows — Partage SMB|🪟 Windows — Partage SMB]]
- [[15 - Partage de fichiers#🐧 Linux — Samba (partage avec accès Windows)|🐧 Linux — Samba (partage avec accès Windows)]]
- [[15 - Partage de fichiers#🐧 Linux — NFS (partage entre Linux uniquement)|🐧 Linux — NFS (partage entre Linux uniquement)]]
- [[15 - Partage de fichiers#🆚 SMB vs NFS — Quel choix ?|🆚 SMB vs NFS — Quel choix ?]]
- [[15 - Partage de fichiers#🔒 Droits — rappel|🔒 Droits — rappel]]
- [[15 - Partage de fichiers#🩺 Diagnostic partage — Phase 2|🩺 Diagnostic partage — Phase 2]]
