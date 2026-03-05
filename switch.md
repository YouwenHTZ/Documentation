# ⚙️Configuration du switch

--- 

## Annotations importantes

- **DHCP Snooping** : Mécanisme de sécurité qui filtre les messages DHCP non autorisés en identifiant les ports "de confiance" (trust) et en bloquant les réponses DHCP provenant de ports non autorisés.
- **Port Security** : Fonctionnalité qui limite le nombre d’adresses MAC autorisées sur un port et peut verrouiller le port en cas de violation.
- **MAC Address Sticky** : Permet d’apprendre dynamiquement les adresses MAC connectées et de les enregistrer dans la configuration du switch.
- **Violation Shutdown (sh)** : Action qui ferme le port en cas de violation de sécurité (trop d’adresses MAC ou MAC non autorisée).

---

### Sécurisation d'un switch 

## Sécurisation des accès

### Mot de passe enable (mode privilégié) - chiffré
```bash
Switch(config)# enable secret MonMotDePasse
```

### Chiffrer tous les mots de passe en clair

```bash
Switch(config)# service password-encryption
```

### Console locale

```bash
Switch(config)# line console 0
Switch(config-line)# password MonMdpConsole
Switch(config-line)# login
Switch(config-line)# exec-timeout 5 0   # Déconnexion après 5 min d'inactivité
Switch(config-line)# exit
```

### Accès SSH (lignes VTY)

```bash
Switch(config)# line vty 0 15
Switch(config-line)# transport input ssh
Switch(config-line)# login local
Switch(config-line)# exec-timeout 5 0
Switch(config-line)# exit
```

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

## Accès & Modes

```bash
Switch>          # Mode utilisateur (limité)
Switch> enable   # Passer en mode privilégié
Switch#          # Mode privilégié

Switch# configure terminal   # Entrer en mode configuration globale
Switch(config)#              # Mode configuration globale

Switch(config)# exit         # Retourner au mode précédent
Switch(config)# end          # Retourner directement au mode privilégié
Switch# disable              # Revenir au mode utilisateur
```

---

## Nom d'hôte & Bannière

```bash
Switch(config)# hostname SW1

Switch(config)# banner motd #
  Accès autorisé uniquement !
#
```

---

## Sécurisation des accès

```bash
# Mot de passe enable (mode privilégié) - chiffré
Switch(config)# enable secret MonMotDePasse

# Chiffrer tous les mots de passe en clair
Switch(config)# service password-encryption

# Console locale
Switch(config)# line console 0
Switch(config-line)# password MonMdpConsole
Switch(config-line)# login
Switch(config-line)# exec-timeout 5 0   # Déconnexion après 5 min d'inactivité
Switch(config-line)# exit

# Accès SSH (lignes VTY)
Switch(config)# line vty 0 15
Switch(config-line)# transport input ssh
Switch(config-line)# login local
Switch(config-line)# exec-timeout 5 0
Switch(config-line)# exit
```

---

## Compte local & SSH

```bash
# Créer un utilisateur local
Switch(config)# username admin privilege 15 secret MonMdpAdmin

# Configurer SSH (obligatoire : nom de domaine + clé RSA)
Switch(config)# ip domain-name monreseau.local
Switch(config)# crypto key generate rsa modulus 2048
Switch(config)# ip ssh version 2
Switch(config)# ip ssh time-out 60
Switch(config)# ip ssh authentication-retries 3
```

---

## Interface de gestion (VLAN 1 ou VLAN dédié)

```bash
# Adresse IP sur le VLAN de gestion
Switch(config)# interface vlan 1
Switch(config-if)# ip address 192.168.1.10 255.255.255.0
Switch(config-if)# no shutdown
Switch(config-if)# exit

# Passerelle par défaut
Switch(config)# ip default-gateway 192.168.1.1
```

---

## Configuration des VLANs

```bash
# Créer des VLANs
Switch(config)# vlan 10
Switch(config-vlan)# name Serveurs
Switch(config-vlan)# exit

Switch(config)# vlan 20
Switch(config-vlan)# name Bureaux
Switch(config-vlan)# exit

Switch(config)# vlan 99
Switch(config-vlan)# name Management
Switch(config-vlan)# exit
```

---

## Configuration des ports Access

```bash
Switch(config)# interface FastEthernet 0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
Switch(config-if)# spanning-tree portfast       # Pour ports end-device
Switch(config-if)# spanning-tree bpduguard enable
Switch(config-if)# no shutdown
Switch(config-if)# exit

# Appliquer sur une plage de ports
Switch(config)# interface range FastEthernet 0/1 - 12
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 20
Switch(config-if-range)# spanning-tree portfast
Switch(config-if-range)# no shutdown
Switch(config-if-range)# exit
```

---

## Configuration des ports Trunk (vers routeur ou autre switch)

```bash
Switch(config)# interface GigabitEthernet 0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk native vlan 99       # VLAN natif (sécurité)
Switch(config-if)# switchport trunk allowed vlan 10,20,99
Switch(config-if)# no shutdown
Switch(config-if)# exit
```

---

## Sauvegarde & Vérification

```bash
# Sauvegarder la configuration en cours
Switch# copy running-config startup-config
# ou
Switch# write memory

# Vérifications essentielles
Switch# show running-config
Switch# show startup-config
Switch# show version
Switch# show interfaces status
Switch# show vlan brief
Switch# show interfaces trunk
Switch# show mac address-table
Switch# show spanning-tree
Switch# show port-security
Switch# show ip ssh
Switch# show cdp neighbors
Switch# show etherchannel summary
Switch# show logging
```

---

## Réinitialisation complète (si nécessaire)

```bash
Switch# write erase          # Efface la startup-config
Switch# delete vlan.dat      # Supprime la base VLAN
Switch# reload               # Redémarre le switch
```

---