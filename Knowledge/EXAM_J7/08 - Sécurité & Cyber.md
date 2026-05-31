# 🛡️ Sécurité & Cyber

## 📑 Sommaire

- [[#🏛️ Organismes à connaître (Phase 2 garanti)|🏛️ Organismes à connaître (Phase 2 garanti)]]
- [[#🔺 Triade CIA (fondamental)|🔺 Triade CIA (fondamental)]]
- [[#📋 Modèle OSI — 7 couches|📋 Modèle OSI — 7 couches]]
- [[#🔥 Pare-feu — Lire des règles|🔥 Pare-feu — Lire des règles]]
- [[#🔐 Sécurité SSH (ANSSI)|🔐 Sécurité SSH (ANSSI)]]
- [[#🌐 802.1X|🌐 802.1X]]
- [[#🔑 RGPD — Points clés|🔑 RGPD — Points clés]]
- [[#🔗 Connexions inter-thèmes|🔗 Connexions inter-thèmes]]

---

## 🏛️ Organismes à connaître (Phase 2 garanti)

| Organisme | Rôle |
|-----------|------|
| **ANSSI** | Agence Nationale de la Sécurité des Systèmes d'Information — référent cybersécurité France |
| **CNIL** | Commission Nationale Informatique et Libertés — protection des données personnelles |
| **CERT-FR** | Centre gouvernemental de veille et réponse aux attaques informatiques (rattaché ANSSI) |
| **ENISA** | Agence européenne pour la cybersécurité |
| **RGPD** | Règlement Général sur la Protection des Données (européen) |

---

## 🔺 Triade CIA (fondamental)

| Lettre | Concept | Définition |
|--------|---------|-----------|
| **C** | Confidentialité | Seules les personnes autorisées accèdent aux données |
| **I** | Intégrité | Les données ne sont pas altérées sans autorisation |
| **A** | Disponibilité | Les données sont accessibles quand on en a besoin |

---

## 📋 Modèle OSI — 7 couches

| # | Couche | PDU | Rôle | Équipement |
|---|--------|-----|------|-----------|
| 7 | Application | Données | Interface utilisateur (HTTP, FTP, DNS) | — |
| 6 | Présentation | Données | Chiffrement, compression | — |
| 5 | Session | Données | Gestion des sessions | — |
| 4 | Transport | Segment | TCP/UDP, ports | — |
| 3 | Réseau | Paquet | Adressage IP, routage | Routeur |
| 2 | Liaison | Trame | Adresse MAC, VLAN | Switch |
| 1 | Physique | Bit | Câbles, signaux | Hub, câble |

> **Mémo :** "**A**h **P**our **S**e **T**rouver **R**éseau **L**iaison **P**hysique"

**Réponse jury "différence couche 2 vs 3" :**
> "La couche 2 travaille avec les adresses MAC et gère la communication sur le réseau local. La couche 3 travaille avec les adresses IP et gère le routage entre réseaux différents. Un switch travaille en couche 2, un routeur en couche 3."

---

## 🔥 Pare-feu — Lire des règles

| Règle | Interprétation |
|-------|---------------|
| `* → 88.88.88.88:443 TCP ACCEPT` | Tout le monde peut envoyer du HTTPS vers cette IP |
| `* → 88.88.88.88:443 TCP ACCEPT + PAT vers IPServeurWeb:443` | On accepte ET on redirige vers le serveur web interne (NAT/PAT) |

**DMZ :** Zone neutre entre Internet et le réseau interne, protégée par des pare-feux des deux côtés.

---

## 🔐 Sécurité SSH (ANSSI)

| Directive sshd_config | Raison sécuritaire ANSSI |
|-----------------------|--------------------------|
| `Port 2222` | Évite les scans automatiques sur le port 22 |
| `PermitRootLogin no` | Empêche l'accès direct au compte le plus puissant |
| `PasswordAuthentication no` | Force les clés SSH (bien plus robustes que les mots de passe) |
| `MaxAuthTries 3` | Limite les attaques par force brute |

---

## 🌐 802.1X

> Standard de contrôle d'accès au réseau. Authentifie les équipements **avant** de leur donner accès au réseau. Utilise EAP + RADIUS.
> Usage : WiFi entreprise, LAN sécurisé.

---

## 🔑 RGPD — Points clés

- Droit à l'oubli, droit d'accès, droit de portabilité
- Notification de violation de données dans les **72h**
- DPO (Délégué à la Protection des Données) obligatoire dans certains cas
- Sanctions jusqu'à **4% du CA mondial** ou 20M€

---

## 🔗 Connexions inter-thèmes

> **sshd_config ↔ ANSSI** → chaque paramètre SSH a une justification dans les guides ANSSI
> **RGPD ↔ CNIL** → la CNIL est l'autorité de contrôle RGPD en France
> **CIA ↔ Sauvegardes** → les sauvegardes répondent au A (Disponibilité)
