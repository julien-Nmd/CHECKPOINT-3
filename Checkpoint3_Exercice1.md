# Exercice 1 : Manipulations pratiques sur VM Windows (temps estimé : 1h30)

## Partie 1 : Gestion des utilisateurs

L'utilisateur Kelly Rhameur a quitté l'entreprise.
Elle est remplacée par Lionel Lemarchand

### Q.1.1.1 Créer l'utilisateur Lionel Lemarchand avec les même attribut de société que Kelly Rhameur.
  
Pour créer le nouvel utilisateur en conservant les mêmes attributs que Kelly Rhameur, j'ai utilisé la console Active Directory Users ans Computers.  
Dans server Manager => Tools => Active Directory Users and Computers  
Puis j'ai fait un clic droit sur le domaine TSSR.LAN => Find, dans Name j'ai tapé Kelly => Find now  
Dans Search results j'ai fait un clic droit sur Kelly Rhameur => Properties => Onglet Object pour noter son chemin dans l'arborescence de l'AD.  
J'ai déroulé TSSR.LAN/LabUsers/DirectionDesRessourcesHumaines/Kelly.Rhameur  => clic droit dessus => Copy  

<P ALIGN="center"><IMG src="https://github.com/julien-Nmd/CHECKPOINT-3/blob/main/CHECKPOINT-3-capture-1.png" width=600></P>  

Puis j'ai rentré les noms et prénoms du nouvel utilisateur Lionel Lemarchand. => Next 

<P ALIGN="center"><IMG src="https://github.com/julien-Nmd/CHECKPOINT-3/blob/main/CHECKPOINT-3-capture-2.png" width=600></P>  

J'ai rentré un mot de passe (ici Azerty1*) et j'ai coché "User must change password at next logon". => Next 

<P ALIGN="center"><IMG src="https://github.com/julien-Nmd/CHECKPOINT-3/blob/main/CHECKPOINT-3-capture-3.png" width=600></P>   

J'ai vérifié les principaux attributs => Finish  
  

<P ALIGN="center"><IMG src="https://github.com/julien-Nmd/CHECKPOINT-3/blob/main/CHECKPOINT-3-capture-4.png" width=600></P> 

En suite j'ai vérifié en faisant un clic droit sur chacun des 2 utilisateurs que les attributs ont bien été copiés.  
Ici, par exemple on peut bien voir qu'ils sont bien tous 2 membres des mêmes groupes. 

<P ALIGN="center"><IMG src="https://github.com/julien-Nmd/CHECKPOINT-3/blob/main/CHECKPOINT-3-capture-5.png" width=600></P> 

  
### Q.1.1.2 Créer une OU DeactivatedUsers et déplace le compte désactivé de Kelly Rhameur dedans.  

Etant donné que cette OU concerne à priori des utilisateurs j'ai créé cette OU dans LabUsers.  
Toujours dans la même console, clic droit sur LabUsers => New => Organisation Unit  
Puis j'ai inscrit "DeactivatedUsers" dans Name et OK.  
Après j'ai désactivé le compte de Kelly Rhameur => Clic droit dessus => Properties => Onglet Account => Coché la case "Acount is disabled".  
J'ai également sélectionné la date du jour dans Acount expires => End of => Apply => OK  
<P ALIGN="center"><IMG src="https://github.com/julien-Nmd/CHECKPOINT-3/blob/main/CHECKPOINT-3-capture-7.png" width=600></P>   

Puis j'ai simplement glissé cet utilisateur dans la nouvelle OU DeactivatedUsers.  

<P ALIGN="center"><IMG src="https://github.com/julien-Nmd/CHECKPOINT-3/blob/main/CHECKPOINT-3-capture-8.png" width=600></P>  

### Q.1.1.3 Modifier le groupe de l'OU dans laquelle était Kelly Rhameur en conséquence.  

J'ai pu voir que Kelly Rhameur (précédemment dans Member of) était dans le groupe GrpUsersDirectionDesRessourcesHumaines.
Ce groupe étant dans la même OU ou est cet utilisateur j'ai simplement double cliqué dessus. Puis j'ai sélectionné Kelly Rhameur et cliqué sur "Remove" afin de la sortir de ce groupe. 
"Do you want to remove the selected object..." => Yes => OK

<P ALIGN="center"><IMG src="https://github.com/julien-Nmd/CHECKPOINT-3/blob/main/CHECKPOINT-3-capture-6.png" width=600></P> 


### Q.1.1.4 Créer le dossier Individuel du nouvel utilisateur et archive celui de Kelly Rhameur en le suffixant par -ARCHIVE.  

