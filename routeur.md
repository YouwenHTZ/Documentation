## Accès & Modes

```bash
Router>           # Mode utilisateur (limité)
Router> enable    # Passer en mode privilégié
Router#           # Mode privilégié

Router# configure terminal   # Entrer en mode configuration globale
Router(config)#              # Mode configuration globale

Router(config)# exit         # Retourner au mode précédent
Router(config)# end          # Retourner directement au mode privilégié
Router# disable              # Revenir au mode utilisateur
```

---

## Nom d'hôte & Bannière

```bash
Router(config)# hostname R1

Router(config)# banner motd #
  Accès autorisé uniquement !
#
```

---

## Sécurisation des accès

```bash
# Mot de passe enable (mode privilégié) - chiffré
Router(config)# enable secret MonMotDePasse

# Chiffrer tous les mots de passe en clair
Router(config)# service password-encryption

# Console locale
Router(config)# line console 0
Router(config-line)# password MonMdpConsole
Router(config-line)# login
Router(config-line)# exec-timeout 5 0   # Déconnexion après 5 min d'inactivité
Router(config-line)# exit

# Accès SSH (lignes VTY)
Router(config)# line vty 0 4
Router(config-line)# transport input ssh
Router(config-line)# login local
Router(config-line)# exec-timeout 5 0
Router(config-line)# exit
```

---

## Compte local & SSH

```bash
# Créer un utilisateur local
Router(config)# username admin privilege 15 secret MonMdpAdmin

# Configurer SSH (obligatoire : nom de domaine + clé RSA)
Router(config)# ip domain-name monreseau.local
Router(config)# crypto key generate rsa modulus 2048
Router(config)# ip ssh version 2
Router(config)# ip ssh time-out 60
Router(config)# ip ssh authentication-retries 3
```

---

## Configuration des interfaces

```bash
# Interface LAN (vers le réseau interne)
Router(config)# interface GigabitEthernet 0/0
Router(config-if)# description LAN - Reseau interne
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit

# Interface WAN (vers Internet / FAI)
Router(config)# interface GigabitEthernet 0/1
Router(config-if)# description WAN - Connexion Internet
Router(config-if)# ip address 203.0.113.2 255.255.255.252
Router(config-if)# no shutdown
Router(config-if)# exit

# Interface Loopback (utile pour la gestion et le routage)
Router(config)# interface loopback 0
Router(config-if)# ip address 1.1.1.1 255.255.255.255
Router(config-if)# exit
```

---

## Sous-interfaces (Router-on-a-Stick pour VLANs)

```bash
# Activer l'interface physique sans IP
Router(config)# interface GigabitEthernet 0/0
Router(config-if)# no ip address
Router(config-if)# no shutdown
Router(config-if)# exit

# Sous-interface VLAN 10
Router(config)# interface GigabitEthernet 0/0.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0
Router(config-subif)# exit

# Sous-interface VLAN 20
Router(config)# interface GigabitEthernet 0/0.20
Router(config-subif)# encapsulation dot1Q 20
Router(config-subif)# ip address 192.168.20.1 255.255.255.0
Router(config-subif)# exit

# Sous-interface VLAN 99 (Management) - VLAN natif
Router(config)# interface GigabitEthernet 0/0.99
Router(config-subif)# encapsulation dot1Q 99 native
Router(config-subif)# ip address 192.168.99.1 255.255.255.0
Router(config-subif)# exit
```

---

## Routage statique

```bash
# Route statique simple
Router(config)# ip route 10.0.0.0 255.255.255.0 192.168.1.254

# Route statique avec interface de sortie
Router(config)# ip route 10.0.0.0 255.255.255.0 GigabitEthernet 0/1

# Route par défaut (vers Internet)
Router(config)# ip route 0.0.0.0 0.0.0.0 203.0.113.1

# Route flottante (backup, distance administrative plus haute)
Router(config)# ip route 0.0.0.0 0.0.0.0 203.0.113.5 10
```

---

## Routage dynamique — OSPF

```bash
Router(config)# router ospf 1
Router(config-router)# router-id 1.1.1.1
Router(config-router)# network 192.168.1.0 0.0.0.255 area 0
Router(config-router)# network 192.168.10.0 0.0.0.255 area 0
Router(config-router)# network 192.168.20.0 0.0.0.255 area 0
Router(config-router)# default-information originate    # Redistribuer la route par défaut
Router(config-router)# passive-interface GigabitEthernet 0/0   # Ne pas envoyer OSPF sur LAN
Router(config-router)# exit
```

---

## Routage dynamique — EIGRP

