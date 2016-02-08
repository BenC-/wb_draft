# Connais ton protocole TCP !

Que vous passiez votre quotidien à créer des sites web, développer des micro-services en Java, ou montiez un serveur de mail sur votre raspberry pi, vous avez besoin de connaitre TCP ! De facto, considérez que tout ce qui transite entre vos clients et vos serveurs et entre vos serveurs passe sur la couche TCP.
Pour ceux qui ont séché leurs cours de réseau à l'école, le procotole TCP c'est celui qui se situe entre la couche réseau et la couche applicative en suivant le modèle TCP/IP. La couche réseau, c'est IP, procotole non connecté et dit non fiable (car le contrôle d'intégrité des données et les accusés de réception sont délégués aux couches supérieures). La couche applicative, ce sont les protocoles qui viennent se greffer sur TCP, HTTP, TCP, SSH, SMTP et j'en passe.
Du coup, pas besoin de faire un dessin pour expliquer l'intérêt de maitriser un temps soit peu ce qui se passe sous vos trames HTTP si vous faites du web, SMTP si vous envoyez des emails ou AMQP si vous utilisez un broker de message de type RabbitMQ.
Cela vous permettra d'identifier et comprendre des comportements qui ne sont pas de votre ressort en tant que développeur (oui c'est souvent utile de se dédouaner) ou d'implémenter votre protocole perso sur TCP.
L'exemple le plus fréquent chez nous c'est la coupure de session TCP par nos HAProxy (voir section proxy). La connexion est coupée par le proxy, et le serveur qui est en attente d'une trame cliente, n'est pas informé de la coupure de la connexion.
Le seul moyen d'en être informé, c'est d'écrire sur la socket et d'attendre éternellement (jusqu'au timeout) le ACK client qui n'arrivera jamais. Dans ce cas de figure on peut considérer que la connexion est alors à rétablir.
Un des soucis majeurs de TCP est l'absence d'action (flag tcp) ayant pour rôle de vérifier l'état de la connexion. A l'opposé, le protocole SMPP, pour Short Message Peer to Peer, utiliser dans le transfert de SMS vers les opérateurs, fournit un mécanisme appelé enquire_link pour vérifier que l'hôte distant est toujours connecté.
TCP est efficace et omniprésent mais souffre de quelques limitations. 
Ce qui suit est un résumé de ce qu'il faut en connaitre en tant que développeur, pas plus. 
Si vous voulez en savoir plus, jetez un oeil aux RFC ou passez une certif d'ethical hacker.

## Trame, paquet, datagramme ?
J'admets, on se perd un peu dans cette terminologie et on a tendance à tout appeler une trame...
La plupart des définitions sont parfois imprécises. Un paquet désigne génériquement un bloc de données composé d'une entête et d'un contenu utile. Ensuite, les paquets ont des noms différents selon la couche du modèle OSI :
- Les messages sont créés par la couche application (HTTP, FTP, SSH)
- Les segments sont créés par la couche transport (TCP)
- Les datagramme sont créés par la couche réseau (IP)
- Les trames sont créés par la couche liaison (Ethernet)
Mais bon, dans la réalité les gens parlent quand même de trame TCP...

## Alors ? De quoi elle se compose ma trame TCP ?
Une trame TCP c'est une entête et un contenu. 

L'entête est toujours de longueur variable et contient tout plein de champs dont un port d'origine et un port cible. Le reste, on s'en fout. En fait non, ya juste un truc important, ce sont les bits de contrôle. Ce sont des flags binaires qui définissent le "rôle" de la trame. On dénombre 6 bits de contrôle qui peuvent prendre la valeur 0 ou 1. 
- SYN: Permet d'ouvrir une connexion
- ACK: Permet d'accuser réception d'une trame
- RST: Demande la réinitialisation de la connexion si une erreur se produit
- FIN: Permet de fermer une connexion
- PSH/URG: Permet de prioriser des segments TCP

Le contenu se compose de données binaires compréhensibles par les couches applicatives supérieures. 

## Les états d'une socket TCP

Il faut ensuite connaitre les 10 états possible d'une socket TCP, ça peut sembler emmerdant à apprendre mais ça vous permettra de briller auprès de vos admins sys... ;)

LISTEN
Le port est en attente d'une demande de connexion.
SYN-SENT
Le client à envoyé une trame SYN et attend de la réponse du destinataire après une demande de connexion.
SYN-RECEIVED
Attente de la confirmation de l'acquiescement d'une demande de connexion après avoir reçu la demande de connexion et renvoyé un premier acquiescement.
ESTABLISHED
Représente une connexion ouverte, prête à transmettre et recevoir des données.
FIN-WAIT-1
Attente d'une demande de fin de connexion du TCP distant. Ou un acquiescement de la demande de fin de connexion préalablement envoyé.
FIN-WAIT-2
Attente d'une demande de fin de connexion du TCP distant.
TIME-WAIT
Attente d'une durée suffisante pour être sûr que l'hôte TCP a reçu l'acquiescement de sa demande de fin de connexion.
CLOSE-WAIT
Attente d'une demande de fin de connexion venant d'un utilisateur local.
CLOSING
Attente de l'acquiescement d'une demande de fin de connexion d'un hôte TCP.
CLOSED
Représente un état où il n'y a pas de connexion.

## Le scénario classique

Regardons le mécanisme TCP avec l'exemple d'une connexion HTTP.
Lorsque vous tapez http://www.google.fr dans la barre de votre navigateur, qu'est ce qui se passe ?
Il y a d'abord un mécanisme de résolution d'adresse qui ne n'étudierons pas ici (voir section DNS).
Puis commence l'établissemt de la connexion TCP.

Tout ça se passe en 5 étapes

1. SERVEUR: Ma socket est dans l'état LISTEN, prête à recevoir une demande de connexion
2. CLIENT: J'envoie un SYN au serveur et passe dans l'état SYN-SENT
3. SERVEUR: Je recois le SYN, répond un SYN/ACK et passe dans l'état SYN-RECEIVED
4. CLIENT: Je recois le SYN/ACK, répond un ACK et passe dans l'état ESTABLISHED
5. SERVEUR : Je recois le ACK et passe dans l'état ESTABLISHED

Ensuite, débute le transfert de données. Typiquement, un échange de trame HTTP.
Lorsque l'échange est terminée, la rupture de connexion se passe encore en 5 étapes.

1. SERVEUR: J'envoie une trame FIN au client et passe dans l'état FIN_WAIT-1 
2. CLIENT: Je reçois la trame FIN

Lorsque la connexion est terminée, le client (ou le serveur) demande la fin de la connexion.

souhaite récupérer une ressource auprès d'un serveur HTTO

