# Connais ton protocole TCP !

Que vous bossiez dans le web, sur des micro-services en backend, ou sur de l'administration de serveurs de mail, vous pouvez considérer que tout ce qui transite entre les mahcines passe sur TCP.
Du coup, pas besoin de faire un dessin pour expliquer l'intérêt de maitriser un temps soit peu ce qui se passe sous vos trames HTTP, SMTP ou vos channels RabbitMQ.
Cela vous permettra d'identifier et comprendre des comportements qui ne sont pas de votre ressort en tant que développeur (oui c'est souvent utile de se dédouaner) ou d'implémenter votre propre protocole sur TCP.
L'exemple le plus fréquent chez nous est la coupure de connexion TCP par nos proxy de facade (voir section proxy). La connexion est coupée, et le serveur en attente d'une trame cliente, n'est pas informé de la coupure de la connexion.
Le seul moyen d'en être informé, c'est d'écrire soit même sur le descripteur de fichier ouvert et d'attendre éternellement (jusqu'au timeout) le ACK client qui ne viendra pas. Dans ce cas de figure on peut considérer que la connexion est é rétablir.
Un des soucis majeurs de TCP est l'absence d'action (flag tcp) ayant pour rôle de vérifier l'intégrité de la connexion. Le protocole SMPP, pour Short Message Peer to Peer, que l'on utilise pour envoyer des SMS vers les opérateurs, fournit un mécanisme appelé enquire_link pour vérifier que l'hôte distant est toujours connecté.
TCP est efficace et omniprésent mais souffre de quelques limitations. 
Ce qui suit est un résumé de ce qu'il fauit en connaitre en tant que développeur, pas plus. 
Si vous voulez en savoir plus, jetez un oeil aux RFC ou passez une certif d'ethical hacker.


TODO

