## Partie 1 : Gestion des utilisateurs

### Q.2.1.1 Sur le serveur, créer un compte pour ton usage personnel.  

Pour créer mon compte personnel je me suis connecté en tant que root.  
Puis j'ai utilisé la commande adduser  
```
root@SRVLX01:~# adduser julien
```
### Q.2.1.2 Quelles préconisations proposes-tu concernant ce compte ?  

Concernant ce compte personnel, mes préconnisations sont les suivantes :
- Utiliser un mot de passe fort (assez long et aussi contenant des caractères spéciaux, chiffres majuscules et minuscules)
- Avoir des droits d'administrations restreints, uniquement lorsque c'est nécessaire afin d'éviter une manipulation dangereuse par exemple pour l'intégrité du système.

Pour avoir quand même des droits d'administration, mais restreints j'ai ajouté ce compte dans les comptes sudo (donc dans le fichier sudoers) avec cette commande :  
```
usermod -aG sudo julien 
```
Si je n'avais pas été en root j'aurais du rajouter `sudo` devant. Ainsi pour avoir des droits d'administration sur ce poste je serais obligé de préfixer avec la commande `sudo`. 
  
## Partie 2 : Configuration de SSH

Un serveur SSH est lancé sur le port par défaut.
Il est possible de s'y connecter avec n'importe quel compte, y compris le compte root.

### Q.2.2.1 Désactiver complètement l'accès à distance de l'utilisateur root.  

Pour désactiver l'accès root en ssh, il faut éditer le fichier de configuration SSH :  
```
nano /etc/ssh/sshd_config
```
Puis il faut modifier la ligne PermittRootLogin :  
Il faut effacer le # afin de rendre active cette ligne et passer le paramère à "no" => Enregister et quitter  
<P ALIGN="center"><IMG src="https://github.com/julien-Nmd/CHECKPOINT-3/blob/main/CHECKPOINT-3-capture-13.png" width=600></P>  
Ensuite il faut redémarer le service ssh avec la commande  

```
systemctl restart ssh
```
### Q.2.2.2 Autoriser l'accès à distance à ton compte personnel uniquement.  

De la même manière pour n'autoriser que certains utilisateurs à se connecter en SSH il faut modifier une ligne dans le même fichier.
Cette ligne n'existant pas il faudra la rajouter : "AllowUsers" suivi des utilisateurs autorisés. Toutes les autres tentatives de connexion SSH seront refusées.  
<P ALIGN="center"><IMG src="https://github.com/julien-Nmd/CHECKPOINT-3/blob/main/CHECKPOINT-3-capture-14.png" width=600></P>  
il faudra encore relancer le service ssh pour que les modifications du fichier de configurations soient prises en compte :  

```
systemctl restart ssh
```

### Q.2.2.3 Mettre en place une authentification par clé valide et désactiver l'authentification par mot de passe  

Pour faire ça, il faut d'abord générer une clé sur ma machine locale en ouvrant un terminal, puis en tapant la commande suivante :  
```
ssh-keygen -t rsa -b 4096 -C "julien.normand@gmail.com"
```
<P ALIGN="center"><IMG src="https://github.com/julien-Nmd/CHECKPOINT-3/blob/main/CHECKPOINT-3-capture-15.png" width=600></P>  
Puis toujours depuis le terminal de ma machine locale j'envoie la clé publique directement sur la machine debian avec la commande :  

```
ssh-copy-id -i ~/.ssh/id_rsa.pub julien@192.168.1.56
```
<P ALIGN="center"><IMG src="https://github.com/julien-Nmd/CHECKPOINT-3/blob/main/CHECKPOINT-3-capture-16.png" width=600></P>  
Bien sûr il faut mettre l'ip de la debian et le nom d'utiilisateur créé précédemment.  
Ensuite on retoutne sur la machine Debian pour encore modifier le fichier de configuration SSH :  
```
nano /etc/ssh/sshd_config
```
On recherche ces 2 lignes et on les modifie ainsi :  

```
PubkeyAuthentication yes
PasswordAuthentication no
```

<P ALIGN="center"><IMG src="https://github.com/julien-Nmd/CHECKPOINT-3/blob/main/CHECKPOINT-3-capture-17.png" width=600></P>  
Puis on relance encore le service :  

```
systemctl restart ssh
```

## Partie 3 : Analyse du stockage

### Q.2.3.1 Quels sont les systèmes de fichiers actuellement montés ?  

<P ALIGN="center"><IMG src="https://github.com/julien-Nmd/CHECKPOINT-3/blob/main/CHECKPOINT-3-capture-18.png" width=800></P>  

Les systèmes de fichiers actuellement montés sont ext2, ext4 et swap.

### Q.2.3.2 Quel type de système de stockage ils utilisent ?  

Les types de stochage sont dev/sda, /dev/md0 et LVM.

