# 🌐 Réseau IP & VLANs

## 📑 Sommaire

- [[#🧮 Calcul IP — Méthode express|🧮 Calcul IP — Méthode express]]
- [[#🔬 Comprendre le CIDR — La logique binaire|🔬 Comprendre le CIDR — Logique binaire]]
- [[#🧮 Exemple exam complet : 50 hôtes sur 192.168.1.0|🧮 Exemple exam : 50 hôtes]]
- [[#🏷️ VLANs — Commandes Cisco|🏷️ VLANs — Commandes Cisco]]
- [[#🔀 Routage inter-VLAN (Router-on-a-stick)|🔀 Routage inter-VLAN]]
- [[#🔒 Port Security|🔒 Port Security]]
- [[#🧪 Diagnostic IP rapide|🧪 Diagnostic IP rapide]]
- [[#📡 Protocoles & Ports à connaître|📡 Protocoles & Ports à connaître]]
- [[#🔗 Connexions inter-thèmes|🔗 Connexions inter-thèmes]]

---

## 🧮 Calcul IP — Méthode express

### Réseau privé en /24 — Le plus fréquent à l'exam

| Hôtes utilisables max | Masque | CIDR | Taille du bloc |
|----------------------|--------|------|----------------|
| 2 | 255.255.255.252 | /30 | 4 |
| 6 | 255.255.255.248 | /29 | 8 |
| 14 | 255.255.255.240 | /28 | 16 |
| 30 | 255.255.255.224 | /27 | 32 |
| 62 | 255.255.255.192 | /26 | 64 |
| 126 | 255.255.255.128 | /25 | 128 |
| 254 | 255.255.255.0 | /24 | 256 |

**Formule :** Taille du bloc - 2 = hôtes utilisables (réseau + broadcast ne comptent pas)

---

## 🔬 Comprendre le CIDR — La logique binaire

> Le chiffre après le `/` (ex: `/26`) indique le nombre de bits à **1** dans le masque, en partant de la gauche. Tous les bits restants sont à **0**.

### Les valeurs de chaque bit dans un octet

```
Position :  1    2    3    4    5    6    7    8
Valeur   : 128   64   32   16    8    4    2    1
```

### Exemple : /26 → pourquoi 255.255.255.192 ?

```
26 bits à 1 → répartis en 4 octets :
11111111 . 11111111 . 11111111 . 11000000
   255   .    255   .    255   .    ?

Dernier octet : 11000000
→ Bits à 1 : position 1 (128) + position 2 (64)
→ 128 + 64 = 192

Masque final : 255.255.255.192 ✅
```

### Exemple : /30 → pourquoi 255.255.255.252 ?

```
30 bits à 1 :
11111111 . 11111111 . 11111111 . 11111100
   255   .    255   .    255   .    ?

Dernier octet : 11111100
→ 128 + 64 + 32 + 16 + 8 + 4 = 252

Masque final : 255.255.255.252 ✅
```

### Exemple : /27 → pourquoi 255.255.255.224 ?

```
27 bits à 1 :
11111111 . 11111111 . 11111111 . 11100000
   255   .    255   .    255   .    ?

Dernier octet : 11100000
→ 128 + 64 + 32 = 224

Masque final : 255.255.255.224 ✅
```

> **Mémo :** Un octet complet de 8 bits à 1 = toujours **255**. Tu ne travailles que sur le dernier octet variable — c'est juste une addition des puissances de 2.

---

## 🧮 Exemple exam complet : 50 hôtes sur 192.168.1.0

```
1. Besoin 50 hôtes → 2^6 = 64 > 50 ✅ → 6 bits hôtes
2. CIDR = 32 - 6 = /26
3. Dernier octet masque : 11000000 → 128 + 64 = 192
4. Masque = 255.255.255.192

@Réseau    = 192.168.1.0/26
@Broadcast = 192.168.1.63
Plage IP   = 192.168.1.1 → 192.168.1.62 (62 hôtes)
```

---

## 🏷️ VLANs — Commandes Cisco

### Créer des VLANs

```cisco
SWITCH(config)#vlan 10
SWITCH(config-vlan)#name Marketing
SWITCH(config-vlan)#exit

SWITCH(config)#vlan 20
SWITCH(config-vlan)#name Direction
SWITCH(config-vlan)#exit
```

### Affecter des ports (access = un seul VLAN)

```cisco
SWITCH(config)#interface range GigabitEthernet0/1-2
SWITCH(config-if-range)#switchport mode access
SWITCH(config-if-range)#switchport access vlan 10
SWITCH(config-if-range)#exit

SWITCH(config)#interface range GigabitEthernet0/3-4
SWITCH(config-if-range)#switchport mode access
SWITCH(config-if-range)#switchport access vlan 20
SWITCH(config-if-range)#exit
```

### Port trunk (transporte plusieurs VLANs via 802.1Q)

> Un trunk = lien qui transporte plusieurs VLANs grâce à l'étiquetage 802.1Q.
> Par défaut il transporte tous les VLANs, mais **bonne pratique : limiter avec `allowed vlan`**.

```cisco
SWITCH(config)#interface GigabitEthernet0/48
SWITCH(config-if)#switchport mode trunk
SWITCH(config-if)#switchport trunk allowed vlan 10,20
SWITCH(config-if)#exit
```

> ⚠️ Sur certains switchs Cisco (ex: 3560), la commande `switchport trunk encapsulation dot1q` est nécessaire avant `switchport mode trunk`. Sur les switchs 2960 (Packet Tracer), dot1q est déjà le mode par défaut — la commande peut être refusée, c'est normal.

### Vérifications

```cisco
show vlan brief
show interfaces trunk
show interfaces GigabitEthernet0/1 switchport
```

---

## 🔀 Routage inter-VLAN (Router-on-a-stick)

> Un seul câble physique routeur ↔ switch, avec des sous-interfaces. Le switch doit avoir un port trunk vers le routeur.

### Côté switch — trunk vers le routeur

```cisco
SWITCH(config)#interface GigabitEthernet0/48
SWITCH(config-if)#switchport mode trunk
SWITCH(config-if)#switchport trunk allowed vlan 10,20
SWITCH(config-if)#exit
```

### Côté routeur — sous-interfaces

```cisco
Router(config)#interface GigabitEthernet0/0.10
Router(config-subif)#encapsulation dot1q 10
Router(config-subif)#ip address 192.168.10.1 255.255.255.0
Router(config-subif)#exit

Router(config)#interface GigabitEthernet0/0.20
Router(config-subif)#encapsulation dot1q 20
Router(config-subif)#ip address 192.168.20.1 255.255.255.0
Router(config-subif)#exit

Router(config)#interface GigabitEthernet0/0
Router(config-if)#no shutdown
```

### Passerelle pour chaque VLAN

> Chaque VLAN a son propre sous-réseau. Pour communiquer avec un autre VLAN, les postes utilisent leur passerelle = l'adresse IP de la sous-interface du routeur.

```
VLAN 10 :
  PC       : 192.168.10.x/24
  Passerelle : 192.168.10.1  ← sous-interface G0/0.10

VLAN 20 :
  PC       : 192.168.20.x/24
  Passerelle : 192.168.20.1  ← sous-interface G0/0.20
```

---

## 🔒 Port Security

```cisco
SWITCH(config)#interface GigabitEthernet0/1
SWITCH(config-if)#switchport mode access
SWITCH(config-if)#switchport port-security
SWITCH(config-if)#switchport port-security maximum 1
SWITCH(config-if)#switchport port-security mac-address 0094.EEFA.9669
SWITCH(config-if)#switchport port-security violation shutdown
```

> Si violation → port passe en **err-disabled** + log envoyé

---

## 🧪 Diagnostic IP rapide

### Si un poste ne communique pas

```
1. Vérifier l'adresse IP
   ipconfig /all  (Windows)
   ip a           (Linux)

2. Vérifier le masque
   Le poste est-il dans le bon sous-réseau ?

3. Vérifier la passerelle
   Elle doit être dans le même sous-réseau que le poste.

4. Vérifier le DNS
   Ping IP OK mais ping nom KO → problème DNS probable.

5. Vérifier le DHCP
   Adresse 169.254.x.x = APIPA = DHCP non reçu.

6. Vérifier VLAN/trunk
   show vlan brief
   show interfaces trunk

7. Vérifier routage inter-VLAN
   Les sous-interfaces existent-elles ?
   Le trunk vers le routeur est-il actif ?
```

> **APIPA (169.254.x.x)** = le poste n'a pas reçu d'IP du DHCP. Causes possibles : DHCP arrêté, mauvais VLAN, pas de relais DHCP entre VLANs.

---

## 📡 Protocoles & Ports à connaître

| Protocole | Port | Usage |
|-----------|------|-------|
| SSH | 22 | Accès distant sécurisé |
| HTTP | 80 | Web |
| HTTPS | 443 | Web sécurisé |
| DNS | 53 | Résolution de noms |
| DHCP | 67/68 | Attribution IP |
| LDAP | 389 | Annuaire AD |
| LDAPS | 636 | LDAP sécurisé |
| Kerberos | 88 | Authentification AD |
| SMB | 445 | Partage fichiers Windows |
| RDP | 3389 | Bureau à distance |
| **SIP** | **5060** | Téléphonie IP |
| **SIPS** | **5061** | SIP sécurisé |

---

## 🔗 Connexions inter-thèmes

> **VLAN sans pool DHCP ou sans relais DHCP** → les postes tombent en APIPA (169.254.x.x)
> **Calculs IP ↔ VLANs** → chaque VLAN a son propre sous-réseau
> **dot1q ↔ routage inter-VLAN** → dot1q étiquette les trames ; le routeur lit ces étiquettes via ses sous-interfaces
> **Trunk oublié côté switch** → le routeur a ses sous-interfaces mais ne reçoit rien → inter-VLAN KO
