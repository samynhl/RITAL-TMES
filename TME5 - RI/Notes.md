## PDF 01 : Extraction d’information
- Extraction d’information : définition
    - Compréhension des textes
    - vise à extraire d'un texte donné des éléments pertinents qui répondent à un besoin d’information (Texte => Formulalire rempli)
- Niveaux d'analyse utilisés (ou pas)
    - La machine sait faire:
        - Morphologie : stemming, tagging (savoir ce qu'est un verbe, racine etc)
        - Syntaxe : Sujet, objet, verbe
        - Entités nommées : Qsq est important dans le texte indép de la structure du texte
    - Ce que la machine ne sait pas faire: Ensemble de règles qu'il faut pour la machine, on peut par contre jamais donner tout les règles qu'on a dans la tête:
        - Sémantique : sujet et objet => Décrire le sujet de la conversation
        - Mécanisme d'inférence

## PDF 02 : Réconnaissance d'entités nommées:
2. Définition:
Unités lexicales particulières : noms de personners, noms d'orga etc
3. But  : Extraire les bons bouts de texte puis de les classer
6. Wikidata : tout ce qui a sur wikipedia, mais structurée, base de connaissances énorme.
Normalisation des entités detectés :  samedi (ql date de l'année)
7. Décider de la granularité : rien qui empeche du niveau de la granularité
- Intérêt des entités nommées:
    - Sert à aider à l'indexation **regrouper les entités nommées** : 
    Au lieu de coder Samy seul, Nehlil seul, coder Samy Nehlil à la fois
    Codage des ngrams peut exploser rapidement.
    - Les entités nommées ne se traduisent pas
    - Se sont des séquences de mots => RNNs
- Modèles en séquences
    - Format BIO : Begin Inside O an entity
        Le O
        joueur O
        de O
        tennis O
        américain O
        John B-PERS
        McEnroe I-PERS
        a O
        déclaré O
        samedi B-DATE
        sur O
        ESPN B-ORG
        que O
        Gaël B-PERS
        Monfils I-PERS
    - Format IO : Inside O an entity, on perd une classe (plus facile)
    Ne peut pas représentter le cas ou deux entités se suivent sans une virgule ni un autre séparateur : 
    I-PERS I-PERS I-PERS
12. Modèles en séquences:
    - RNNs : on rajoute une couche crf pour expliciter les contraintes
    - Représentation contextuelles : BERT
    - Span Based : Exhaustive Biaffine
### Comment on faisait avant ?
- Utilisation du feature eng : Réfléchir à tout ce qui peut être utile pour la tache de la classification => Construire petit  à petit les attributs (features ou traits) de notre séquence de mots.
feature eng : Collecte à la main (se conçoit manuellement et se code pour les écrire, contrairement aux RNs, ce travail de fe se fait automatiquement) tout les traits qui peuvent aider dans la tache de classification.
- La classe des mots précédents et suivant ont de l'importance.
- Etiquettes morphosyntaxiques (POS)

RNs : On lui passe tous les données, réalise une représentation latente des données

### Introduction aux réseaux de neurones
28. Un réseau statique : Entrée figée à une certaine direction
Une fois que le réseau est entrainé, on ne peut plus changer la dimension de l'entrée ni de la sortie
29. Exemple : 
### Introduction aux réseaux de neurones récurrents
34. Gérer des séquences de tailles variables
35. Entrée d'une unité : mot + Sortie précédente
- Initialisation : par des 0 ou des vecteurs random
- Sortie : Dernier mot est plus important
36. Classif, Pos, Entités nommées ..
- On gère le contexte : Catégorie d'un mot dépend de son contexte
- Taille raisonnable
- Taille variable
- Position variable des mots
Limites : 
Du mal a retenir les infos sur le contexte sur des distances si longues
43. LSTM
Idée : ajouter une nouvelle entrée/sortie à chaque unité, 
dont le but sera de se « souvenir » des choses importantes
- Trois entrées : mot, entrée mémoire, entrée Classif
- Deux sorties : Sortie mémoire, Classif
48. Bi-LSTM
- Parcourir le texte dans les deux sens
- Une cellule profite du contexte à gauche et à droite
Pour chaque unité : Forward et backward


### LSTM Staquest
- Gradient descent in Vanilla RNNs can explode or vanish.
-  LSTM uses two paths for predecting what will hapens tommorow, or let's reference it by memories:
    - Long term memories
    - Short term memories
- In a simple way, LSTM networks have some internal contextual state cells that act as long-term or short-term memory cells.
The output of the LSTM network is modulated by the state of these cells. This is a very important property when we need the prediction of the neural network to depend on the historical context of inputs, rather than only on the very last input.
- LSTM is a recurrent neural network (RNN) architecture that REMEMBERS values over arbitrary intervals. LSTM is well-suited to classify, process and predict time series given time lags of unknown duration. Relative insensitivity to gap length gives an advantage to LSTM over alternative RNNs, hidden Markov models and other sequence learning methods.
- The structure of RNN is very similar to hidden Markov model. However, the main difference is with how parameters are calculated and constructed. One of the advantage with LSTM is insensitivity to gap length. RNN and HMM rely on the hidden state before emission / sequence. If we want to predict the sequence after 1,000 intervals instead of 10, the model forgot the starting point by then. LSTM REMEMBERS.
- How does it work
    - LSTM networks manage to keep contextual information of inputs by integrating a loop that allows information to flow from one step to the next. These loops make recurrent neural networks seem magical. But if we think about it for a second, as you are reading this post, you are understanding each word based on your understanding of the previous words. You don’t throw everything away and start thinking from scratch at each word. Similarly, LSTM predictions are always conditioned by the past experience of the network’s inputs.
    - On the other hand, the more time passes, the less likely it becomes that the next output depends on a very old input. This time dependency distance itself is as well a contextual information to be learned. LSTM networks manage this by learning when to remember and when to forget, through their forget gate weights. In a simple way, if the forget gate is just a multiplicative factor of 0.9, within 10 time steps this factor becomes: 0.9¹⁰=0.348 (or 65% of information forgotten), and within 30 steps -> 0.04 (96% forgotten).