### Q.2.3.3 Ajouter un nouveau disque de 8,00 Gio au serveur et réparer le volume RAID  
  
Pour faire ça j'ai ajouter un disque virtuel de 8 Go comme indiqué puis j'ai redémarré la VM et me suis connecté en root.  
J'ai d'abord vérifié la présence du disque avec un `lsblk`.  
Ensuite je l'ai partitionné en RAID auto :  
```bash
fdisk /dev/sdb/
```
Puis : 
`n` nouvelle partition  
`p` partition primaire  
`1` numéro de partition  
`ENTREE` pour le premier secteur par defaut  
`ENTREE` pour le dernier secteur par défaut (soit la totalité du disque)  
`t` pour le type de partition  
`L` qui affichera tous les types possibles  
`fd` pour RAID Linux auto  
`w` pour écrire la partition et quitter l'utilitaire  

Puis j'ai ajouté le nouveau disque au RAID avec la commande :  
```bash
mdadm --add /dev/md0 /dev/sdb1
```
Ensuite j'ai vérifié avec :  
```bash
mdadm --detail /dev/md0
```
<P ALIGN="center"><IMG src="https://github.com/julien-Nmd/CHECKPOINT-3/blob/main/CHECKPOINT-3-capture-19.png" width=800></P>  

On voit bien le statut `clean` et les 2 volumes sont bien synchronisés.  

### Q.2.3.4 Ajouter un nouveau volume logique LVM de 2 Gio qui servira à héberger des sauvegardes. Ce volume doit être monté automatiquement à chaque démarrage dans l'emplacement par défaut : /var/lib/bareos/storage.  
<P ALIGN="center"><IMG src="https://github.com/julien-Nmd/CHECKPOINT-3/blob/main/CHECKPOINT-3-capture-20.png" width=800></P>   
<P ALIGN="center"><IMG src="https://github.com/julien-Nmd/CHECKPOINT-3/blob/main/CHECKPOINT-3-capture-21.png" width=800></P> 

### Q.2.3.5 Combien d'espace disponible reste-t-il dans le groupe de volume ?  
<P ALIGN="center"><IMG src="https://github.com/julien-Nmd/CHECKPOINT-3/blob/main/CHECKPOINT-3-capture-22.png" width=800></P>   
Il reste 1.79 G d'espace dispo dans le groupe de volume.
  
## Partie 4 : Sauvegardes

Le logiciel bareos est installé sur le serveur.
Les composants bareos-dir, bareos-sd et bareos-fd sont installés avec une configuration par défaut.

### Q.2.4.1 Expliquer succinctement les rôles respectifs des 3 composants bareos installés sur la VM.  

**- bareos-dir**  
Pour **Bareos Director**, c'est lui qui est responsable de la planification, du contrôle et du lancement des tâches de sauvegardes.  
**- bareos-sd**  
Pour **Bareos Storage Daemon**, permet d'effectuer les sauvegardes sur la machine. Il peut effectuer des sauvegardes sur toutes les machines ou il est installé et sur différents supports.
**- bareos fd**  
Pour **Bareos File Daemon**, ce composant est installé sur chaque machine devant être sauvegardée.
Il est en charge de collecter les informations à sauvegarder et de les envoyer au **Bareos Storage Daemon**.  
  
## Partie 5 : Filtrage et analyse réseau

### Q.2.5.1 Quelles sont actuellement les règles appliquées sur Netfilter ?  

<P ALIGN="center"><IMG src="https://github.com/julien-Nmd/CHECKPOINT-3/blob/main/CHECKPOINT-3-capture-23.png" width=800></P>  

### Q.2.5.2 Quels types de communications sont autorisées ?  

On peut voir que le filtre autorise :
- La communication interne de la machine  
- Les connections entrantes sur le port 22 en TCP, donc la connection SSH est autorisée
- Les protocoles ICMP en ipv4 et en ipv6, donc le ping est autorisé

### Q.2.5.3 Quels types sont interdit ?  
  
`ct state invalid drop`indique que les paquets considérés comme invalides seront rejetés.  
  
### Q.2.5.4 Sur nftables, ajouter les règles nécessaires pour autoriser bareos à communiquer avec les clients bareos potentiellement présents sur l'ensemble des machines du réseau local sur lequel se trouve le serveur.  

```bash
# Créer une table et y ajouter une chaine
nft add table inet filter
nft add chain inet filter input { type filter hook input priority 0; policy drop; }

# Autoriser les connexions établies ou associées
nft add rule inet filter input ct state established,related accept

# Autoriser le trafic de la boucle locale
nft add rule inet filter input iifname "lo" accept

# Autoriser le trafic entrant sur les ports Bareos depuis le réseau local
nft add rule inet filter input ip saddr 192.168.1.0/24 tcp dport {9101, 9102, 9103} accept

# rendre cette configuration persistante en la sauvegardant dans le fichier par défaut :
nft list ruleset > /etc/nftables.conf


```

