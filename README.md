# Sécurisation Switch : Anti-DHCP Spoofing & MAC Flooding

```bash
# =============================================
# 1. ACTIVATION DU DHCP SNOOPING
#    - ip dhcp snooping : Active la surveillance DHCP
#    - vlan 1 : Applique au VLAN 1
#    - information option : Ajoute des infos de traçage
# =============================================
enable
configure terminal
ip dhcp snooping
ip dhcp snooping vlan 1
ip dhcp snooping information option

# =============================================
# 2. CONFIGURATION DU PORT DE CONFIANCE (trust)
#    - gig0/1 : Port connecté au serveur DHCP légitime
#    - trust : Autorise les réponses DHCP sur ce port
# =============================================
interface gigabitEthernet 0/1
 ip dhcp snooping trust
 exit

# =============================================
# 3. SÉCURISATION DES PORTS D'ACCÈS
#    - mode access : Port en mode accès (pas de trunk)
#    - port-security maximum 4 : Limite à 4 MAC par port
#    - mac-address sticky : Apprend et sauvegarde les MAC
#    - violation shutdown : Désactive le port en cas d'abus
# =============================================
interface range fastEthernet 0/1-24, gigabitEthernet 0/2
 switchport mode access
 switchport port-security maximum 4
 switchport port-security mac-address sticky
 switchport port-security violation shutdown
 exit

# =============================================
# 4. LIMITATION DU TAUX DE REQUÊTES DHCP
#    - limit rate 4 : Limite à 4 requêtes DHCP/seconde
# =============================================
ip dhcp snooping limit rate 4
exit

# =============================================
# 5. GESTION DES VIOLATIONS DE PORT
#    - shutdown/no shutdown : Réactive un port désactivé
# =============================================
interface fastEthernet 0/1
 shutdown
 no shutdown

# =============================================
# 6. VISUALISATION DES ADRESSES MAC
#    - mac address-table : Affiche la table MAC
#    - port-security : Affiche l'état de la sécurité des ports
# =============================================
show mac address-table count
show mac address-table
show port-security

# =============================================
# 7. SIMULATION D'ATTAQUES (Kali Linux)
#    - yersinia : Outil d'attaque DHCP (starvation)
#    - tcpdump : Capture le trafic DHCP (ports 67/68)
# =============================================
sudo yersinia dhcp -attack 1 -interface eth0
sudo tcpdump -i eth0 port 67 or port 68

# =============================================
# 8. JOURNALISATION & REDÉMARRAGE SERVEUR DHCP (Linux)
#    - journalctl : Affiche les logs du serveur DHCP
#    - systemctl : Redémarre le service DHCP
# =============================================
journalctl -u isc-dhcp-server -f
systemctl restart isc-dhcp-server
```
