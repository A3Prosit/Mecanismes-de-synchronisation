# Prosit 3: Mécanisme de synchronisation

# Team :

Animateur : Mehdi

Secrétaire : Max

Scribe : Nico

Gestionnaire : Étienne

# Mot clés (rien à définir) :

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

# Contexte :

Quoi ?

- Les machines ne se synchronisent pas et ralentissent la production

- Réparer la chaîne de montage

- Des machines sur une chaine d’assemblage

Comment ?

- En synchronisant les machines

Pourquoi ?

- Gagner plus d’argent

- Ne se synchronisent pas

# Contraintes :

- Temps : 2,5 jours

# Problématique :

- Comment synchroniser les machines pour gagner en productivité

- Comment gérer la synchronisation des machines

- Comment optimiser la production en utilisant des mécanismes de synchronisation ?

# Généralisation :

- Optimisation

- Synchronisation

- Gestion

# Hypothèses :

- Les IPC sont un mécanisme de synchronisation (Gilly)

- Pour une bonne synchronisation il faudrait des données constantes (mais pas trop de données) et régulières (Émilien)

- Pour synchroniser les machines il faudra regarder leur temps d’exécution (Flo)

- Il faut regarder le trajet (ordre) des matières premières pour améliorer la synchronisation. (Jon)

- Il faut rendre en compte le fait que le traitement est séquentiel (Jon, reformulé)

- On peut utiliser des pipes pour organiser l’ordre des tâches (Étienne)

- On va utiliser le DP observer/observable en Java (Fantou)

- Des threads non synchronisés ne peuvent pas communiquer entre eux (Maz)

- On peut utiliser des locks pour que les threads ne se disputent pas les ressources (Max)

# Plan d’action

**Études**

- IPC (linux et windows)

- Concurrence des processus

- Gestion des threads en Java

- Accès critiques (locks, sémaphores, mutex)

- Synchronisation

**Réalisation**

- Corbeille

## Composantes - IPC (Interprocess Communications) :

Mécanisme, où l'OS permet à plusieurs processus de communiquer entre eux.
- Synchronisation de leurs actions
- Management des données partagées

La communication peut-être de deux types :
- Parent/Fils (lié sur un seul process)
- Deux processus différents




**Données - Mémoire partagée**
![](https://www.tutorialspoint.com/inter_process_communication/images/shared_memory.jpg)
``` C
#include  <sys/ipc.h>  
#include  <sys/shm.h>  
int shmget(key_t key,  size_t size,  int shmflg)
```

**Informer - Message Queue**
- Comprendre (Réception, accessibilité ressources)
- Communiquer avec des petits formats de messages

**Protéger - Sémaphores**
- Si 2 en modification ==> Nécessité de protéger
- Binary (Lock/Unlock)
- Coutning Sempahore (Allouer arbitrairement)

**Communiquer - Signaux**
![](https://www.tutorialspoint.com/inter_process_communication/images/signal.jpg)
### Windows :
- Clipboard : Dépôt central pour les données applicatives, où les données sont dans un format standard
- COM : Principe du OLE : Un document peut-être traité par plusieurs applications puis retourné à son état initial (données liées...)
- Data Copy : Utilise le WM COPYDATA, envoyer des infos sur une autre appli, l'envoyeur ne peut pas modifier les références mémoires par des pointeurs.
- DDE : Protocole pour échanger des données des données dans une grande variété de formats.
- File Mapping : Traiter un fichier comme si c'était un bloc mémoire dans l’adresse du processus. Il peut utiliser un simple pointeur pour visualiser/modifier les contenus d'un fichier.
- Mailslots : Fournit une communication à sens unique, le porcessus l'envoyant devient un *mailslot server*, les autres sont donc des clients. L'échange de données se fait donc via des messages.
- **Pipes :** 
 (related):
- Père/Fils
- 1 process écrit, et un autre lit
- 1 pipe for one-way communication
- 2 pipes for bi-directionnal communication
![](https://www.tutorialspoint.com/inter_process_communication/images/pipe_with_one.jpg)

- **Named Pipes ou FIFO** :
- Single named pipe (one way + bi-directionnal) communication
- C'est un pipe assez spécial

- RPC : Supportant automatiquement les données de conversation entre différentes architectures matérielles
- Windows Socket : Protocole indépendant des interfaces, pouvant être utilisé comme fichier, supportant les communications.


### Linux :



## Concurrence des processus
⇒ Recherche de diminution de temps de latence
⇒ Terminaisons (arrêts)
⇒ Variables partagées
==> Sections critiques
==> Ordonancement + Etats 
⇒ Lectures / ecritures (ne pas modifier une donnée)
⇒ Syncrhonisation (wait & signal)
⇒ Sémaphore & "producteur / consommateur"
## Gestion des threads en Java
- Bibliothèques de  processus POSIX (The Portable Operating System Interface)
- Interface "Runnable" qui va contenir la méthode run : Appellée au lancement d'un processus
- Constructeur des processus "Thread" va prendre un objet de la classe Runnable et le transformer en ps
- On utilise la méthode "start" pour démarrer un processus
- Fonction "interrupt" qui va lever l'exception "InterruptedException" pour arrêter un ps
- Fonction "sleep" pour mettre en pause pendant un certain temps (ms)
- **Une instruction est atomique si elle ne peux pas être interrompue pour laisser l'exécution à un autre processus, manipulant les mêmes ressources critiques**  : Java ⇒ Lecture + écriture de variable sont entièrement atomiques
- synchronized : contrôler l'accès à une section critique (#atomique) ⇒ Verroux
- Des classes comme FIFO [...} pour file d'attente avec des fonctions ajouter() ou supprimer()
- Des classes comme sémaphores
## Accès critiques (locks, sémaphores, mutex)

- Méthode courament utilisé pour bloquer des accès
- Vient de Edsger Dijkstra 
- Principe du "diner des philosophes" https://fr.wikipedia.org/wiki/D%C3%AEner_des_philosophes
	- Cinq philosophes autours d'une table
	- Chacun à un plat de spaghetti devant lui
	- A gauche de ce plat se trouve une fourchette
	- Trois états possible pour le philosophe :
		- Penser
		- Etre affamé (sinon famine)
		- Manger pendant un temps déterminé et fini
	==> Trouver ordonancement pour éviter famine
- Fonction de synchronisation de bas niveau
- Variable Booléen sur deux opérations atomiques :
	- prendre (test de la valeur booléenne)
	- libérer : réveille un processus en attente
- Les sémaphores ne sont pas attachés à un verrou, ce sont des variables d'états
- On peut les utiliser pour programmer un verrou

**Mutex** :
- Primitive de programmation pour éviter que des ressources partagées d'un système soient utilisées en même temps
- Doit savoir si les processus sont busy ou en wait 
- Ex :
	- Algorithme de Dekker
	- Algorithme de Peterson

## Synchronisation
- Un procesus qui attend consomme du temps processus
	- Résoudre avec un sleep ou un yield dans la boucle
