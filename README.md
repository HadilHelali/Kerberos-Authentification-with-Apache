# Kerberos-Authentification-with-Apache

## What is Kerberos ?

Kerberos is a computer network authentication protocol, which provides a secure Single Sign On (SSO) based on a trusted third-party mutual authentication service. It is secure because the user’s password is never transmitted over the wire. Kerberos uses Tickets which are negotiated with the server, with a limited time to live.
In the case of HTTP, support for Kerberos is usually provided using the SPNEGO authentication mechanism (Simple and Protected GSS-API Negotiation). This is also known as ‘integrated authentication’ or ‘negotiate authentication’. Apache does not itself support SPNEGO, but support can be added by means of the mod_auth_kerb authentication module.

## Hostname and IP Addresses
in this manipulation we will use 3 kali machines : 
●	KDC machine : 192.168.232.3
●	Apache server machine : 192.168.232.6
●	Client machine : 192.168.232.5
-> Virtual machines only have a NAT adapter by default but we will have to assign IP addresses to these machines, In order to do that we have to add a host-only adapter to each machine manually 
 <img src="./screenshots/1.png" width=450 height=300>

### Synchronisation des horloges
Kerberos nécessite une synchronisation de horloges entre les 3 machines. Donc les horloges des 3 machines doivent être synchronisées.

System clock synchronized était initialisée à false.  Pour l’activer : 
  
 <img src="./screenshots/2.png" width=450 height=300>
 <img src="./screenshots/3.png" width=450 height=300>
On aura alors dans les 3 machines : 
 
  <img src="./screenshots/4.png" width=450 height=300>
   <img src="./screenshots/5.png" width=450 height=300>
  <img src="./screenshots/6.png" width=450 height=300>

Définissons des noms d'hôtes pour chaque machine:

●	Machine KDC :
 
 <img src="./screenshots/7.png" width=450 height=300>
●	Machine Client :
  <img src="./screenshots/8.png" width=450 height=300>

●	Machine Apache Server :
  <img src="./screenshots/9.png" width=450 height=300>

NB: pour vérifier le nom d'hôte de chaque machine on utilise la commande : hostname  

Ensuite on mappe chaque nom d'hôte avec son IP address sur chaque machine . On accède alors au fichier au fichier /etc/hosts et on ajoute ces lignes .
 
 <img src="./screenshots/10.png" width=450 height=300>
  <img src="./screenshots/11.png" width=450 height=300>

Une fois la configuration terminée, nous pouvons vérifier si les 3 machines sont accessibles grâce à la commande ping:
●	Machine KDC :
  <img src="./screenshots/12.png" width=450 height=300>
 


●	Machine client : 
 
 <img src="./screenshots/13.png" width=450 height=300>

●	Machine ApacheServer:

  <img src="./screenshots/14.png" width=450 height=300>
		

###	Configuration des machines:
●	Centre de Distribution des Clés (KDC): dans la machine KDC

  ●	on installe les packages à installer sur la machine KDC:
<img src="./screenshots/15.png" width=450 height=300>
<img src="./screenshots/16.png" width=450 height=300>
 
 

Lors de l’installation , on doit configurer Kerberos comme suit 

●	Le realm : ‘INSAT.TN’ (doit être écrit tout en majuscules)
●	Le serveur Kerberos: ‘kdc.insat.tn’
●	Le serveur administrateur du royaume : ‘kdc.insat.tn’

	Le realm de Kerberos est un terme utilisé dans le système d'authentification Kerberos. Il s'agit d'une chaîne de caractères qui identifie un domaine administratif unique pour l'ensemble des serveurs et des clients qui utilisent Kerberos pour l'authentification et l'autorisation.

●	pui on exécute la commande : 


<img src="./screenshots/17.png" width=450 height=300>










elle nous demande alors d’entrer le ‘KDC dataset master key ‘

NB:La commande sudo krb5_newrealm est utilisée pour créer un nouveau royaume Kerberos sur un serveur. Cette commande est généralement utilisée par les administrateurs système qui mettent en place un nouvel environnement Kerberos.
Lorsque vous exécutez la commande sudo krb5_newrealm, elle vous invite à entrer plusieurs options de configuration, telles que le nom de royaume, le nom du serveur d'administration Kerberos et le royaume Kerberos par défaut pour les clients qui ne font pas partie du nouveau royaume. Une fois que vous avez saisi toutes les informations requises, la commande crée un nouveau royaume Kerberos et initialise la base de données Kerberos.
●	création des utilisateurs:
Les utilisateurs et les services d’un domaine sont définis comme des principals dans Kerberos . Ils sont gérés par un utilisateur admin que nous devons créer manuellement : 
	 <img src="./screenshots/18.png" width=450 height=300>