J'ai d'abord archivé le dossier de Kelly Rhameur.  
Dans l'explorateur de fichiers, j'ai ouvert le disque F:\ nommé Dossiers Individuels.  
J'ai fait un clic droit sur le dossier kelly.rhameur => Properties  
J'ai ajouté le suddixe -ARCHIVE au nom du dossier puis j'ai cliqué sur "Advanced" => Coché la case "Folder is ready for archiving" => OK => Apply => OK  

Ensuite j'ai créé le dossier de Lionel Lemarchand :
Clique droit => New => Folder => lionel.lemarchand => ENTER  
Clique droit dessus => Properties => Onglet Sharing => dans Add j'ai tapé lionel, puis find => encore lionel => check names => selectionné lionel lemarchand  
j'ai mois en "read and write" => OK

## Partie 2 : Restriction utilisateurs

### Q.1.2.1 Faire en sorte que l'utilisateur Gabriel Ghul ne puisse se connecter que du lundi au vendredi, de 7h à 17h.  

Pour ça j'ai fait comme précédemment pour retrouver cet utilisateur.  
Dans AD Users and computers => clic droir sur le domaine => Find => Gabriel => regardé son emplacement dans l'onglet Object.  
Dans les properties de ce User => Onglet Account =>  Cliqué sur "Logon Hours" 
  
 <P ALIGN="center"><IMG src="https://github.com/julien-Nmd/CHECKPOINT-3/blob/main/CHECKPOINT-3-capture-9.png" width=600></P>  

 Puis j'ai désactivé tout avec le bouton à droite Logon Denied => sélectionné la zone 7h-17h, lundi au vendredi et cliqué sur le bouton "Logon Permitted" => OK => Apply => OK  
 
 <P ALIGN="center"><IMG src="https://github.com/julien-Nmd/CHECKPOINT-3/blob/main/CHECKPOINT-3-capture-10.png" width=600></P> 
  
### Q.1.2.2 De même, bloquer sa connexion au seul ordinateur CLIENT01.  

Pour ça, toujours dans la console Active Directory Users and Computers.  
Dans l'OU LabComputers => clic droit sur CLIENT1 => Promerties => Onglet Security => cliqué sur Advanced  
Puis Add => Select a principal => J'ai tapé Gab => check names => Sélectionné Gabriel Ghul => OK  
  
<P ALIGN="center"><IMG src="https://github.com/julien-Nmd/CHECKPOINT-3/blob/main/CHECKPOINT-3-capture-11.png" width=600></P>  

Type sélectionné "Deny" => Ok => Apply => OK

### Q.1.2.3 Mettre en place une stratégie de mot de passe pour durcir les comptes des utilisateurs de l'OU LabUsers.  

Pour mettre en place une stratégie de mot de passe spécifique pour l'OU LabUsers j'ai créé une GPO.  
Dans Server manager => Tools => Group Policy Management  
J'ai fait un clique droit sur LabUsers et sélectionné "Create a GPO in this domain, and link it here". Je l'ai nommé UsersPasswordPolicy  => OK  
Puis j'ai fait un clic droit dessus => Edit  
Je suis allé dans Computer  Configuration\Windows Settings\Security Settings\Password Policies.  
(1) Là j'ai double cliqué sur "Password must meet complexity requirements => Coché Enabled => Apply => OK  
(2) J'ai fait la même chose avec "Minimum password lengh audit" => coché "Define this policy setting" et mis à 12 => Ally => OK 
  
Ainsi les mots de passe devront avoir au moins une majuscule et une minuscule, un chiffre et un caractère spécial (1) et devront comporter au moins 12 caractères (2). La GPO est active et déjà lié à l'OU LabUsers.  

## Partie 3 : Lecteurs réseaux

### Q.1.3.1 Créer une GPO Drive-Mount qui monte les lecteurs E: et F: sur les clients.  
  
Anant de pouvoir créer la GPO qui montera les lecteurs sur les utilisateurs il faut activer le partage de ces lecteurs.  
Pour faire ça, dans Server Manager, j'ai sélectionné "File and storage service" sur la gauche, puis Shares.  
Dans la fenêtre Shares j'ai fait un clic droit => New Share => Laissé "SMB Share - quick" => Next => Sélectionné le lecteur E:\ => Next => Share Name j'ai inscrit "Share_E" => Next (x3) => Create  
Même opération avec F:\ nommé "Share_F".  

Ensuite, retour dans la console Group Policy Management.  

Clic droit sur LabUsers et sélectionné "Create a GPO in this domain, and link it here".  
Nommée "Drive-Mount" => OK => clic droit => Edit  