```bash
Router(config)# router eigrp 100
Router(config-router)# eigrp router-id 1.1.1.1
Router(config-router)# network 192.168.1.0 0.0.0.255
Router(config-router)# network 192.168.10.0 0.0.0.255
Router(config-router)# no auto-summary
Router(config-router)# passive-interface GigabitEthernet 0/0
Router(config-router)# exit
```

---


## Routage dynamique - RIP

```bash
# Activer RIP
Router(config)# router rip
Router(config-router)# version 2
Router(config-router)# no auto-summary
```

# Annoncer les réseaux
```bash
Router(config-router)# network 192.168.1.0
Router(config-router)# network 10.0.12.0
```

# Passive interface (interfaces vers les PCs)
```bash
Router(config-router)# passive-interface GigabitEthernet 0/0
```

# Propager la route par défaut (routeur connecté à Internet)
```bash
Router(config-router)# default-information originate
Router(config-router)# exit
```

# Route par défaut vers le FAI
```bash
Router(config)# ip route 0.0.0.0 0.0.0.0 203.0.113.1
```

# Authentification MD5
```bash
Router(config)# key chain CLE_RIP
Router(config-keychain)# key 1
Router(config-keychain-key)# key-string MonMotDePasse
Router(config-keychain-key)# exit
Router(config-keychain)# exit
Router(config)# interface GigabitEthernet 0/1
Router(config-if)# ip rip authentication mode md5
Router(config-if)# ip rip authentication key-chain CLE_RIP
Router(config-if)# exit
```

# Vérification
```bash
Router# show ip route rip
Router# show ip protocols
Router# show ip rip database
```

---

## NAT / PAT (Accès Internet)

```bash
# Définir les interfaces inside/outside
Router(config)# interface GigabitEthernet 0/0
Router(config-if)# ip nat inside
Router(config-if)# exit

Router(config)# interface GigabitEthernet 0/1
Router(config-if)# ip nat outside
Router(config-if)# exit

# ACL pour définir les hôtes à translater
Router(config)# access-list 1 permit 192.168.0.0 0.0.255.255

# PAT (overload) — tous les hôtes LAN partagent l'IP WAN
Router(config)# ip nat inside source list 1 interface GigabitEthernet 0/1 overload

# NAT statique (redirection de port / serveur DMZ)
Router(config)# ip nat inside source static 192.168.1.100 203.0.113.2
```

---

## Access Control Lists (ACL)

```bash
# ACL standard (filtre par IP source uniquement)
Router(config)# access-list 10 permit 192.168.1.0 0.0.0.255
Router(config)# access-list 10 deny any

# Appliquer l'ACL sur une interface
Router(config)# interface GigabitEthernet 0/1
Router(config-if)# ip access-group 10 in
Router(config-if)# exit

# ACL étendue (filtre source, destination, protocole, port)
Router(config)# ip access-list extended BLOCAGE_HTTP
Router(config-ext-nacl)# deny tcp 192.168.1.0 0.0.0.255 any eq 80
Router(config-ext-nacl)# permit ip any any
Router(config-ext-nacl)# exit

Router(config)# interface GigabitEthernet 0/0
Router(config-if)# ip access-group BLOCAGE_HTTP in
Router(config-if)# exit
```

---

## Serveur DHCP

```bash
# Exclure des adresses (passerelle, serveurs, imprimantes...)
Router(config)# ip dhcp excluded-address 192.168.1.1 192.168.1.20

# Créer un pool DHCP
Router(config)# ip dhcp pool LAN
Router(dhcp-config)# network 192.168.1.0 255.255.255.0
Router(dhcp-config)# default-router 192.168.1.1
Router(dhcp-config)# dns-server 8.8.8.8 8.8.4.4
Router(dhcp-config)# lease 7              # Durée du bail en jours
Router(dhcp-config)# exit

# DHCP Relay (si le serveur DHCP est distant)
Router(config)# interface GigabitEthernet 0/0
Router(config-if)# ip helper-address 192.168.1.50
Router(config-if)# exit
```

---


## Sauvegarde & Vérification

```bash
# Sauvegarder la configuration en cours
Router# copy running-config startup-config
# ou
Router# write memory

# Vérifications essentielles
Router# show running-config
Router# show startup-config
Router# show version
Router# show interfaces
Router# show interfaces GigabitEthernet 0/0
Router# show ip interface brief
Router# show ip route
Router# show ip nat translations
Router# show ip nat statistics
Router# show access-lists
Router# show ip dhcp pool
Router# show ip dhcp binding
Router# show ip ssh

```

---

## Réinitialisation complète (si nécessaire)

```bash
Router# write erase      # Efface la startup-config
Router# reload           # Redémarre le routeur
```

---
