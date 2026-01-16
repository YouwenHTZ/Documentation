# Documentation : Sécurisation d'un switch contre les attaques DHCP et MAC

---

## 1. Introduction aux concepts clés

### DHCP Snooping
**Définition** : Mécanisme de sécurité qui filtre les messages DHCP non autorisés sur un réseau. Il permet de prévenir les attaques comme le **DHCP Spoofing** (usurpation de serveur DHCP).
**Fonctionnement** : Le switch vérifie que les réponses DHCP proviennent bien d’un port de confiance (`trust`).

### Port Security
**Définition** : Limite le nombre d’adresses MAC autorisées sur un port et permet de bloquer les attaques comme le **MAC Flooding** (saturation de la table MAC).
**Options** :
- `maximum 4` : Limite à 4 adresses MAC par port.
- `mac-address sticky` : Apprend automatiquement les adresses MAC connectées.
- `violation shutdown` : Désactive le port en cas de violation.

### MAC Address Table
**Définition** : Table interne du switch qui associe les adresses MAC aux ports physiques.
**Commandes de visualisation** :
- `show mac address-table count` : Affiche le nombre d’entrées.
- `show mac address-table` : Liste toutes les entrées.

---

## 2. Configuration du switch

### Activation du DHCP Snooping
```bash
enable
configure terminal
ip dhcp snooping
ip dhcp snooping vlan 1
ip dhcp snooping information option
