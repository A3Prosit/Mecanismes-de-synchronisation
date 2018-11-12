
# Prosit 3: Mécanisme de synchronisation

## Team :

Animateur : Mehdi

Secrétaire : Max

Scribe : Nico

Gestionnaire : Étienne

## Mot clés (rien à définir) :

- Synchroniser

- Travailler en parallèle

- Processus

- Chaîne d’assemblage

- Périodiquement

- En même temps

- Produits perdus

- Trop de produits

- Quais vides

- Pris en charge rapidement

## Contexte :

Quoi ?

- ~~Les machines ne se synchronisent pas et ralentissent la production~~

- ~~Réparer la chaîne de montage~~

- Des machines sur une chaine d’assemblage

Comment ?

- En synchronisant les machines

Pourquoi ?

- ~~Gagner plus d’argent~~

- Ne se synchronisent pas

## Contraintes :

- Temps : 2,5 jours

## Problématique :

- ~~Comment synchroniser les machines pour gagner en productivité~~

- ~~Comment gérer la synchronisation des machines~~

- Comment optimiser la production en utilisant des mécanismes de synchronisation ?

## Généralisation :

- Optimisation

- ~~Synchronisation~~

- ~~Gestion~~

## Hypothèses :

- Les IPC sont un mécanisme de synchronisation (Gilly)

- Pour une bonne synchronisation il faudrait des données constantes (mais pas trop de données) et régulières (Émilien)

- Pour synchroniser les machines il faudra regarder leur temps d’exécution (Flo)

- Il faut regarder le trajet (ordre) des matières premières pour améliorer la synchronisation. (Jon)

- Il faut rendre en compte le fait que le traitement est séquentiel (Jon, reformulé)

- On peut utiliser des pipes pour organiser l’ordre des tâches (Étienne)

- On va utiliser le DP observer/observable en Java (Fantou)

- Des threads non synchronisés ne peuvent pas communiquer entre eux (Maz)

- On peut utiliser des locks pour que les threads ne se disputent pas les ressources (Max)

## Plan d’action

## **Études**

### IPC (linux et windows)

windows fournit des mécanismes pour faciliter la communication et le partage de données entre les processus, on appelle ces mécanismes les IPC (interprocess communication). Certains IPC facilitent la division du travail (division of labor) entre plusieurs processus spécialisés, d'autres entre plusieurs ordinateurs sur un réseau.

Pour déterminer de quels mécanismes notre application a besoin on se pose les questions suivantes:

- A-t-elle besoin de communiquer avec d'autres applications sur d'autres ordis sur le réseau ou justes celles en local ?
- si elle communique avec d'autres PC sont ils sur le même OS (32/64 inclu) ?
- l'utilisateur doit-il spécifier les aplications avec lesquelles communiquer ou l'application les trouve-elle toute seule ?
- l'applicatin doit-elle avec beaucoup d'app de façon générale, copier-coller entre les app par exemple ou juste limiter/restreindre les interactions et uniquement avec certaines app ?
- Les perfs sont elles un aspect critiques ? Les IPC causent certains délais
- l'app a-t-elle une interface ou est-ce une appli console ? certains IPC nécessitent une interface graphique (gui) 


IPC supportés par windows:

- presse-papier
- COM
- Data copy
- DDE
- file mapping
- mailslots
- piepes
- RPC
- windows socket

**presse papier**
couper/copier place les données selectionnées dans le presse-papier selon un format standard ou défini par l'appli. Une autre appli peut récup les données et peut choisir quel format si plusieurs sont dispo. Cet IPC est simple et à pour seule difficulté de se mettre d'accord sur le format, il fonctionne en local et sur réseau. Toutes les applis devraient supporter le presse-papier pour les formats qu'elles comprennent

**COM**
les applis qui utilisent OLE (Object Linking & Embedding)  gèrent des documents composés, cad des docs faits à partir de données d'autres applications. OLE propose des services facilitant l'appel d'une app à l'autre pour l'édition de donnée. ex: word peut intégrer une feuille de calcul excel.
La fondation d'OLE est le Component Object Model (COM) un composant logiciel utilisant COM peut communiquer avec une variété d'autres coposants. LEs compo interagisent selon le modèle objet-client 


**Data Copy**

Data coy permet à une app d'envoyer des infos à une autre app en utilisant le message WM_COPYDATA. Cette méthode requiert la coopération entre émetter et récepteur. L'app réceptrice doit connaitre le format d'nformation et être capable d'identifier l'émetteur, l'émetteur ne peut pas modifier la mémoire référencée par des pointeurs

**DDE**

DDE est un protocole qui permet aux app d'échanger des données sous une variété de formats. il peut être utilisé pour des échanges ponctuels ou continus. LE format de donnée est le même que pour le presse papier, on peut également défnir des formats sur mersure. On peut voire DDE comme une extension du presse papier qui ne sert généralement qu'une fois. DDE est généralement inité par commande utiliateur mais continue souvent sans intervention. Il est cependant moins efficace que certaines nouvelles techno
Fonctionne également à travers le réseau.
**File mapping**

Le file mapping permet a un processus de trater le contenu d'un fichier comme s'il s'agissait d'un bloc mémoire de sous espace d'adressage. Il peut utiliser des opérations de pointeur pour examiner et modifier le contenu. Quand 2+ processus accèdent au même fichier chauqe processus à son propre pointer vers son propre espace mémoire. Ils doivent utiliser un objet de sychro comme u sémaphore pour empêcher la corruption de données.
On peut utilisé le file mapping pour créer de la mémoire partagé nommée entre des processus en spécifiant un fichier de swap, tous les processus peuvent alors y accéder
LE file ampping est efficace et offre de la sécurité pour prévenir la corruption de donnée. Il ne peut être utilisé que de façon locale, pas sur le réseau

