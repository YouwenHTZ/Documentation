# Documentation générale

---

# Sécurisation d'un switch contre les attaques DHCP et MAC

--- 

## 1. Introduction aux concepts clés

### Annotations importantes

- **DHCP Snooping** : Mécanisme de sécurité qui filtre les messages DHCP non autorisés en identifiant les ports "de confiance" (trust) et en bloquant les réponses DHCP provenant de ports non autorisés.
- **Port Security** : Fonctionnalité qui limite le nombre d’adresses MAC autorisées sur un port et peut verrouiller le port en cas de violation.
- **MAC Address Sticky** : Permet d’apprendre dynamiquement les adresses MAC connectées et de les enregistrer dans la configuration du switch.
- **Violation Shutdown (sh)** : Action qui ferme le port en cas de violation de sécurité (trop d’adresses MAC ou MAC non autorisée).
- **DHCP Starvation** : Attaque consistant à épuiser le pool d’adresses IP d’un serveur DHCP en envoyant massivement des requêtes.
- **MAC Flooding** : Attaque consistant à saturer la table CAM du switch avec de fausses adresses MAC, forçant le switch à inonder le réseau avec du trafic.

---

## 2. Configuration du switch


### Configuration des ports

```bash
interface gigabitEthernet 0/1
ip dhcp snooping limit rate 4
ip dhcp snooping trust
exit
```
 
## 3. Gestion des violations de port

### Réactiver un port désactivé

```bash
interface fastEthernet 0/1
shutdown
no shutdown
```

--- 

## 5. Simulation d'attaques (Kali Linux)

### Attaque DHCP Starvasion

```bash
sudo yersinia dhcp -attack 1 -interface eth0
sudo tcpdump -i eth0 port 67 or port 68
```

- **Explications**

- yersinia dhcp -attack 1 : Envoie des requêtes DHCP pour épuiser le pool d’adresses.
- tcpdump : Capture le trafic DHCP pour analyse.

### Attaque MAC Flooding 

- **Explications**

- Utilisez des outils comme macof (inclus dans dsniff) pour inonder le switch d’adresses MAC.

### Surveillance du serveur DHCP (Linux)

```bash
journalctl -u isc-dhcp-server -f
systemctl restart isc-dhcp-server
```

- **Explications**

- journalctl : Affiche les logs du serveur DHCP en temps réel.
- systemctl restart : Redémarre le service DHCP.

--- 

