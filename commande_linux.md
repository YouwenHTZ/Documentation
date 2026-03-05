## Se repérer dans le système

```bash
pwd          # Où suis-je ? Affiche le dossier courant
ls           # Quoi est ici ? Liste les fichiers et dossiers
ls -l        # Liste détaillée (permissions, taille, date)
ls -la       # Idem + fichiers cachés (commençant par un point)
clear        # Effacer l'écran du terminal
```

---

## Se déplacer dans les dossiers

```bash
cd Documents          # Aller dans le dossier "Documents"
cd /var/log           # Aller vers un chemin absolu (depuis la racine /)
cd ..                 # Remonter d'un niveau (dossier parent)
cd ~                  # Aller dans ton dossier personnel (/home/tonnom)
cd -                  # Retourner au dossier précédent
```

---

## Créer, copier, déplacer, supprimer

```bash
# Créer
touch fichier.txt           # Créer un fichier vide
mkdir monDossier            # Créer un dossier
mkdir -p a/b/c              # Créer des dossiers imbriqués d'un coup

# Copier
cp fichier.txt copie.txt           # Copier un fichier
cp -r monDossier/ monDossierCopie/ # Copier un dossier (-r = récursif)

# Déplacer / Renommer
mv fichier.txt Documents/          # Déplacer un fichier
mv ancien.txt nouveau.txt          # Renommer un fichier

# Supprimer
rm fichier.txt              # Supprimer un fichier
rm -r monDossier/           # Supprimer un dossier et son contenu
```

---

## Lire et afficher des fichiers

```bash
cat fichier.txt             # Afficher tout le contenu d'un fichier
less fichier.txt            # Lire page par page (↑↓ pour naviguer, q pour quitter)
head fichier.txt            # Afficher les 10 premières lignes
head -n 20 fichier.txt      # Afficher les 20 premières lignes
tail fichier.txt            # Afficher les 10 dernières lignes
tail -f /var/log/syslog     # Suivre un fichier en direct (logs)
```

---

## Écrire et éditer des fichiers

```bash
nano fichier.txt            # Ouvrir l'éditeur nano (le plus simple pour débuter)
```

**Dans nano :**
| Raccourci | Action |
|-----------|--------|
| `Ctrl + O` | Sauvegarder |
| `Ctrl + X` | Quitter |
| `Ctrl + K` | Couper une ligne |
| `Ctrl + U` | Coller |
| `Ctrl + W` | Chercher un mot |

```bash
# Écrire directement dans un fichier sans éditeur
echo "Bonjour" > fichier.txt        # Écrire (écrase le contenu existant)
echo "Ligne 2" >> fichier.txt       # Ajouter à la fin (sans écraser)
```

---

## Chercher

```bash
# Chercher un fichier
find / -name "fichier.txt"          # Chercher par nom depuis la racine
find ~ -name "*.txt"                # Tous les .txt dans ton home
find . -name "*.log"                # Tous les .log dans le dossier courant

# Chercher dans un fichier
grep "mot" fichier.txt              # Chercher un mot dans un fichier
grep -r "mot" /dossier/             # Chercher dans tous les fichiers d'un dossier
grep -i "mot" fichier.txt           # Recherche sans distinction majuscules/minuscules

# Chercher une commande
which python3                       # Trouver où est installée une commande
```

---

## Utilisateurs & Permissions

```bash
whoami                      # Qui suis-je ? (nom de l'utilisateur courant)
sudo commande               # Exécuter une commande en tant qu'administrateur (root)
sudo su                     # Passer en mode administrateur (root)
exit                        # Quitter le mode root ou fermer le terminal
passwd                      # Changer son propre mot de passe
```

**Comprendre les permissions avec `ls -l` :**

```
-rwxr-xr-- 1 alice users 1024 mars 1 fichier.txt
 ↑↑↑ ↑↑↑ ↑↑↑
 │   │   └── Autres (r = lecture seule)
 │   └──── Groupe (r-x = lecture + exécution)
 └──────── Propriétaire (rwx = lecture + écriture + exécution)
```

```bash
chmod +x script.sh          # Rendre un script exécutable
chmod 644 fichier.txt       # rw-r--r-- (lecture/écriture owner, lecture pour les autres)
chown alice fichier.txt     # Changer le propriétaire d'un fichier (besoin de sudo)
```

---

## Installer des logiciels

```bash
# Sur Ubuntu / Debian
sudo apt update                     # Mettre à jour la liste des logiciels disponibles
sudo apt upgrade                    # Mettre à jour tous les logiciels installés
sudo apt install [nomdulogiciel]      # Installer un logiciel
sudo apt remove [nomdulogiciel]       # Désinstaller un logiciel
sudo apt search [nomdulogiciel]       # Chercher un logiciel disponible

# Sur Fedora / Red Hat / CentOS
sudo dnf update
sudo dnf install [nomdulogiciel]
sudo dnf remove [nomdulogiciel]
```