NB :kadmin.local est un utilitaire d'administration local pour Kerberos qui permet de gérer les principaux d'une base de données Kerberos à partir d'une ligne de commande.

●	pour vérifier la liste des principals:
 <img src="./screenshots/19.png" width=450 height=300>

●	                  Ensuite, nous devons accorder tous les droits d’accès à la base de données Kerberos à admin principal root / admin en utilisant le fichier de configuration /etc/krb5kdc/kadm5.acl .
 <img src="./screenshots/20.png" width=450 height=300>
  <img src="./screenshots/21.png" width=450 height=300>
 
Pour que les modifications prennent effet, nous devons redémarrer le service suivant: 
  <img src="./screenshots/22.png" width=450 height=300>

●	Maintenant on va créer des principaux pour la machine cliente et la machine apacheserver 
○	Le principal pour le client :
  <img src="./screenshots/23.png" width=450 height=300>
○	Le principal su server Apache:
  <img src="./screenshots/24.png" width=450 height=300>

●	pour vérifier la nouvelles liste des principaux :
  <img src="./screenshots/25.png" width=450 height=300>

●	Le serveur Apache : 
○	on installe Apache2:
   <img src="./screenshots/26.png" width=450 height=300>
    <img src="./screenshots/27.png" width=450 height=300>

 

●	pour vérifier que Apache2 est déjà démarré :
  <img src="./screenshots/28.png" width=450 height=300>

●	pour que notre serveur démarre automatiquement au démarrage de la machine , on doit exécuter la commande:
  <img src="./screenshots/29.png" width=450 height=300>

●	Après l’installation il est recommandé d’installer “curl”, si on veut pouvoir lancer les cnxs web depuis le navigateur:
  <img src="./screenshots/30.png" width=450 height=300>

Les fichiers et dossiers nécessaires à la configuration des sites webs au travers des hôtes virtuels sont dans /etc/apache2. ceux nous intéressant sont:
●	Le fichier apache2.conf:
Contenant la configurations par defauts d'apache.
●	Le dossier conf-available:
Contenant les configurations disponibles dans apache.
●	Le dossier conf-enabled:
Contenant les configurations actives dans apache.
●	Le dossier mods-available:
Contenant les modules prient en charges par apache.
●	Le dossier sites-available:
Contenant les fichiers de configuration des sites web.
●	NB:
Apache lit les fichier de configuration pas ordre numérique de 000 à XXX.
 <img src="./screenshots/31.png" width=450 height=300>

●	Le dossier sites-enabled: (contenant les fichiers des sites actif sur le serveur)
 <img src="./screenshots/32.png" width=450 height=300>
 <img src="./screenshots/33.png" width=450 height=300>
### ○	Configuration du serveur pour le site:
Pour ce Projet nous avons décidé de changer la page html par défaut du serveur par un site web basic (sans Js ni Php).

●	Nous allons créer un site sur notre serveur.Il sera stocké à l'emplacement suivant : /var/www/sitesec :
 <img src="./screenshots/34.png" width=450 height=300>
●	www-data étant l'utilisateur d'apache appartenant au groupe www-data, nous allons changer le propriétaire de notre dossier ainsi que son groupe. Et vérifier l'effectivité des changement grâce à ls:
 <img src="./screenshots/35.png" width=450 height=300>
 <img src="./screenshots/36.png" width=450 height=300>
 

●	créons le fichier de configuration de notre site:
 <img src="./screenshots/37.png" width=450 height=300>
 <img src="./screenshots/38.png" width=450 height=300>
 
●	Apache possède un outil de vérification des fichiers de configurations nommé configtest, qui effectue un test de ces fichiers (la syntaxe, indentation, etc ...). Il est accessible par la commande:
 <img src="./screenshots/39.png" width=450 height=300>
●	nous devons activer notre site web. En créant un lien symbolique sites-available vers sites-enabled grâce à la commande :
 <img src="./screenshots/40.png" width=450 height=300>

