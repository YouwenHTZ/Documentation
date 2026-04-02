# Documentation Technique — Active Directory & Réseau Windows Server

> **Portée :** Utilisateurs · Groupes · GPO · Unités d'Organisation · Pools d'adresses IP (DHCP)

---

## Table des matières

1. [Gestion des Utilisateurs](#1-gestion-des-utilisateurs)
2. [Gestion des Groupes](#2-gestion-des-groupes)
3. [Stratégies de Groupe (GPO)](#3-stratégies-de-groupe-gpo)
4. [Unités d'Organisation (UO / OU)](#4-unités-dorganisation-uo--ou)
5. [Pools d'adresses IP (DHCP)](#5-pools-dadresses-ip-dhcp)

---

## 1. Gestion des Utilisateurs

Les comptes utilisateurs sont au cœur de l'Active Directory. Chaque compte représente une identité numérique permettant l'authentification et l'autorisation dans le domaine.

### 1.1 Créer un utilisateur (ADUC)

Active Directory Users and Computers est accessible via `dsa.msc`.

1. Ouvrir **ADUC** (`dsa.msc`)
2. Naviguer jusqu'à l'Unité d'Organisation souhaitée
3. Clic droit → **Nouveau** → **Utilisateur**
4. Renseigner : Prénom, Nom, Nom de connexion (UPN)
5. Définir un mot de passe et les options associées
6. Cliquer sur **Terminer**

### 1.2 Paramètres principaux

| Champ | Description |
|---|---|
| Prénom / Nom | Identité civile de l'utilisateur |
| Nom de connexion (UPN) | Identifiant de connexion au domaine (ex : `j.dupont@domaine.local`) |
| Mot de passe | Doit respecter la politique de complexité du domaine |
| L'utilisateur doit changer de mot de passe | Forcer le changement à la première connexion |
| Le mot de passe n'expire jamais | À réserver aux comptes de service uniquement |
| Le compte est désactivé | Désactiver sans supprimer le compte |

### 1.3 Opérations courantes

#### Modifier un utilisateur

1. Dans ADUC, double-cliquer sur l'utilisateur
2. Modifier les champs souhaités (onglets : Général, Organisation, Membre de…)
3. Cliquer sur **Appliquer** puis **OK**

#### Désactiver un compte

1. Clic droit sur l'utilisateur → **Désactiver le compte**
2. Une icône de flèche apparaît sur le compte pour indiquer qu'il est désactivé

#### Réinitialiser un mot de passe

1. Clic droit sur l'utilisateur → **Réinitialiser le mot de passe**
2. Saisir le nouveau mot de passe et le confirmer
3. Cocher **L'utilisateur doit changer de mot de passe à la prochaine ouverture de session** si nécessaire
4. Cliquer sur **OK**

#### Supprimer un compte

1. Clic droit sur l'utilisateur → **Supprimer**
2. Confirmer la suppression

> 📌 **Bonne pratique :** Appliquer toujours le principe du moindre privilège — n'accorder que les droits strictement nécessaires.

---

## 2. Gestion des Groupes

Les groupes permettent d'organiser les utilisateurs et de simplifier l'attribution de permissions. La stratégie **AGDLP** (Account → Global → Domain Local → Permission) est la bonne pratique Microsoft.

### 2.1 Types et portées de groupes

| Type | Portée | Utilisation recommandée |
|---|---|---|
| Sécurité | Domaine local | Attribuer des permissions sur des ressources locales |
| Sécurité | Global | Regrouper des utilisateurs du même domaine |
| Sécurité | Universel | Accès multi-domaines dans une forêt |
| Distribution | Domaine local | Listes de diffusion mail (Exchange) |

### 2.2 Créer un groupe

1. Dans ADUC, clic droit sur l'UO cible → **Nouveau** → **Groupe**
2. Saisir le nom du groupe
3. Choisir la portée : Domaine local / Global / Universel
4. Choisir le type : Sécurité ou Distribution
5. Valider

### 2.3 Gérer les membres

#### Ajouter un membre

1. Double-cliquer sur le groupe → onglet **Membres**
2. Cliquer sur **Ajouter**
3. Rechercher et sélectionner l'utilisateur ou le groupe
4. Cliquer sur **OK**

#### Supprimer un membre

1. Double-cliquer sur le groupe → onglet **Membres**
2. Sélectionner le membre à retirer
3. Cliquer sur **Supprimer** puis **OK**

#### Ajouter un groupe dans un autre (imbrication)

1. Double-cliquer sur le groupe parent → onglet **Membres**
2. Cliquer sur **Ajouter** et rechercher le groupe enfant
3. Valider

> 📌 **Stratégie AGDLP :** Placer les comptes dans des groupes **Globaux**, les groupes Globaux dans des groupes **Domaine Local**, puis attribuer les permissions aux groupes Domaine Local.

---

## 3. Stratégies de Groupe (GPO)

Les GPO (Group Policy Objects) permettent de centraliser la configuration des ordinateurs et des utilisateurs du domaine. Elles s'appliquent sur le Site, le Domaine ou les Unités d'Organisation.

### 3.1 Créer une GPO (GPMC)

La console GPMC est accessible via `gpmc.msc`.

1. Ouvrir **GPMC** (`gpmc.msc`)
2. Naviguer jusqu'au nœud **Objets de stratégie de groupe**
3. Clic droit → **Nouveau** → Nommer la GPO
4. Clic droit sur l'UO cible → **Lier un objet de stratégie de groupe existant**
5. Sélectionner la GPO créée et valider

### 3.2 Modifier les paramètres d'une GPO

1. Dans GPMC, clic droit sur la GPO → **Modifier**
2. L'éditeur de gestion des stratégies de groupe s'ouvre
3. Naviguer dans **Configuration ordinateur** ou **Configuration utilisateur**
4. Modifier les paramètres souhaités et fermer l'éditeur (la GPO est sauvegardée automatiquement)

### 3.3 Paramètres GPO courants

| Paramètre | Description |
|---|---|
| Politique de mot de passe | Longueur, complexité, durée de validité |
| Verrouillage de compte | Seuil et durée de verrouillage |
| Mappage de lecteurs réseau | Connecter automatiquement des partages |
| Déploiement de logiciels | Installer des MSI/MSP sur les postes |
| Configuration du pare-feu | Règles entrantes/sortantes Windows |
| Scripts d'ouverture/fermeture | Scripts Logon / Logoff / Startup / Shutdown |
| Paramètres Edge/IE | Page de démarrage, proxy, zones de sécurité |
| Restrictions applicatives (AppLocker) | Autoriser / Bloquer des exécutables |

### 3.4 Opérations courantes

#### Forcer l'application d'une GPO sur un poste client

1. Ouvrir une invite de commandes en tant qu'administrateur
2. Exécuter : `gpupdate /force`
3. Pour générer un rapport des GPO appliquées : `gpresult /r`

#### Activer / Désactiver une GPO

1. Dans GPMC, clic droit sur la GPO → **Statut de la GPO**
2. Choisir : Activé / Désactivé / Paramètres ordinateur désactivés / Paramètres utilisateur désactivés

#### Sauvegarder une GPO

1. Dans GPMC, clic droit sur la GPO → **Sauvegarder**
2. Choisir un dossier de destination et valider

#### Restaurer une GPO

1. Dans GPMC, clic droit sur **Objets de stratégie de groupe** → **Gérer les sauvegardes**
2. Sélectionner la sauvegarde et cliquer sur **Restaurer**

> 📌 **Bonne pratique :** Toujours tester une GPO sur une UO de test avant de la déployer en production. Utiliser le **Filtrage de sécurité** pour cibler un groupe précis.

---

## 4. Unités d'Organisation (UO / OU)

Les Unités d'Organisation (OU) sont des conteneurs Active Directory permettant d'organiser les objets (utilisateurs, groupes, ordinateurs) et de déléguer l'administration ou d'appliquer des GPO de façon ciblée.

### 4.1 Structure type recommandée

```
DC=domaine,DC=local
├── OU=Utilisateurs
│   ├── OU=Informatique
│   ├── OU=RH
│   └── OU=Direction
├── OU=Groupes
├── OU=Postes
│   ├── OU=Portables
│   └── OU=Fixes
├── OU=Serveurs
└── OU=Comptes_Service
```

### 4.2 Créer une UO

1. Dans ADUC, clic droit sur le domaine ou une UO parente → **Nouveau** → **Unité d'Organisation**
2. Saisir le nom de l'UO
3. Cocher **Protéger le conteneur contre une suppression accidentelle** (recommandé)
4. Valider

### 4.3 Déléguer le contrôle sur une UO

La délégation permet à un utilisateur ou groupe de gérer les objets d'une UO sans droits d'administration globaux.

1. Dans ADUC, clic droit sur l'UO → **Déléguer le contrôle**
2. Ajouter le ou les groupes / utilisateurs
3. Choisir les tâches à déléguer (ex : Réinitialisation des mots de passe)
4. Terminer l'assistant

### 4.4 Opérations courantes

#### Déplacer un objet vers une autre UO

1. Dans ADUC, clic droit sur l'objet → **Déplacer**
2. Sélectionner l'UO de destination et valider

#### Supprimer une UO

1. Dans ADUC, clic droit sur l'UO → **Propriétés** → onglet **Objet**
2. Décocher **Protéger l'objet contre une suppression accidentelle**
3. Valider, puis clic droit sur l'UO → **Supprimer**

> 📌 **Bonne pratique :** Structurer les UO en fonction de l'administration (GPO, délégation) plutôt que de l'organigramme. Éviter les hiérarchies trop profondes (> 5 niveaux).

---

## 5. Pools d'adresses IP (DHCP)

Le service DHCP (Dynamic Host Configuration Protocol) distribue automatiquement des adresses IP aux périphériques du réseau. Les étendues (scopes) définissent les plages d'adresses allouables.

### 5.1 Créer une étendue DHCP

1. Ouvrir la console **DHCP** (`dhcpmgmt.msc`)
2. Clic droit sur le serveur → **Nouvelle étendue**
3. Nommer l'étendue (ex : `LAN_RH_192.168.10.x`)
4. Définir la plage : IP de début et IP de fin
5. Définir le masque de sous-réseau
6. Ajouter les exclusions (adresses statiques réservées)
7. Définir la durée du bail (8 h postes / 24 h serveurs recommandé)
8. Configurer les options : passerelle, DNS, domaine
9. Activer l'étendue

### 5.2 Options DHCP courantes

| Code | Option | Exemple de valeur |
|:---:|---|---|
| 003 | Routeur (passerelle) | `192.168.1.254` |
| 006 | Serveurs DNS | `192.168.1.10, 8.8.8.8` |
| 015 | Nom de domaine DNS | `domaine.local` |
| 044 | Serveurs WINS | `192.168.1.15` |
| 051 | Durée du bail (secondes) | `86400` (24 h) |
| 066 | Serveur TFTP (PXE) | `192.168.1.20` |
| 067 | Nom du fichier de boot | `pxelinux.0` |

### 5.3 Configurer les options d'une étendue

1. Dans la console DHCP, déplier l'étendue → clic droit sur **Options d'étendue** → **Configurer les options**
2. Cocher l'option souhaitée (ex : 003 Routeur)
3. Saisir la valeur et cliquer sur **Ajouter**
4. Répéter pour chaque option puis valider

### 5.4 Réservations d'adresses

Une réservation attribue toujours la même adresse IP à un équipement via son adresse MAC.

1. Dans la console DHCP, déplier l'étendue → clic droit sur **Réservations** → **Nouvelle réservation**
2. Saisir un nom, l'adresse IP souhaitée et l'adresse MAC du client
3. Choisir le type : DHCP / BOOTP / Les deux
4. Cliquer sur **Ajouter**

### 5.5 Opérations courantes

#### Consulter les baux actifs

1. Dans la console DHCP, déplier l'étendue → cliquer sur **Baux d'adresses**
2. La liste affiche les IP attribuées, les noms de machines et les dates d'expiration

#### Sauvegarder la configuration DHCP

1. Dans la console DHCP, clic droit sur le serveur → **Sauvegarder**
2. Choisir le dossier de destination (par défaut : `C:\Windows\System32\dhcp\backup`)

#### Activer la redondance DHCP (Failover)

1. Dans la console DHCP, clic droit sur l'étendue → **Configurer le basculement**
2. Saisir le nom du serveur partenaire
3. Choisir le mode : **Équilibrage de charge** ou **Serveur de secours (Hot Standby)**
4. Définir une clé secrète partagée et valider

> 📌 **Bonne pratique :** Toujours sauvegarder la configuration DHCP avant toute modification. Activer le **Failover DHCP** pour la haute disponibilité en environnement de production.

---

*Documentation générée pour Windows Server — Active Directory Domain Services & DHCP Server*