**Mailslot**

Communication unidirectionnelle, un procédé qui créé un mailslot est un 'serveur mailslot', les autres procédés, les 'clients mailslot', envoient des messages au serveur en les écrivant dans son mailslot. LEsmessages entrant sont toujours concaténés au mailslot qui le sauvegarde jusqu'à ce que le processus le lise. Un processus peut être à la fois client et serveur, une communication bi-directionnelle est donc possible
Un client peut envoyer un message à un mailslot en local, sur le réseau ou sur tous les mailslots ayant le même nom sur un réseau donné. Les broadcast ne peuvent pas faire plus de 400 bytes, et ceux avec un destinataire ne sont limités que par la taille limite spécifiée par le serveur

**Pipe**
2 types de pipes:
- anonymes: pour transférer des données entre processus (related) liés. ex: rediriger l'entrée/sorite d'un processus fils pour communiquer avec le père. Pour les échanges bi-directionnels ils faut créer 2 pipes anonymes. ils ne peuvent pas être utilisés à travers le réseau ou entre processus non liés (unrelated)

- només: pour transférer des données entre processus non liés ou sur des ordis différents. par exemple un processus serveur créé un pipe nomé avec un nom connu ou qu'il va communiquer à son client.. Un processus client connaissant le nom du pipe peut l'ouvrir, une fois connecté le client et le serveur peuvent échanger des donnée en lisant/écrivant sur le pipe

**RPC**

Remote procedure call prosit 1 (ou 2?) 

Le RPC fourni par Windows est conforme à l'Open Software Foundation (OSF) Distributed Computing Environment (DCE), cad, les app utilisant RPC peuvent communiquer avec des apps d'autres OS supporttant DCE. RPC supporte automatiquement (c'est dans le protocole) la conversion de donnée entre les architectures

**Windows socket**

interface "protocol-independent", il prend avantage des capacités de dcommunication du protocole sous-jacent (underlying)
In Windows Sockets 2, a socket handle can optionally be used as a file handle with the standard file I/O functions.

Windows Sockets are based on the sockets first popularized by Berkeley Software Distribution (BSD). An application that uses Windows Sockets can communicate with other socket implementation on other types of systems. However, not all transport service providers support all available options.

Windows Sockets is a protocol-independent interface capable of supporting current and emerging networking capabilities.

### Concurrence des processus

### Gestion des threads en Java

relation happens-before: 
pour s'assurer qu'un event est vu par un autre avant qu'il n'exécute son action. ex: s'assurer qu'un print ai vu qu'une valeur ai changé. Cette relation peut être créé par:
- synchronized (construct)
- volatile (construct)
- Thread.start()
- Thread.join()
- une suite d'action dans un même thread (obviously)
- tout ce qui se passe avant un unlock se passe avant le prochain lock
-  écrire dans un volatile se fait avanttoute lecture (c'est obvious ou il y a une subtilité a comprendre ?)

Les métodes de java.util.concurrent garantissent cette relation:
- les action d'un thread avant d'ajouter un objet à une collection concurente se passent avant qu'un autre thread n'y accède
- les actions d'un thread avant de soumettre un Runnable à un Executor se passent avant son execution. De même pour soumettre des Callables à un ExecutorService.
- les actions d'un Future se passent avant le Future.get() (oui je sais...)
-  les actions avant de "relacher" une synchronizer method come Lock.unlock, Semaphore.release et CountDownLatch.coutDown se passent avant la prochaine action d'acquisition


**Thread pool**
aka replicated worker aka worker-crew model: design pattern utilisé pour effectuer ds exéctions concurentes.
un thread pool maintient plusieurs threads en attente de tâches d'exécution concurente par le  programme de supervision (ordonanceur ?)? En maintenant le thread pool on économise le temps de destruction et création des threads courts. Le nombre de threads dispo dépends des ressources allouées au prog: proco parallèles, nombre de coeurs, mémoire, socket réseau 

en résumé:
- créer trop de thread gaspille les ressources et coute du temps pour créer des threads inutiles
- détruire trop de threads requiert plus de temps car on doit les re-créer après
- ne pas créer les threads assez vite résulte en une mauvaise expérience utilisateur
- détruire des threads trop lentement peut créer de la famine pour les autre processus


**Fork/join**

implémentation de l'interface ExecutorService qui permet de prendre avantage de multiples procos. Elle est designé pour les tâches pouvant être découpées en petit morceaux de façon récursive. 
il distrivue les taches dans un thread pool, il utilise un mécanisme de "work stealing" vol de travail. Un worker qui n'a plus rien a faire peut "voler" le travail d'autres threads occupés
la classe centrale esr ForkJoinPool qui peut exécuter des processus ForkJoinTask

**intinsic lock/monitor lock**
assure l'accès exclusif a un objet et la relation happens-before
on acquiert la ressource puis on la libère
les méthodes synchronized récupère le lock de l'objet de la méthode et le libère quand elle retourne

synchronized statements : doit spécifier l'objet a lock , dangereux

synchro réentrante  ( Reentrant Synchronization): une thread ne peut acquérir un lock possédé par un autre thread mais peut réacquérir un lock qu'elle possède. Si une méthode synchro appelle une autre méthode synchro (in)dirrectement sans ça on devrait faire attention a ne jamais appeller du code syncro depuis une méthode synchro

### Accès critiques (locks, sémaphores, mutex)



### Synchronisation

**Réalisation**

### Corbeille
