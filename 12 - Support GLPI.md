# 🎫 Support & GLPI

## 📚 ITIL

> **ITIL** = Information Technology Infrastructure Library
> Ensemble de bonnes pratiques pour la **gestion des services IT**.
> Ce n'est pas un logiciel, c'est un **framework** (cadre de travail).

---

## 🔄 Cycle de vie d'un ticket d'incident

```
Créé → Affecté → En cours → Résolu → Clos → Archivé
```

| Statut | Signification |
|--------|--------------|
| **Créé** | Ticket ouvert par l'utilisateur ou le technicien |
| **Affecté** | Assigné à un technicien ou une équipe |
| **En cours** | Technicien travaille sur l'incident |
| **Résolu** | Solution appliquée, en attente validation user |
| **Clos** | Utilisateur a confirmé la résolution |
| **Archivé** | Ticket conservé pour historique |

---

## ⏱️ GTR & GTI

| Sigle | Signification | Définition |
|-------|--------------|-----------|
| **GTI** | Garantie de Temps d'Intervention | Délai max pour qu'un technicien **commence** à traiter l'incident |
| **GTR** | Garantie de Temps de Rétablissement | Délai max pour que le service soit **rétabli** |

> **Mémo :** GTI = Intervention (débuter) | GTR = Rétablissement (finir)

---

## 🏗️ Niveaux de support

| Niveau | Rôle |
|--------|------|
| **N1** | Helpdesk — prise d'appel, incidents simples, réinitialisation MDP |
| **N2** | Technicien — incidents complexes, configuration, diagnostic |
| **N3** | Expert / éditeur — bugs logiciels, escalade constructeur |

---

## 💻 GLPI — Actions courantes

| Action | Où |
|--------|-----|
| Créer un ticket | Assistance > Créer un ticket |
| Voir mes tickets | Assistance > Tickets |
| Affecter un ticket | Ticket > Assigné à > Technicien |
| Changer le statut | Ticket > Statut |
| Ajouter un suivi | Ticket > Ajouter un suivi |
| Résoudre | Ticket > Solution > Décrire la résolution |

---

## 🔗 Connexions inter-thèmes

> **GLPI tourne sur Stack LAMP** → si Apache ou MariaDB tombe, GLPI est inaccessible → panne type Phase 2
> **GTR/GTI ↔ PRA/PCA** → les SLA (niveaux de service) définissent les GTR/GTI
> **ITIL ↔ Méthode diagnostic** → ITIL préconise une démarche structurée = exactement ce que le jury attend
