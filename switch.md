# ⚙️Configuration du switch

--- 

## Annotations importantes

- **DHCP Snooping** : Mécanisme de sécurité qui filtre les messages DHCP non autorisés en identifiant les ports "de confiance" (trust) et en bloquant les réponses DHCP provenant de ports non autorisés.
- **Port Security** : Fonctionnalité qui limite le nombre d’adresses MAC autorisées sur un port et peut verrouiller le port en cas de violation.
- **MAC Address Sticky** : Permet d’apprendre dynamiquement les adresses MAC connectées et de les enregistrer dans la configuration du switch.
- **Violation Shutdown (sh)** : Action qui ferme le port en cas de violation de sécurité (trop d’adresses MAC ou MAC non autorisée).

---

### Sécurisation d'un switch 

## Activation du DHCP Snooping

```bash
enable
configure terminal
ip dhcp snooping
ip dhcp snooping vlan 1
ip dhcp snooping information option
```

- **Explications** : 

- ip dhcp snooping trust : Marque le port comme "de confiance" (ex : port connecté à un serveur DHCP légitime).

--- 

### Ports utilisateurs (untrusted)

```bash
interface range fastEthernet 0/1-24, gigabitEthernet 0/2
switchport mode access
switchport port-security maximum 4
switchport port-security mac-address sticky
switchport port-security violation shutdown
exit
```
- **Explications** :

- switchport mode access : Configure le port en mode accès (non trunk).
- switchport port-security maximum 4 : Limite à 4 adresses MAC par port.
- switchport port-security mac-address sticky : Enregistre automatiquement les adresses MAC apprises.
- switchport port-security violation shutdown : Désactive le port en cas de violation.

--- 

### Limitation du taux de requêtes DHCP

```bash
ip dhcp snooping limit rate 4
exit
```
- **Explications** :

- Limite le nombre de requêtes DHCP par seconde pour éviter les attaques par inondation.

--- 

## 3. Commandes de vérification

### Afficher les adresses MAC apprises

```bash
show mac address-table count
show mac address-table
show port-security
```
- **Explications** :

- show mac address-table count : Affiche le nombre d’entrées dans la table MAC.
- show mac address-table : Liste toutes les adresses MAC apprises par le switch.
- show port-security : Affiche l’état de la sécurité des ports.

--- 

# ⚙️Configuration basique

### Configuration des ports

```bash
interface gigabitEthernet 0/1
ip dhcp snooping limit rate 4
ip dhcp snooping trust
exit
```

--- 

### Réactiver un port désactivé

```bash
interface fastEthernet 0/1
shutdown
no shutdown
```

--- 

# Configurer le VTP

```bash
server(config)# vtp domain testVTP
```

- **Explication**

- vtp domain [nomdudomaine] : Configurer le domaine VTP qui permet à tous les commutateurs d’être dans le même “groupe”
