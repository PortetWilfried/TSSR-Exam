# 📞 Téléphonie IP — FreePBX & SIP

## 📑 Sommaire

- [[#🧠 Concepts clés|🧠 Concepts clés]]
- [[#🛠️ Créer un utilisateur FreePBX|🛠️ Créer un utilisateur FreePBX]]
- [[#📱 Configurer un Softphone (Zoiper / Linphone / MicroSIP)|📱 Configurer un Softphone]]
- [[#📞 Test d'appel|📞 Test d'appel]]
- [[#🔧 Dépannage FreePBX|🔧 Dépannage FreePBX]]
- [[#🔗 Connexions inter-thèmes|🔗 Connexions inter-thèmes]]

---

## 🧠 Concepts clés

| Terme | Définition |
|-------|-----------|
| **VoIP** | Voice over IP — téléphonie via le réseau IP |
| **SIP** | Session Initiation Protocol — protocole qui gère les appels (établissement, fin) |
| **FreePBX** | IPBX open source basé sur Asterisk — gère les extensions, les appels |
| **Extension** | Numéro interne (ex: 101, 102) |
| **Softphone** | Logiciel qui émule un téléphone (ex: Zoiper, Linphone, MicroSIP) |
| **Trunk SIP** | Lien vers l'extérieur (opérateur) |

**Ports SIP :** 5060 (non chiffré) | 5061 (SIPS, chiffré)

---

## 🛠️ Créer un utilisateur FreePBX

1. Interface web FreePBX → **Applications > Extensions**
2. **Add Extension** → Type : SIP (chan_pjsip)
3. Remplir :
   - **User Extension :** 101
   - **Display Name :** Alice Martin
   - **Secret (mot de passe) :** mot de passe SIP
4. **Submit** puis **Apply Config** (bouton rouge en haut)
5. **Screenshot après Apply Config**

---

## 📱 Configurer un Softphone (Zoiper / Linphone / MicroSIP)

1. Installer le softphone sur le poste client
2. Ajouter un compte SIP :
   - **Username / User :** 101 (le numéro d'extension)
   - **Password :** le Secret configuré dans FreePBX
   - **Server / Domain :** IP du serveur FreePBX (ex: 192.168.1.20)
   - **Port :** 5060
3. Valider → statut doit passer à **Registered / Connecté**
4. **Screenshot du statut "Registered"**

---

## 📞 Test d'appel

1. S'assurer que les **deux extensions** sont enregistrées (Registered)
2. Sur l'extension 101 → composer **102**
3. L'extension 102 sonne → décrocher
4. Vérifier que la communication s'établit
5. **Screenshot de l'appel en cours**

---

## 🔧 Dépannage FreePBX

| Problème | Vérification |
|---------|-------------|
| Softphone "Unregistered" | IP serveur correcte ? Port 5060 ouvert ? Identifiants exacts ? |
| Pas de sonnerie | Extension destinataire bien enregistrée ? |
| Pas de son | Codec compatible ? NAT configuré ? |
| Apply Config oublié | **Toujours cliquer Apply Config** après chaque modif FreePBX |

---

## 🔗 Connexions inter-thèmes

> **SIP port 5060 ↔ tableau des ports réseau** → à connaître comme SSH=22, HTTP=80
> **FreePBX ↔ DHCP** → les softphones ont besoin d'une IP pour joindre le serveur FreePBX
> **VoIP ↔ VLAN** → en entreprise, on isole la voix dans un VLAN dédié (QoS)
