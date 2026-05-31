# 🌐 DHCP & Configuration IP — Windows + Linux

> **Concept clé :** DHCP = Dynamic Host Configuration Protocol. Un serveur qui **distribue automatiquement** des IP, masques, passerelles et DNS aux clients du réseau.
> Ports : **67** (serveur) / **68** (client) — UDP.

---

## 📑 Sommaire

- [[#🪟 Windows — Configurer une IP statique sur une interface|🪟 Windows — Configurer une IP statique]]
- [[#🪟 Windows Server — Installer & Configurer DHCP|🪟 Windows Server — Installer & Configurer DHCP]]
- [[#🐧 Linux Debian — Configurer une IP statique|🐧 Linux Debian — Configurer une IP statique]]
- [[#🐧 Linux Debian — Installer & Configurer un serveur DHCP|🐧 Linux Debian — Installer & Configurer un serveur DHCP]]
- [[#🩺 Diagnostic DHCP — Phase 2|🩺 Diagnostic DHCP — Phase 2]]
- [[#🔗 Connexions inter-thèmes|🔗 Connexions inter-thèmes]]

---

## 🪟 Windows — Configurer une IP statique sur une interface

### Via GUI

`Panneau de configuration > Centre Réseau > Modifier les paramètres de la carte`
→ clic droit sur la carte → **Propriétés**
→ Double-clic sur **Protocole Internet version 4 (TCP/IPv4)**
→ Cocher **Utiliser l'adresse IP suivante** :

```
Adresse IP        : 192.168.1.10
Masque            : 255.255.255.0
Passerelle        : 192.168.1.1
DNS préféré       : 192.168.1.1
DNS auxiliaire    : 8.8.8.8
```

### Via PowerShell (rapide à l'exam)

```powershell
# Voir les interfaces disponibles
Get-NetAdapter

# Configurer une IP statique
New-NetIPAddress `
  -InterfaceAlias "Ethernet0" `
  -IPAddress 192.168.1.10 `
  -PrefixLength 24 `
  -DefaultGateway 192.168.1.1

# Configurer les DNS
Set-DnsClientServerAddress `
  -InterfaceAlias "Ethernet0" `
  -ServerAddresses ("192.168.1.1","8.8.8.8")

# Vérifier
ipconfig /all
Get-NetIPConfiguration
```

### Repasser en DHCP

```powershell
Set-NetIPInterface -InterfaceAlias "Ethernet0" -Dhcp Enabled
Set-DnsClientServerAddress -InterfaceAlias "Ethernet0" -ResetServerAddresses
ipconfig /renew
```

### Commandes de diagnostic Windows

```cmd
ipconfig /all              # Voir toute la conf IP
ipconfig /release          # Libérer le bail DHCP
ipconfig /renew            # Demander un nouveau bail DHCP
ipconfig /flushdns         # Vider le cache DNS
ping 192.168.1.1
nslookup intranet.local
```

---

## 🪟 Windows Server — Installer & Configurer DHCP

### 1. Installer le rôle DHCP

```powershell
Install-WindowsFeature DHCP -IncludeManagementTools
```

Ou via **Server Manager > Ajouter des rôles > DHCP Server**.

### 2. Autoriser le serveur dans l'AD (obligatoire si membre du domaine)

```powershell
Add-DhcpServerInDC -DnsName "SRV-DHCP-01.tvs.lan" -IPAddress 192.168.1.5
```

### 3. Créer une étendue (scope) via GUI

`dhcpmgmt.msc` → clic droit IPv4 → **Nouvelle étendue**

| Paramètre | Valeur exemple |
|-----------|---------------|
| Nom de l'étendue | `Scope-LAN` |
| Plage de début | `192.168.1.100` |
| Plage de fin | `192.168.1.200` |
| Masque | `255.255.255.0` (/24) |
| Exclusions | `192.168.1.100` à `192.168.1.110` (pour serveurs) |
| Durée du bail | 8 jours (défaut) |
| Routeur (passerelle) | `192.168.1.1` |
| DNS | `192.168.1.5` (le DC) |
| Domaine DNS | `tvs.lan` |

### 3. Créer une étendue via PowerShell

```powershell
Add-DhcpServerv4Scope `
  -Name "Scope-LAN" `
  -StartRange 192.168.1.100 `
  -EndRange 192.168.1.200 `
  -SubnetMask 255.255.255.0 `
  -State Active

# Options du scope
Set-DhcpServerv4OptionValue -ScopeId 192.168.1.0 `
  -Router 192.168.1.1 `
  -DnsServer 192.168.1.5 `
  -DnsDomain "tvs.lan"

# Activer le scope
Set-DhcpServerv4Scope -ScopeId 192.168.1.0 -State Active
```

### 4. Réservation DHCP (IP fixe pour une MAC donnée)

```powershell
Add-DhcpServerv4Reservation `
  -ScopeId 192.168.1.0 `
  -IPAddress 192.168.1.50 `
  -ClientId "AA-BB-CC-DD-EE-FF" `
  -Name "PC-Compta-01"
```

### 5. Vérifications

```powershell
Get-DhcpServerv4Scope                              # Liste les scopes
Get-DhcpServerv4Lease -ScopeId 192.168.1.0         # Voir les baux distribués
Get-DhcpServerv4Reservation -ScopeId 192.168.1.0   # Voir les réservations
```

---

## 🐧 Linux Debian — Configurer une IP statique

### Méthode classique : `/etc/network/interfaces`

```bash
# Sauvegarder avant !
cp /etc/network/interfaces /etc/network/interfaces.bak

nano /etc/network/interfaces
```

```ini
# Loopback
auto lo
iface lo inet loopback

# Interface eth0 en statique
auto eth0
iface eth0 inet static
    address 192.168.1.10
    netmask 255.255.255.0
    gateway 192.168.1.1
    dns-nameservers 192.168.1.1 8.8.8.8
```

```bash
# Appliquer
systemctl restart networking
# Ou redémarrer juste l'interface
ifdown eth0 && ifup eth0

# Vérifier
ip a
ip r
ping 192.168.1.1
```

### Mode DHCP (client)

```ini
auto eth0
iface eth0 inet dhcp
```

### Trouver le nom de l'interface

```bash
ip a                  # Liste toutes les interfaces
ip link show          # Idem, plus court
```

> Le nom peut être `eth0`, `ens33`, `enp0s3`, `enp3s0`... selon la VM / la version Debian.

---

## 🐧 Linux Debian — Installer & Configurer un serveur DHCP

### 1. Installer le paquet

```bash
apt update
apt install isc-dhcp-server -y
```

### 2. Définir l'interface d'écoute

```bash
cp /etc/default/isc-dhcp-server /etc/default/isc-dhcp-server.bak
nano /etc/default/isc-dhcp-server
```

```bash
INTERFACESv4="eth0"
```

### 3. Configurer le scope

```bash
cp /etc/dhcp/dhcpd.conf /etc/dhcp/dhcpd.conf.bak
nano /etc/dhcp/dhcpd.conf
```

```bash
# Configuration globale
default-lease-time 600;
max-lease-time 7200;
authoritative;

# Sous-réseau distribué
subnet 192.168.1.0 netmask 255.255.255.0 {
    range 192.168.1.100 192.168.1.200;
    option routers 192.168.1.1;
    option subnet-mask 255.255.255.0;
    option domain-name-servers 192.168.1.1, 8.8.8.8;
    option domain-name "tvs.lan";
    option broadcast-address 192.168.1.255;
}

# Réservation (IP fixe pour une MAC)
host pc-compta-01 {
    hardware ethernet AA:BB:CC:DD:EE:FF;
    fixed-address 192.168.1.50;
}
```

### 4. Démarrer & vérifier

```bash
# Tester la config avant restart
dhcpd -t -cf /etc/dhcp/dhcpd.conf

# Démarrer et activer au boot
systemctl restart isc-dhcp-server
systemctl enable isc-dhcp-server
systemctl status isc-dhcp-server

# Vérifier les logs
journalctl -u isc-dhcp-server -n 50
tail -f /var/log/syslog | grep dhcp

# Voir les baux distribués
cat /var/lib/dhcp/dhcpd.leases
```

### 5. Côté client (pour tester)

```bash
# Sur un autre poste Linux
dhclient -r eth0      # Libérer le bail
dhclient eth0          # Demander une nouvelle IP
ip a                   # Vérifier l'IP reçue
```

---

## 🩺 Diagnostic DHCP — Phase 2

### Symptômes & causes

| Symptôme | Cause probable | Vérification |
|----------|---------------|--------------|
| IP en 169.254.x.x (APIPA) | Pas de réponse DHCP | Serveur DHCP arrêté ? Bon VLAN ? |
| Toujours la même IP malgré renew | Réservation ou bail long | `Get-DhcpServerv4Reservation` |
| Aucun client n'obtient d'IP | Service arrêté ou mauvaise interface | `systemctl status` (Linux) / `dhcpmgmt.msc` (Windows) |
| Clients sur d'autres VLANs n'obtiennent rien | Pas de relais DHCP (`ip helper-address`) | Vérifier conf routeur |

### Relais DHCP (DHCP cross-VLAN)

> Un serveur DHCP ne couvre que son propre sous-réseau. Pour servir d'autres VLANs, il faut un **relais DHCP** sur le routeur.

```cisco
# Sur la sous-interface du VLAN à servir
Router(config)#interface GigabitEthernet0/0.20
Router(config-subif)#ip helper-address 192.168.1.5
```

Où `192.168.1.5` = IP du serveur DHCP.

---

## 🔗 Connexions inter-thèmes

> **DHCP ↔ WDS** → WDS a besoin de DHCP pour que les clients PXE obtiennent une IP avant le déploiement
> **DHCP ↔ VLAN** → un VLAN sans scope DHCP ou sans relais → APIPA
> **DHCP ↔ DNS** → l'option `domain-name-servers` distribue l'IP du DNS aux clients
> **DHCP Windows ↔ DHCP Linux** → même concept, deux implémentations. Windows = GUI + PowerShell ; Linux = `isc-dhcp-server` + fichier conf
> **IP statique ↔ DHCP** → l'inverse l'un de l'autre. Les serveurs ont une IP statique, les postes clients sont en DHCP. Compromise : réservation DHCP (IP fixe attribuée par MAC).
