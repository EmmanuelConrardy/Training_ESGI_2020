# Training_ESGI_2020

### Prototype :
Création d'un registre d'objet préconçu prêt à être cloné.
on récupère le clone et on le fait varier si nécessaire.
Utile sur des objets avec une construction complexe, parfois on a seulement besoin du même objet, mais avec quelques différences.

Utilisation personnelle : dans mes tests d'intégration, je clone des contextes de panier et fait juste varier les prix et/ou articles pour activer les règles métiers cible.
Sinon on doit recréer un contexte de zéro, c'est coûteux et peu maintenable.

### Factory method :
l'héritage dans toute sa splendeur, son intention est de desservir un objet via une methode, le retour est abstrait ce qui permet de découpler le créateur de nos objets concret. 
Cette approche respecte le principe open/close et single responsability.

Usage personnel : quand je dois créer un objet dérivé par exemple pour une spécificité d'un pays les adresses espagnoles demandent plus de champs que les adresses françaises (domaine e-commerce internationale).

J'aime bien sa variation "static factory method" que l'on retrouve souvent dans les "value object"

### Template method :
Comme la factory method; mais son intention est différente, on va chercher des étapes, un workflow et l'abstraire cela nous permet de nous abstraire des implémentations de nos classes concrètes.
- https://softwareengineering.stackexchange.com/questions/340099/factory-method-is-a-specialization-of-template-method-how
Si on souhaite modifier le workflow ou les étapes c'est facile !

usage personnel : on a un template sur le workflow du paiement, il varie en fonction du contexte client et panier.

### Bridge :
- On souhaite séparer deux concept ! On a tendance à tout mettre au même endroit. On sépare les abstractions et chacun fait sa vie :).

Quand on regarde autour de nous, les objets qui nous entourent ont leur "concept", dans le code c'est la même chose.
```c#
class Appartement
 bool CanSleep()
 bool CanEat()
 int PriceAppartement
```
devient

```c#
class Appartement
 Price - int amount
 Kitchen - CanEat
 Bedroom - CanSleep
```
On aura le loisir d'implémenter une cuisine Ikea, Conforama,... sans gêner nos autres objets.
principe open/close respecté et single responsabilité aussi !

usage personnel : Souvent quand mes classes ont plusieurs responsabilités,
je me demande si je ne peux pas faire un pont avec une nouvelle abstraction qui portera une partie des méthodes/comportements.

### Singleton :
les singleton je passe ma vie à les chasser et les détruire donc n'en faite pas ou définisser le cycle vie singleton au lancement grâce à l'inversion de dépendance comme ça on peut au moins les tester.
Cauchemar personnel : Un objet "session" singleton appelé partout, intestable, hyper couplé, ... dans un premier temps on va le wrapper
pour le rendre testable puis on adaptera le design pour changer la structure de nos classes pour éviter d'avoir besoin de passer l'objet globale,
mais juste ce qu'on a besoin de passer en paramètre. 

### Observable :
faire communiquerà sens unique deux structures d'objet entre eux ça permet de synchroniser/activer des modules distants de nos applications.

usage personne: On a un bel observable qui s'active quand notre panier est modifié (changement de quantité par exemple) pour notifier les re-calcul des prix et promotions.
Cela nous permet de garder nos Aggregats (concept DDD) pour qu'ils restent consistent. En gros nos "observer" garantissent la cohérence de notre panier.

### Iterator :
On souhaite faire des parcours sur une structure et l'abstraire pour ne pas dépendre des implémentations, merci Iterator. on respecte le principe d'open/close.
On le manipule plus simplement qu'un simple "foreach".
usage personnel: il me semble avoir eu besoin de l'implémenter, il existe une struture toute faite en C# Collection / IEnumerator  

### Composite
Mon préféré, il permet de rassembler plusieurs objets semblables en un objet et en tirer les responsabilités désirées.
On a tendance à abuser des "List" et des "foreach" ce qui n'est pas extensible, le composite quant à lui est limité par son interface.

Comme on implémente chaque méthode de l'interface dans les objets tant qu'ils sont semblables ça va, mais s'ils commencent à trop se differencier, on peut briser le principe de liskov.

### Decorator
Le moyen d'ajouter, supprimer ou modifier du comportement à l'exécution.
Je l'utilise souvent dans un workflow, on a un objet à "consolider" en fonction du contexte du système.
Notre objet traverse notre workflow et "s'équipe" ou se "décore" au fur et à mesure du workflow

- Tasse -> + expresso ? oui -> -> + sucre ? non -> + lait ? oui -> + touillette ? à pas de sucre donc non -> "expresso au lait" 

