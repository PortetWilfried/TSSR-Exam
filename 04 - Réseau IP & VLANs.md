# 🌐 Réseau IP & VLANs

## 🧮 Calcul IP — Méthode express

### Classe C (192.168.x.x) — Le plus fréquent à l'exam

| Hôtes nécessaires | Masque | CIDR | Hôtes utilisables |
|-------------------|--------|------|-------------------|
| 2 | 255.255.255.252 | /30 | 2 |
| 6 | 255.255.255.248 | /29 | 6 |
| 14 | 255.255.255.240 | /28 | 14 |
| 30 | 255.255.255.224 | /27 | 30 |
| 62 | 255.255.255.192 | /26 | 62 |
| 126 | 255.255.255.128 | /25 | 126 |
| 254 | 255.255.255.0 | /24 | 254 |

**Formule :** 2^n - 2 hôtes utilisables (n = bits hôtes)

### Exemple exam : 50 hôtes sur 192.168.1.0

1. Besoin 50 hôtes → 2^6 = 64 > 50 ✅ → **6 bits hôtes**
2. Masque = 32 - 6 = **/26** → 255.255.255.192
3. @Réseau = 192.168.1.0/26
4. @Broadcast = 192.168.1.63
5. Plage hôtes = 192.168.1.1 à 192.168.1.62

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

### Port trunk (laisse passer tous les VLANs — dot1q)

```cisco
SWITCH(config)#interface GigabitEthernet0/48
SWITCH(config-if)#switchport mode trunk
SWITCH(config-if)#switchport trunk encapsulation dot1q
SWITCH(config-if)#exit
```

### Vérifications

```cisco
show vlan brief                  # Liste tous les VLANs et leurs ports
show interfaces trunk            # Ports en mode trunk
show interfaces GigabitEthernet0/1 switchport   # Détail d'un port
```

---

## 🔀 Routage inter-VLAN (Router-on-a-stick)

> Un seul câble physique routeur ↔ switch, avec des sous-interfaces.

```cisco
! Sur le routeur
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

> **VLAN sans DHCP dédié** → les machines du VLAN n'ont pas d'IP automatique
> **Calculs IP ↔ VLANs** → chaque VLAN a son propre sous-réseau
> **dot1q ↔ routage inter-VLAN** → dot1q = le "langage" qui étiquette les trames entre switch et routeur
