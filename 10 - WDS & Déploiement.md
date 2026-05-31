# 🚀 WDS & Déploiement

## 🧠 Concepts

| Terme | Définition |
|-------|-----------|
| **WDS** | Windows Deployment Services — déploie Windows par le réseau |
| **PXE** | Pre-boot eXecution Environment — démarre un PC depuis le réseau (sans OS local) |
| **Image de démarrage** | Boot WinPE pour démarrer le déploiement |
| **Image d'installation** | L'OS à déployer (install.wim) |
| **BYOD** | Bring Your Own Device — politique autorisant les équipements personnels en entreprise |
| **FOG** | Solution open source de clonage de postes par le réseau |

---

## ⚙️ Prérequis WDS (le jury adore cette question)

> **WDS a besoin de :**
> 1. **DHCP** fonctionnel sur le réseau (pour que les clients PXE obtiennent une IP)
> 2. **DNS** (pour la résolution de noms)
> 3. Un partage réseau pour stocker les images

> Si WDS et DHCP sont sur le **même serveur** → configurer les options 60/66/67 sur le serveur DHCP

---

## 📋 Étapes déploiement WDS (à l'oral)

1. Installer le rôle **WDS** sur Windows Server
2. Configurer WDS : dossier de stockage des images, paramètres réseau
3. **Ajouter une image de démarrage** (boot.wim depuis le DVD Windows)
4. **Ajouter une image d'installation** (install.wim depuis le DVD Windows)
5. Sur le client : démarrer sur le réseau (PXE) → F12 au boot
6. Le client contacte le serveur DHCP → obtient une IP → contacte WDS
7. WinPE se charge → assistant d'installation → choisir l'image → déployer

---

## 🆚 WDS vs FOG

| | WDS | FOG |
|--|-----|-----|
| **Éditeur** | Microsoft | Open Source |
| **OS déployé** | Windows uniquement | Windows + Linux |
| **Licence** | Inclus Windows Server | Gratuit |
| **Usage** | Entreprises Windows | PME, écoles |

---

## 🔑 BYOD — Points clés

- Politique qui autorise l'usage d'appareils **personnels** (téléphones, laptops) dans un contexte professionnel
- **Risques :** sécurité des données, confidentialité, malwares
- **Mesures :** MDM (Mobile Device Management), VPN, segmentation réseau, chiffrement

---

## 🔗 Connexions inter-thèmes

> **WDS ↔ DHCP** → sans DHCP, PXE ne peut pas donner d'IP au client → WDS inutilisable
> **WDS ↔ DNS** → résolution de noms nécessaire
> **PXE ↔ VLAN** → le VLAN de déploiement doit avoir accès au serveur WDS/DHCP