●	Nous avons créé un fichier .htaccess pour la gérer la réécriture de notre url. afin que chaque fois que nous tapons www.sitesec.com l'url est reécrit en sitesec
 <img src="./screenshots/41.png" width=450 height=300>

●	Pour éviter les érreurs FORBIDEN à cause de l'absance du fichier ou plutôt de lien symbolique de rewrite dans mods-enabled, nous avons activer le module rewrite.
  ○	Le fichier rewrite est absent du dossier mods-enabled :
 <img src="./screenshots/42.png" width=450 height=300>

●	il fait bien parti des modules apache
 <img src="./screenshots/43.png" width=450 height=300>

●	Activons le module rewrite :
 <img src="./screenshots/44.png" width=450 height=300>

●	On va modifier le fichier de configuration de la page par défaut afin ls
qu’elle pointe vers notre site :
 <img src="./screenshots/45.png" width=450 height=300>

●	Vérifions l'effectivité de notre config dans sites-enabled/:
 <img src="./screenshots/46.png" width=450 height=300>

●	pour que nos modification soit prise en compte, nous devons redémarrer notre serveur:
 <img src="./screenshots/47.png" width=450 height=300>
### ●	Configuration des Packages nécessaires :

●	Installation des Packages nécessaires:
○	le package libapache2-mod-auth-kerb
 <img src="./screenshots/48.png" width=450 height=300>

●	le package krb5-user
 <img src="./screenshots/49.png" width=450 height=300>

●	on doit configurer le royaume :
○	le royaume:
 <img src="./screenshots/50.png" width=450 height=300>
○	le serveur kerberos:
  <img src="./screenshots/51.png" width=450 height=300>
○	le serveur administrateur du royaume:
  <img src="./screenshots/52.png" width=450 height=300>


### ●	Préparation du fichier keytab :
Nous devons extraire le principal du service de la base de données des principaux KDC dans un fichier keytab.
●	Dans la machine KDC, on va générer le fichier keytab:
 <img src="./screenshots/53.png" width=450 height=300>
  <img src="./screenshots/54.png" width=450 height=300>
 
●	Vérifions que notre keytab a été créer:
  <img src="./screenshots/55.png" width=450 height=300>
●	Envoyez le fichier keytab de la machine KDC à la machine du serveur: ! Nous devons avoir openssh-server package installé sur le serveur: 
  <img src="./screenshots/56.png" width=450 height=300>
●	Vérifiez que le principal du service a été extrait avec succès de la base de données KDC:
○	Répertorier la liste de clés actuelle: ktutil: list
○	Lire un keytab krb5 dans la liste de touches actuelle ktutil: read_kt /home/orphe/Bureau/krb5.keytab
○	Répertorier à nouveau la liste de clés actuelle ktutil: list
  <img src="./screenshots/57.png" width=450 height=300>
  <img src="./screenshots/58.png" width=450 height=300>
### ●	Configuration du site:
○	Modifions légèrement la configuration de notre site dans le fichier 001-sitesec.conf, afin d'intégrer l'authentification kerberos. Ajouter ce qui suit dans <VirtualHost *:80> ...... </VirtualHost> .
  <img src="./screenshots/59.png" width=450 height=300>


●	LE Client
Notre serveur est bien accessible depuis la machine cliente:
  <img src="./screenshots/60.png" width=450 height=300>
## Préparation de Kerberos

●	on installe krb5-user:
  <img src="./screenshots/61.png" width=450 height=300>
○	Le royaume:
  <img src="./screenshots/62.png" width=450 height=300>
 

●	kerberos server:
		   <img src="./screenshots/63.png" width=450 height=300>

●	serveur administrateur :
 
  <img src="./screenshots/64.png" width=450 height=300>

## Authentification du client
●	Dans la machine cliente, vérifiez les informations d’identification mises en cache:
   <img src="./screenshots/65.png" width=450 height=300>

●	Initialisez ensuite l’authentification de l’utilisateur:
 		  <img src="./screenshots/66.png" width=450 height=300>

●	et vérifiez le ticket d’octroi de ticket (TGT):
   <img src="./screenshots/67.png" width=450 height=300>

Pour accéder au serveur après l'implémentation de Kerberos :
●	Depuis Le Terminal:
   <img src="./screenshots/68.png" width=450 height=300>

●	Depuis Le Serveur Web :
   <img src="./screenshots/69.png" width=450 height=300>

●	from another unauthorized user
   <img src="./screenshots/70.png" width=450 height=300>
