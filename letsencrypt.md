# HTTPS sur votre site web ?
Pour sécuriser votre site web et vous assurez que toutes les données échangées entre vos clients et votre serveur sont chiffrées, vous allez avoir besoin d'un certificat.

Un certificat c'est quoi ? 

C'est un document numérique qui contient au minimum : 
* une clé publique
* des informations d'identification, telles que le nom, la localisation de l'entreprise, l'adresse électronique, etc
* une signature électronique, l'entité signataire étant la seule autorité permettant de prêter confiance (ou non) à l'exactitude des informations du certificat

Il existe différents formats de certifiicat. Les deux plus utilisés sont X.509 et OpenPGP.

# Les acteurs d'un échange sur HTTPS

Lorsqu'un internaute arrive sur votre site en HTTP, c'est simple : 
* le client émet une requête HTTP vers le serveur
* le serveur interprète la requête et la retourne au client

Facile ;) !

Mais voilà, en HTTPS, les choses ne sont pas si simples. La page de [wikipedia](https://fr.wikipedia.org/wiki/Autorit%C3%A9_de_certification) est plutôt claire sur le sujet. En voici les grandes lignes :
* Les navigateurs possèdent nativement la liste des autorités de certifications et leurs certificats respectifs
* Lorsqu'un client souhaite se connecter en HTTPS, le serveur lui transmet son certificat, qui contient le CSR (demande de certificat) chiffré par l'autorité choisie par le webmaster.
* Le navigateur, possédant les certificats des autorités, s'assure qu'une des clés publiques permet bien de déchiffrer le CSR. Il s'assure ainsi que le certificat est bien issu d'un tiers de confiance.
* D'autres vérifications sont effectuées par le navigateur telle que la date d'expiration du nom de domaine
* Le navigateur initie une connexion SSL en utilisant la clé publique récupérée dans le certificat
* Le serveur reçoit chiffrée qu'il déchiffre à l'aide de sa clé privée
* Le serveur interprète la requête HTTP, construit une réponse qu'il chiffre à nouveau avec sa clé privée et l'envoie au client
* Le client déchiffre la requête avec la clé publique ? PB : tout le monde peut déchiffrer le contenu de retour