Ça me fait un dizaine de classes sinon si je devais créer toutes les classes en statique j'en aurais pour 30-40 avec toute les combinaisons de taille de café, avec sans sucre, lait, ....

### Adapter
C'est ton meilleur ami dans un contexte legacy on a tendance à se lier avec des dépendances externes qui ne sont pas forcément compatibles avec notre tout notre système.
On souhaite créer une surcouche plus "simple" du coup on adapte un peu ce qui nous gêne dans notre legacy.
Le scénario -> Ce bon vieux service pas maintenable qui calcule les "itinéraires" je l'adapte dans mon nouveau système derrière une interface stylé
Quand je peux, on fait la refacto du service pour rendre l'interface à la place de l'ancienne pas sympa et on enlève l'adapter.

### Mediator
La distanciation sociale mais chez nos objets ou l'ordre et la rigueur dans vos dépendances entre vos objets.
- A tous vos objets : "Votre seul collaborateur c'est le sergent Mediator, il peut tout faire pour vous  si vous souhaitez parler à qqn d'autre il faut passer par lui ! Des Questions !?"
Le médiator ne gère que les flux de communication les "comportements" devraient être encapsulés dans les objets.
C'est tentant de rajouter du comportement dans le médiator mais on risque fort de faire glisser le design petit à petit et avoir un code spaghetti. 

### State
L'état interne de mon objet évolue dans le temps ou en fonction d'un point sensible et ça influe son comportement ?
State est ton ami. Un exemple gourmand est la cuisson d'un steak, il arrive sur la poêle "cru"
après une certaine température et un certain temps, il passe "saignant", puis "bien cuit" et si on le laisse "Cramé".
Et quand on le mange il satisfait plus ou moins nos papilles.

"Cru" -> "Saignant" -> "Bien cuit" -> "Cramé" sont nos états internes.

### Object pool
Le design pour maîtriser le nombre d'instance dans mon système. Peut-être utile dans un système avec des connections limités
On imagine un serveur avec un maximum de 20 connections on va donc créer au maximum 20 instance de Client pour se connecter.
Et il y aura une file d'attente si toutes les connections sont prise, il vaut mieux ralentir votre système que de faire tomber le serveur. 
Certains Hardware peut avoir des contraintes précises l'object pool sera peut-être exactement ce que vous avez besoins.
Sinon on peut économiser la mémoire aussi en recyclant des objets mais c'est un usage de moins en moins pertinent.

### Abstract factory
On aime les interfaces et abstractions pour la souplesse qu'elle offre à notre système pour la rendre orthogonal -> https://en.wikipedia.org/wiki/Orthogonality_%28programming%29.
On va donc abstraire nos objet derrière nos "factory" et on va abstraire nos factory dans une factory
et donc créer une factory de factory. 
Comme ça elles peuvent changer comme elles le souhaitent sans affecter notre système.

Quand on commence à distinguer des familles d'objets c'est peut être le moment de penser à Abstract factory.

### Null Object
- S'intéresser à "null" au pif https://hackernoon.com/null-the-billion-dollar-mistake-8t5z32d6?source=rss
- Un peu de c# https://devblogs.microsoft.com/dotnet/nullable-reference-types-in-csharp/
- Comment c'est géré par cartains langages https://stackoverflow.com/questions/28106234/are-there-languages-without-null

La valeur null englobe trop de possibilités, ça peut être une string comme n'importe quel objet.

Généralement quand une fonction retourne null on devrait rester strict sur le sous ensemble retourné.
Dans notre sou ensemble il y a une représentation de null qu'il faut chercher et on obtient un NullObject.
Il est malheureusement "trop" facile à utiliser et nous sommes tenté d'emprunter ce raccourci, au risque de fragiliser notre système.

### Command
Vous imaginez les ingénieurs du Nucléaire descendre dans les réacteurs pour activer la centrale ? Non ? moi non plus. 
En revanche ils ont un gros bouton "Démarrer" pour le faire et ils peuvent boire leur café tranquillement en regardant les réacteurs se mettre en marche.
Le pattern command c'est pareil, il encapsule tous les comportements vecteur d'action et les rend disponibles par composition.
On a souvent un objet qui enregistre les "command" et offre l'exécution au "client", une calculette, un tableau de bord, un serveur dans un restaurant.
C'est donc une couche d'abstraction permettant de piloter le comportement de notre application.

