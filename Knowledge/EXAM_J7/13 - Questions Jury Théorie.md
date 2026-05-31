# 🎓 Questions Jury — Théorie pure

> Cette fiche regroupe les sujets qui sont **uniquement des questions orales** en Phase 2 — pas de manip, juste de la théorie à connaître pour répondre clairement au jury.

---

## 📑 Sommaire

- [[#🔄 Redondance|🔄 Redondance]]
- [[#🏗️ Clustering|🏗️ Clustering]]
- [[#🛡️ Cybersécurité — Organismes & Concepts|🛡️ Cybersécurité — Organismes & Concepts]]
- [[#🌐 Modèles OSI & TCP/IP|🌐 Modèles OSI & TCP/IP]]
- [[#🔗 Connexions inter-thèmes|🔗 Connexions inter-thèmes]]

---

## 🔄 Redondance

> **Concept clé :** Avoir plusieurs éléments qui font le même travail, pour qu'un seul puisse tomber sans interrompre le service.

### Redondance de routage

| Protocole | Type | Rôle |
|-----------|------|------|
| **HSRP** | Cisco propriétaire | Plusieurs routeurs partagent une IP virtuelle. Si le routeur actif tombe, un autre prend le relais. |
| **VRRP** | Standard (RFC) | Équivalent ouvert de HSRP |
| **GLBP** | Cisco propriétaire | Comme HSRP mais répartit la charge entre routeurs |

> **Analogie :** Comme un binôme de pilotes dans un avion. Si l'un est indisponible, l'autre prend les commandes sans que les passagers s'en aperçoivent.

### Redondance de commutation

| Protocole | Rôle |
|-----------|------|
| **STP** (Spanning Tree Protocol) | Évite les boucles réseau quand on a plusieurs switchs reliés entre eux |
| **RSTP** | Version rapide de STP (convergence en quelques secondes) |
| **EtherChannel / LACP** | Agréger plusieurs liens physiques pour former un lien logique plus rapide ET redondant |

> **Pourquoi STP ?** Si on connecte 2 switchs avec 2 câbles pour la redondance, une trame broadcast tournerait à l'infini → tempête de broadcast → réseau KO. STP désactive logiquement le lien en double tant que le lien principal fonctionne.

### Redondance de services

| Élément | Comment redonder |
|---------|------------------|
| **Serveur AD** | Plusieurs contrôleurs de domaine (DC) qui se répliquent |
| **DNS** | 2+ serveurs DNS, le client a une IP primaire et secondaire |
| **DHCP** | Failover DHCP Windows ou split scope (2 serveurs avec plages différentes) |
| **Web** | Load balancer + plusieurs serveurs web |
| **Stockage** | RAID (1, 5, 6, 10) au niveau disques ; SAN/NAS redondés au niveau infra |

### Phrases jury

> "La redondance, c'est avoir plusieurs éléments capables de faire le même travail. Si l'un tombe, le service continue. Au niveau routeur on utilise HSRP ou VRRP, au niveau switch on utilise STP pour éviter les boucles, et au niveau services on duplique les serveurs critiques comme l'AD, le DNS ou le DHCP."

> "STP empêche les boucles dans un réseau commuté qui a des liens redondants. Sans STP, les broadcasts tourneraient en boucle et satureraient le réseau."

---

## 🏗️ Clustering

> **Concept clé :** Un cluster = un groupe de serveurs qui se présentent comme **un seul système** aux utilisateurs.

### Types de clusters

| Type | Comment ça marche | Exemple |
|------|------------------|---------|
| **Actif/Passif** | 1 serveur travaille, l'autre attend en standby. Si l'actif tombe, le passif prend le relais. | Cluster SQL Server, base de données |
| **Actif/Actif** | Tous les serveurs travaillent en même temps, la charge est répartie. | Ferme web, load balancing |
| **HA (High Availability)** | Vise zéro interruption — basculement automatique en cas de panne | Cluster Hyper-V, VMware HA |
| **Load Balancing** | Répartit la charge sans forcément redonder | NLB Windows, HAProxy |

### Solutions Microsoft

| Outil | Usage |
|-------|-------|
| **WSFC** (Windows Server Failover Clustering) | Cluster de failover pour services Windows (SQL, fichiers, Hyper-V) |
| **NLB** (Network Load Balancing) | Répartition de charge pour services stateless (web, VPN) |

### Solutions Linux

| Outil | Usage |
|-------|-------|
| **Pacemaker / Corosync** | Cluster de failover |
| **Keepalived** | Gestion d'IP virtuelle + failover (VRRP) |
| **HAProxy** | Load balancer applicatif |

### Notions associées

- **Quorum** : mécanisme qui décide quel nœud est "valide" si le cluster se coupe en deux (split-brain)
- **Split-brain** : situation où deux nœuds pensent être actifs en même temps → données corrompues
- **Heartbeat** : signal régulier entre nœuds pour vérifier qu'ils sont vivants
- **Failover** : bascule automatique vers le nœud de secours
- **Failback** : retour au nœud principal quand il est rétabli

### Phrases jury

> "Un cluster, c'est un groupe de serveurs qui travaillent ensemble et apparaissent comme un seul système. Il y a deux grands modes : actif/passif où un serveur prend le relais si l'autre tombe, et actif/actif où tous les serveurs travaillent en parallèle et se partagent la charge."

> "Le clustering répond à la disponibilité — la lettre A de la triade CIA. Contrairement à la sauvegarde qui sert à récupérer après un incident, le clustering vise à ne **jamais** interrompre le service."

> **Pause — lien avec redondance :** Le clustering est une forme avancée de redondance, appliquée à des services applicatifs (BDD, web, virtu). La redondance peut être simple (2 DNS) ou complexe (cluster avec quorum et heartbeat).

---

## 🛡️ Cybersécurité — Organismes & Concepts

### Organismes français

| Organisme | Rôle |
|-----------|------|
| **ANSSI** | Agence Nationale de la Sécurité des Systèmes d'Information. Référent cyber pour l'État et les OIV (Opérateurs d'Importance Vitale). Publie des guides de bonnes pratiques. |
| **CERT-FR** | Centre gouvernemental de veille, d'alerte et de réponse aux attaques informatiques. Rattaché à l'ANSSI. |
| **CNIL** | Commission Nationale Informatique et Libertés. Autorité de contrôle pour la protection des données personnelles (RGPD). |
| **Cybermalveillance.gouv.fr** | Plateforme d'assistance aux victimes (particuliers, entreprises, collectivités) |

### Organismes européens / internationaux

| Organisme | Rôle |
|-----------|------|
| **ENISA** | Agence européenne pour la cybersécurité |
| **CERT-EU** | CERT pour les institutions européennes |
| **NIST** (USA) | National Institute of Standards and Technology. Publie des référentiels (NIST CSF, NIST 800-53) très utilisés en cyber. |

### Réglementations

| Texte | Domaine |
|-------|---------|
| **RGPD** | Protection des données personnelles (européen, 2018). Notification d'une violation en **72h**. Sanctions jusqu'à 4% du CA mondial ou 20M€. |
| **NIS 2** | Directive européenne sécurité des réseaux et systèmes d'information (s'applique aux entités essentielles et importantes) |
| **LPM** | Loi de Programmation Militaire — encadre la cyber des OIV en France |

### Triade CIA

| Lettre | Concept | Définition | Exemple de protection |
|--------|---------|-----------|----------------------|
| **C** | Confidentialité | Seules les personnes autorisées accèdent | Chiffrement, contrôle d'accès |
| **I** | Intégrité | Les données ne sont pas altérées | Hash, signature, contrôles d'intégrité |
| **A** | Disponibilité | Les données sont accessibles quand on en a besoin | Redondance, clustering, sauvegardes |

### Bonnes pratiques ANSSI (à citer)

- Principe du **moindre privilège** : donner exactement les droits nécessaires, pas plus
- **Cloisonnement** : segmenter le réseau (VLANs, DMZ)
- **Défense en profondeur** : plusieurs couches de sécurité, pas un seul rempart
- **Mises à jour régulières** : OS + applications
- **Sauvegardes** + tests de restauration
- **Journalisation** : logs centralisés pour détecter les incidents
- **Sensibilisation** des utilisateurs

### Phrases jury

> "L'ANSSI est l'agence française de référence en cybersécurité. Elle publie des guides de bonnes pratiques que je consulte régulièrement, et elle coordonne la réponse aux incidents via le CERT-FR."

> "La CNIL est l'autorité de contrôle du RGPD en France. En cas de fuite de données personnelles, on a 72 heures pour la notifier."

> "La cybersécurité repose sur la triade CIA : Confidentialité, Intégrité, Disponibilité. Toute mesure de sécurité vise au moins une de ces trois propriétés."

---

## 🌐 Modèles OSI & TCP/IP

### OSI — 7 couches

| # | Couche | PDU | Rôle | Exemples |
|---|--------|-----|------|----------|
| 7 | **Application** | Données | Interface utilisateur | HTTP, FTP, DNS, SMTP |
| 6 | **Présentation** | Données | Chiffrement, compression, format | SSL/TLS, JPEG, ASCII |
| 5 | **Session** | Données | Gestion des sessions | NetBIOS, RPC |
| 4 | **Transport** | Segment | Livraison fiable, ports | **TCP, UDP** |
| 3 | **Réseau** | Paquet | Adressage logique, routage | **IP, ICMP, OSPF** |
| 2 | **Liaison** | Trame | Adressage physique (MAC) | Ethernet, ARP, VLAN |
| 1 | **Physique** | Bit | Signaux, câbles | RJ45, fibre, Wi-Fi |

> **Mémo des couches (du bas vers le haut) :**
> **"Pour Le Réseau Tout Se Passe Automatiquement"**
> Physique → Liaison → Réseau → Transport → Session → Présentation → Application

### Équipements par couche

| Couche | Équipement |
|--------|-----------|
| 1 | Hub, répéteur, câble |
| 2 | Switch, pont (bridge) |
| 3 | Routeur, switch L3 |
| 4-7 | Pare-feu applicatif, proxy, load balancer |

### TCP/IP — 4 couches (modèle pratique)

| # | Couche TCP/IP | Correspondance OSI | Protocoles |
|---|---------------|-------------------|-----------|
| 4 | **Application** | OSI 5+6+7 | HTTP, FTP, DNS, SMTP, SSH |
| 3 | **Transport** | OSI 4 | TCP, UDP |
| 2 | **Internet** | OSI 3 | IP, ICMP, ARP |
| 1 | **Accès réseau** | OSI 1+2 | Ethernet, Wi-Fi |

> **OSI vs TCP/IP :**
> - OSI = modèle **théorique/pédagogique** en 7 couches
> - TCP/IP = modèle **pratique/réel** en 4 couches (celui qui tourne réellement sur Internet)

### TCP vs UDP

| | TCP | UDP |
|--|-----|-----|
| **Connexion** | Avec connexion (handshake 3 voies) | Sans connexion |
| **Fiabilité** | Garanti (accusés de réception, retransmission) | Non garanti |
| **Vitesse** | Plus lent | Plus rapide |
| **Usage** | Web (HTTP/HTTPS), mail, SSH, fichiers | DNS, VoIP, streaming, jeux |

### Encapsulation (à expliquer au jury)

```
Émission (de haut en bas) :
Données     → couche Application
+ entête L4 → Segment (TCP/UDP)
+ entête L3 → Paquet (IP)
+ entête L2 → Trame (Ethernet)
+ codage L1 → Bits sur le câble

Réception : on désencapsule de bas en haut.
```

### Phrases jury

> "Le modèle OSI a 7 couches, c'est un modèle théorique qui sert à comprendre comment fonctionne un réseau. TCP/IP est un modèle pratique en 4 couches, c'est lui qui tourne réellement sur Internet."

> "Un switch travaille en couche 2 avec les adresses MAC. Un routeur travaille en couche 3 avec les adresses IP. Un pare-feu applicatif peut travailler jusqu'en couche 7."

> "TCP est utilisé quand on a besoin de fiabilité, par exemple pour charger une page web ou envoyer un mail. UDP est utilisé quand la vitesse compte plus que la fiabilité, par exemple pour la voix sur IP ou le DNS."

### Question piège fréquente

**"Différence entre adresse MAC et IP ?"**
> "L'adresse MAC est une adresse physique, gravée sur la carte réseau, utilisée en couche 2 pour la communication sur le réseau local. L'adresse IP est une adresse logique, attribuée par configuration ou DHCP, utilisée en couche 3 pour le routage entre réseaux différents."

**"Pourquoi le modèle OSI est utile pour diagnostiquer une panne ?"**
> "Parce qu'il permet de diagnostiquer couche par couche, en partant du bas. Je vérifie d'abord la couche physique — le câble est branché ? — puis la liaison — le VLAN est bon ? — puis le réseau — l'IP est correcte ? Le ping passe ? — et ainsi de suite jusqu'à l'application."

---

## 🔗 Connexions inter-thèmes

> **Redondance ↔ Clustering** → le clustering est une forme avancée de redondance appliquée aux services
> **Clustering ↔ CIA** → le clustering répond au **A** (Disponibilité)
> **Sauvegardes ↔ Clustering** → sauvegardes = récupérer après ; clustering = éviter l'interruption
> **OSI ↔ Diagnostic Phase 2** → diagnostiquer couche par couche du bas vers le haut
> **TCP/IP ↔ OSI** → mêmes idées, organisation différente. OSI = théorie ; TCP/IP = pratique
