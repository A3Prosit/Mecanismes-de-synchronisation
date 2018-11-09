
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