### Visitor
Pas simple celui là. En gros si pour une raison X ou Y tu ne peux pas ajouter du comportement à un objet tu peux le faire grâce au visitor.
Le "visité" donne accès à ses propriétés publiques pour une utilisation externe.
Par exemple moi je m'en sers dans un but de génération de documentation.
Je visite toute mes classes "importante", puis je génère des graphes et autre documents à partir de leur propriétés par réflexion.
Mais comme ce n'est pas directement lié au métier je n'ai pas voulu le mettre en "dur" des mes classes au risque de les polluer.
Donc elles "Acceptent" voir "tolèrent" mon algorithme mais il doit se faire ailleurs. 
Le visitor permet d'effectuer des opérations de la classe en dehors de cette classe.
Use case -> Health check, Documentations, Passage d'une mise à jour,...

### Proxy
Il parle de lui même de base c'est un passe plat, je l'utilise souvent sur les connecteurs externes de mes applications, pour :

- Du cache -> pour dépendre le moins possible d'api tiers
- Des logs -> si il y a un problème de communication, une exception je peux comprendre rapidement
- Des autorisations -> L'accès à ces api tiers est peut être strict ? Es-tu identifier ?
- Du monitoring -> Temps d'exécution de la requête
- De Lazy load -> Accès à une image, un connecteur coûteux en ressource. 

Tout et n'importe quoi tant que ça se passe avant et/ou après l'appel.

Il permet de regrouper la plupart des détails techniques (logs,..) et de proposer ensuite le service au classe métiers.

### Strategy
"Choisir c'est renoncer" -- André Gide
Vous avez plusieurs algorithmes et vous devez faire un choix entre eux, dans ce cas Strategy est fait pour vous mais attention une fois choisi il faudra assumer ;)
Plus sérieusement, Strategy encapsule une série d'algorithme et il choisira pour vous le plus adapté ou le désiré.
L'analogie des moyen de transport est pas mal :
Pour te déplacer, si tu es riche tu peux prendre un "Uber Comfort", si il n'y pas de grève tu as le Bus, si les pneus de ton vélo son OK, tu peux choisir le vélo sinon par défaut tu as tes pieds : )
Alors que choisis tu ? 

### Façade
On a tendance à oublier ce pattern car nous sommes tous addict à la complexité, "pourquoi faire simple quand on peut faire compliqué".
Et pourtant, souvent il serait pertinent de redéfinir les interfaces de communications de nos objets, car au bout d'un moment la complexité c'est plus vraiment fun.
Façade à la rescousse, Il est là dans un premier temps pour proposer une nouvelle interface. 
Vous pouvez pensez au panneau devant les chantiers, ils promettent un super immeuble 
en attendant derrière il sont en train de tout reconstruire et quand l'immeuble est finis il enlève la façade : )
C'est notre pattern "ami" lors de la reprise de code legacy, il est la quintessence du "i" de solid "Interface Segregation".

### Builder
Justement on parlait de chantiers, souvent on souhaite construire des objets "complexe" et leur instanciations peuvent vite devenir difficile.
El dans la plupart des cas une majorité des propriétés de l'objet ne sont pas obligatoire.
Nous allons alors le construire étape par étape, Builder en action.
Construction d'une commande E-commerce -> Builder
Construction d'un plateau de Jeu -> Builder
Souvent à envisager quand on a un code smell "long list parameter" avec beaucoup de paramètre à null.

### Memento
Utile pour faire la sauvegarde et la restauration d'un état d'un objet.
Personnellement je l'utilise pour sauvegarder la commande du client en base de données pour la restaurer si il se connecte sur un autre device.
Je pense que netflix fait la même chose quand tu passes de l'ordinateur au smartphone en pleine lecture.
Autre use case, quand il y a une exception "grave" on sauvegarde l'état critique dans un log (un dump) et on restaure une version antérieur avec un message d'erreur,
par exemple un "Oups erreur technique nous avons restauré votre commande"

### Flyweight
Le fameux "Poids-mouche" pour moi c'est le level 2 du pattern object pool.
Pour rappel "Object pool" définit un ensemble d'instance réutilisables ce qui permet de contrôler et limiter le nombre d'instance.

Flyweight va un peu plus loins jusqu'à réutiliser les propriétés des objets.
Reprenons l'exemple des "balles" afficher sur un écran, on a au maximum 10 instances afficher, on utilise alors un object pool de 10 instances de "balles".
Sauf que ce qui les diffère c'est la position à l'écran, et que l'on souhaite optimiser la mémoire. 
C'est là que flyweight intervient nous allons une seule instance de "balle" avec la "taille" et la "couleur" en 
propriétés "intrinsèques" et la "position" en propriété "extrinsèque" qui sera enrichie lors de l'appel.

C'est une sorte de mélange entre objet pool et prototype, l'un pour la réutilisation d'instance et l'autre pour l'aspect instance à enrichir.
Au final on s'en sort avec "une instance de balle" et un "tableau de positions" ce qui prend moins de mémoire que 10 instances de balles.
