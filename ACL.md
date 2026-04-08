# Documentation : Commandes ACL sur les routeurs

---

## 1. Créer une ACL standard

**Commande :**
```
access-list [numéro] permit [adresse_ip] [wildcard]
```

**Explication :**
- `access-list` → commande pour créer une ACL
- `[numéro]` → numéro de l'ACL (entre 1 et 99 pour standard)
- `permit` → autorise le trafic
- `[adresse_ip]` → adresse IP autorisée
- `[wildcard]` → masque générique (wildcard)

**Exemple :**
```
access-list 10 permit 192.168.1.0 0.0.0.255
```

- Crée une ACL numéro 10
- Autorise le réseau 192.168.1.0/24

---

## 2. Bloquer une adresse IP (ACL standard)

**Commande :**
```
access-list [numéro] deny [adresse_ip] [wildcard]
```

**Exemple :**
```
access-list 10 deny 192.168.1.5 0.0.0.0
```

- Bloque uniquement l'adresse 192.168.1.5
- Le wildcard `0.0.0.0` signifie une correspondance exacte

---

## 3. Autoriser tout le reste

**Commande :**
```
access-list [numéro] permit any
```

**Exemple :**
```
access-list 10 permit any
```

- Autorise tout le trafic restant

> ⚠️ Sans cette ligne, tout sera bloqué par défaut.

---

## 4. Créer une ACL étendue

**Commande :**
```
access-list [numéro] permit [protocole] [source] [wildcard] [destination] [wildcard]
```

**Explication :**
- `[numéro]` → entre 100 et 199
- `[protocole]` → `tcp` / `udp` / `ip` / `icmp`
- `[source]` → adresse source
- `[destination]` → adresse destination

**Exemple :**
```
access-list 100 permit tcp 192.168.1.0 0.0.0.255 any
```

- Autorise le protocole TCP
- Depuis 192.168.1.0
- Vers n'importe quelle destination

---

## 5. Filtrer par port (HTTP, FTP…)

**Commande :**
```
access-list [numéro] permit tcp [source] [wildcard] [destination] [wildcard] eq [port]
```

**Exemple HTTP :**
```
access-list 100 permit tcp any any eq 80
```
- Autorise le trafic HTTP (port 80)

**Exemple HTTPS :**
```
access-list 100 permit tcp any any eq 443
```
- Autorise HTTPS (port 443)

**Exemple FTP :**
```
access-list 100 deny tcp any any eq 21
```
- Bloque FTP (port 21)

---

## 6. Créer une ACL nommée

**Commande :**
```
ip access-list extended [nom]
```

**Exemple :**
```
ip access-list extended BLOQUE_FTP
```
- Crée une ACL étendue nommée `BLOQUE_FTP`

**Ajouter des règles dans une ACL nommée :**
```
deny tcp any any eq 21
permit ip any any
```
- Bloque FTP
- Autorise tout le reste

---

## 7. Appliquer une ACL sur une interface (trafic entrant)

**Commande :**
```
interface [nom_interface]
ip access-group [numéro ou nom] in
```

**Exemple :**
```
interface GigabitEthernet0/0
ip access-group 10 in
```

- Applique ACL 10 sur l'interface G0/0
- Trafic entrant (`in`)

---

## 8. Appliquer une ACL en sortie

**Commande :**
```
ip access-group [numéro] out
```

**Exemple :**
```
interface GigabitEthernet0/1
ip access-group 100 out
```

- Applique ACL 100 sur le trafic sortant

---

## 9. Supprimer une ACL

**Commande :**
```
no access-list [numéro]
```

**Exemple :**
```
no access-list 10
```

- Supprime l'ACL numéro 10

---

## 10. Supprimer une règle dans une ACL nommée

**Commande :**
```
no [règle]
```

**Exemple :**
```
ip access-list extended BLOQUE_FTP
no deny tcp any any eq 21
```

- Supprime la règle qui bloque FTP

---

## 11. Afficher les ACL

**Commande :**
```
show access-lists
```
- Affiche toutes les ACL configurées

**Voir une ACL spécifique :**
```
show access-lists [numéro]
```

**Exemple :**
```
show access-lists 100
```
- Affiche l'ACL 100

---

## 12. Voir les ACL appliquées aux interfaces

**Commande :**
```
show ip interface
```

- Affiche les interfaces et montre quelles ACL sont appliquées

---

## 13. Exemple complet (configuration simple)

**Objectif :**
- Bloquer FTP
- Autoriser HTTP
- Autoriser le reste

**Configuration :**
```
access-list 100 deny tcp any any eq 21
access-list 100 permit tcp any any eq 80
access-list 100 permit ip any any

interface GigabitEthernet0/0
ip access-group 100 in
```
