# 🔍 Phase 2 — Diagnostic de Panne

## 📑 Sommaire

- [[#🧠 La méthode (le jury note ÇA en priorité)|🧠 La méthode (le jury note ÇA en priorité)]]
- [[#🚨 Panne type 1 — GLPI inaccessible depuis un poste client|🚨 Panne type 1 — GLPI inaccessible]]
- [[#🚨 Panne type 2 — WiFi inaccessible|🚨 Panne type 2 — WiFi inaccessible]]
- [[#🚨 Panne type 3 — Utilisateur ne peut pas se connecter au domaine|🚨 Panne type 3 — Connexion domaine impossible]]
- [[#❓ Questions jury fréquentes (Phase 2)|❓ Questions jury fréquentes (Phase 2)]]
- [[#💬 Phrases clés pour le jury|💬 Phrases clés pour le jury]]
- [[#🔗 Connexions inter-thèmes|🔗 Connexions inter-thèmes]]

---

## 🧠 La méthode (le jury note ÇA en priorité)

```
1. CONSTAT     → "Je constate que..."
2. HYPOTHÈSES  → "Les causes possibles sont..."
3. TESTS       → "Je vais vérifier... en faisant..."
4. RÉSOLUTION  → "J'applique la correction..."
5. VALIDATION  → "Je confirme avec vous que c'est résolu"
```

> Le jury joue l'utilisateur/responsable. **Communique à voix haute** à chaque étape.
> **"Je vais faire X pour vérifier Y"** — ne jamais agir en silence.

---

## 🚨 Panne type 1 — GLPI inaccessible depuis un poste client

### Méthode
```
Constat : L'URL GLPI ne répond pas dans le navigateur

Hypothèses :
├── Réseau : poste sans IP / mauvaise passerelle / DNS KO
├── Serveur : Apache arrêté / MariaDB arrêté / serveur éteint
└── GLPI : erreur config / base de données inaccessible

Tests (ordre logique) :
1. ping 8.8.8.8           → réseau OK ?
2. ping IP_serveur_GLPI   → serveur joignable ?
3. ping nom_serveur        → DNS OK ?
4. curl http://IP_serveur  → Apache répond ?
5. Sur serveur : systemctl status apache2
6. Sur serveur : systemctl status mariadb
7. Sur serveur : tail -f /var/log/apache2/error.log

Résolutions courantes :
- Apache arrêté → systemctl start apache2
- MariaDB arrêté → systemctl start mariadb
- Poste sans IP → vérifier DHCP, renouveler (ipconfig /renew)
- DNS KO → vérifier /etc/resolv.conf ou DNS Windows
```

---

## 🚨 Panne type 2 — WiFi inaccessible

### Méthode
```
Constat : Impossible de se connecter au WiFi

Hypothèses :
├── Poste : WiFi désactivé / mauvais SSID ou mdp
├── Borne : borne éteinte / mauvaise config
├── Réseau : VLAN WiFi sans DHCP / ACL bloquante
└── Authentification : 802.1X / certificat expiré

Tests :
1. WiFi activé sur le poste ? (icône Windows)
2. Le SSID apparaît dans la liste ?
3. Connexion avec les bons identifiants ?
4. La borne est allumée ? (LED)
5. Un autre poste arrive à se connecter ?
6. IP obtenue après connexion ? (ipconfig)
7. ping passerelle ?

Résolutions courantes :
- Oublier le réseau + reconnecter
- Vérifier DHCP scope du VLAN WiFi
- Redémarrer la borne
```

---

## 🚨 Panne type 3 — Utilisateur ne peut pas se connecter au domaine

### Méthode
```
Constat : Erreur "impossible de contacter le contrôleur de domaine"

Hypothèses :
├── Réseau : pas d'IP / mauvais DNS
├── AD : compte désactivé / verrouillé / expiré
├── DC : service AD arrêté / DC éteint
└── Jonction : poste sorti du domaine

Tests :
1. ipconfig /all → IP et DNS configurés ?
2. ping DC → DC joignable ?
3. nslookup tvs.lan → DNS résout le domaine ?
4. Sur DC : dsa.msc → compte actif ?
5. Sur DC : eventvwr → erreurs récentes ?

Résolutions :
- Compte verrouillé → dsa.msc → déverrouiller
- Compte expiré → modifier la date d'expiration
- DNS KO → pointer vers l'IP du DC
```

---

## ❓ Questions jury fréquentes (Phase 2)

**Théorie :**
- "Citez les 7 couches OSI" → Application, Présentation, Session, Transport, Réseau, Liaison, Physique
- "Différence GTR et GTI ?" → GTI = délai d'intervention ; GTR = délai de rétablissement
- "C'est quoi ITIL ?" → Framework de bonnes pratiques pour la gestion des services IT
- "Définissez la règle 3-2-1" → 3 copies, 2 supports différents, 1 hors site
- "Hyperviseur type 1 vs type 2 ?" → Type 1 = bare metal (ESXi) ; Type 2 = hosted (VMware Workstation)

**En anglais (au moins 1 question garantie) :**
- "What are the differences between a type 1 and type 2 hypervisor?"
  → "A type 1 hypervisor runs directly on the hardware. A type 2 runs on top of an existing OS."
- "What is SSH and on which port does it run?"
  → "SSH is a secure remote access protocol. It runs on port 22."

---

## 💬 Phrases clés pour le jury

| Moment | Ce que tu dis |
|--------|--------------|
| Début | "Je prends connaissance de l'incident. Je constate que [X] ne fonctionne pas." |
| Analyse | "Les causes possibles sont [A], [B] ou [C]. Je vais commencer par vérifier [A] car c'est le plus probable." |
| Test | "Je fais un ping pour vérifier la connectivité réseau..." |
| Résolution | "J'ai identifié le problème : [X]. Je vais appliquer [correction]." |
| Validation | "Le service est rétabli. Pouvez-vous confirmer que tout fonctionne de votre côté ?" |

---

## 🔗 Connexions inter-thèmes

> **Diagnostic OSI couche par couche** → commence toujours par le bas (physique → réseau → application)
> **GLPI ↔ Stack LAMP** → GLPI tourne sur Apache + MariaDB + PHP
> **WiFi KO ↔ VLAN + DHCP** → souvent le VLAN WiFi n'a pas de scope DHCP