---

## Gérer les services

```bash
sudo systemctl start [nomduservice]          # Démarrer un service
sudo systemctl stop [nomduservice]           # Arrêter un service
sudo systemctl restart [nomduservice]        # Redémarrer un service
sudo systemctl status [nomduservice]         # Voir si un service fonctionne
sudo systemctl enable [nomduservice]         # Lancer automatiquement au démarrage
sudo systemctl disable [nomduservice]        # Ne plus lancer au démarrage
```

---

## Surveiller le système

```bash
top                         # Voir les processus en cours + utilisation CPU/RAM
htop                        # Version améliorée de top (à installer)
free -h                     # Voir la mémoire RAM disponible
df -h                       # Voir l'espace disque disponible
du -sh /chemin/             # Voir la taille d'un dossier
uname -a                    # Informations sur le système (version noyau, OS...)
uptime                      # Depuis combien de temps la machine tourne
```

---

## Gérer les processus

```bash
ps aux                      # Lister tous les processus en cours
ps aux | grep firefox       # Chercher un processus spécifique
kill 1234                   # Arrêter un processus par son numéro (PID)
kill -9 1234                # Forcer l'arrêt d'un processus récalcitrant
killall firefox             # Arrêter tous les processus nommés "firefox"
```

---

## Réseau (bases)

```bash
ping google.com             # Tester la connexion internet
ping -c 4 google.com        # Envoyer seulement 4 paquets
ip a                        # Voir ses adresses IP et interfaces réseau
ip route                    # Voir la passerelle et les routes
curl https://example.com    # Afficher le contenu d'une page web
wget https://example.com/fichier.zip   # Télécharger un fichier

# SSH — connexion à distance
ssh user@192.168.1.10       # Se connecter à une machine distante
exit                        # Se déconnecter du SSH
```

---

## Astuces indispensables du terminal

```bash
# Historique
history                     # Voir toutes les commandes utilisées
!!                          # Répéter la dernière commande
!50                         # Répéter la commande numéro 50 de l'historique
Ctrl + R                    # Rechercher dans l'historique (taper pour chercher)

# Raccourcis clavier utiles
Tab                         # Auto-complétion (noms de fichiers, commandes)
Ctrl + C                    # Annuler / interrompre une commande en cours
Ctrl + Z                    # Mettre en pause une commande
Ctrl + D                    # Fermer le terminal / déconnexion
Ctrl + L                    # Effacer l'écran (= clear)
Ctrl + A                    # Aller au début de la ligne
Ctrl + E                    # Aller à la fin de la ligne

# Le pipe | — enchaîner des commandes
ls -la | grep ".txt"        # Filtrer la liste des fichiers pour ne voir que les .txt
cat fichier.txt | wc -l     # Compter le nombre de lignes d'un fichier
ps aux | grep firefox       # Filtrer les processus

# Redirection
commande > fichier.txt      # Écrire la sortie dans un fichier (écrase)
commande >> fichier.txt     # Ajouter la sortie à la fin d'un fichier
commande 2> erreurs.txt     # Écrire les erreurs dans un fichier
```

---

## Arborescence Linux — Les dossiers importants

| Dossier | Contenu |
|---------|---------|
| `/` | Racine du système (tout part de là) |
| `/home/` | Dossiers personnels des utilisateurs |
| `/root/` | Dossier personnel de l'administrateur root |
| `/etc/` | Fichiers de configuration du système |
| `/var/log/` | Journaux (logs) du système et des services |
| `/tmp/` | Fichiers temporaires (vidés au redémarrage) |
| `/bin/` | Commandes essentielles du système |
| `/usr/bin/` | Commandes des logiciels installés |
| `/dev/` | Périphériques (disques, USB...) |
| `/mnt/` | Points de montage temporaires |
| `/proc/` | Informations sur les processus en cours |


---

## Les 20 commandes à mémoriser en priorité

| Commande | Utilité |
|----------|---------|
| `pwd` | Savoir où on est |
| `ls` | Voir les fichiers |
| `cd` | Se déplacer |
| `mkdir` | Créer un dossier |
| `touch` | Créer un fichier |
| `cp` | Copier |
| `mv` | Déplacer / renommer |
| `rm` | Supprimer |
| `cat` | Lire un fichier |
| `nano` | Éditer un fichier |
| `grep` | Chercher dans un fichier |
| `find` | Chercher un fichier |
| `sudo` | Exécuter en admin |
| `chmod` | Modifier les permissions |
| `apt install` | Installer un logiciel |
| `systemctl` | Gérer les services |
| `top` | Surveiller le système |
| `df -h` | Espace disque |
| `ping` | Tester le réseau |
| `man` | Obtenir de l'aide |
