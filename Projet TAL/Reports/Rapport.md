## Plan du rapport
1. Introduction
2. Analyse préliminaire de la base d'apprentissage
3. Pré-processing de la base d'apprentissage
4. Modèles de machine learning
5. Sélection de modèles
6. Schéma expérimental
7. Campagne d'experiences   
    1. Première campagne
    2. Deuxième campagne
8. Résultats

Ce projet s'inscrit dans le cadre du module de Recherche d'Information et Traitement Automatique du Language naturel RITAL. Nous devons entraîner un classifieur binaire sur deux bases d'apprentissage : 
- Base des discours des présidents français Chirac et Mitterand.
- Base des reviews de films.

## Introduction
Cette base est composée de 54413 expressions et phrases tirés de différents discours des anciens présidents français Chirac et Mitterand, chaque expression (document) est étiquetée par un label C ou M (pour Chirac ou Mitterand) et qui indique le président ayant prononcé cette expression.

Il est à noter que les classes ne sont pas équilibrées, on note une forte présence des expressions du président Chirac que celles du président Mitterand.

L'objectif de ce projet est d'apprendre un classifieur binaire qui permet de prédire pour une expression donnée (Nouvelle expression provenant de l'un des deux présidents), si elle a été prononcée par le président Chirac ou Mitterand. Le modèle proposé sera ensuite appliqué sur un nouveau test set auquel on n'a pas accès durant la phase d'apprentissage.

## Analyse préliminaire de la base d'apprentissage
Nous commençons notre étude par une analyse préliminaire de notre base d’apprentissage.

Nous remarquons tout d’abord que la base est fortement déséquilibrée, puisque 49890 des phrases proviennent du président Chirac, contre 7523 phrases pour Mitterrand (soit 86.9% données étiquetées ‘C’ contre 13.1% de données étiquetées ‘M’). Nous craignons qu’un tel déséquilibre pousse notre 
classifieur à ne prédire que la classe majoritaire (‘C’) ; il faudra donc soit modifier un peu notre base pour  la  balancer  un  peu  mieux,  soit  modifier  notre  modèle  afin  qu’il  puisse  s’adapter  à  ce 
déséquilibre. 
 
Un  wordcloud  sur  les  phrases  propres  à  Chirac  et  à  Mitterrand  ne  nous  permettent  pas  de  différencier à vue d’œil les discours des deux présidents, même en supprimant les stopwords. Voici  les wordclouds pour Chirac à gauche, pour Mitterrand à droite :

**Here add wordclouds pic**

Nous  nous  proposons  donc  de  refaire  un  wordcloud  en  prenant  cette  fois-ci  en  compte  les  odds 
ratios afin de déterminer les mots les plus discriminants pour chaque classe.

**Here add odds ratio pic**

Nous  nous  apercevons  ainsi  par  exemple  que  Chirac  mentionne  beaucoup les mots ‘France’, ‘économie’, ‘social’, ‘engagement’, ‘développement’ et leurs dérivées tandis que Mitterrand parle plus de ‘guerre’, ‘Europe’, ‘industrialisation’, etc... 

Il est aussi intéressant de noter que Chirac, contrairement à Mitterrand, utilise beaucoup des mots incitant  la  confiance  et  cohésion.  En  particulier  parmi  les  mots  les  plus discriminants  pour  lui,  on 
retrouve ‘solidarité’, ‘respect’, ‘valeur’, ‘coeur’, ‘amitié’, ‘ensemble’ tandis que cette tendance n’est pas du tout présente chez Mitterrand.


## Pré-processing de la base d'apprentissage
Cette phase est primordiale afin de réduire la dimensionnalité de notre base lorsqu’elle sera mise sous forme liste de vecteurs sur le vocabulaire.
Les pré-traitements que nous considérons sont notamment : 
- La mise en minuscule 
- La normalisation afin de supprimer les accents et les caractères non normalisés 
- La suppression des chiffres et de la ponctuation 
- Le stemming 
- L’élimination des stopwords 

Ces  pré-traitements  s’effectuent  par  paramétrage  (True  ou  False)  d’une  fonction  dédiée  au  processing  du  corpus.  Il  s’agira  pour  nous  de  retrouver  par  GridSearch  la/les  combinaisons 
optimisant la métrique choisie sur la base d’apprentissage en validation croisée. Sachant que  le 
modèle de machine learning choisi, le paramètre de régularisation et le type de représentation des 
phrases seront aussi des paramètres à optimiser, nous devons déjà faire une pré-sélection sur ces 
pré-traitement  afin  de  réduire  le  nombre  de  combinaisons  à  tester.  Nous  choisissons  de  fixer  la 
suppression des chiffres et de la ponctuation à True : les discours ont été prononcés mais dans la 
base, les phrases ont été écrites de manière neutre (la ponctuation n’apporte donc pas d’information 
supplémentaire). 


## Modèles de machine learning
Nous choisissons de tester trois modèles de machine  learning : 
un SVM  linéaire (LinearSVC), un modèle Naive Bayes (MultinomialNB) et une régression logistique (LogisticRegression). En particulier,  sklearn  propose  également  un  paramètre  de  régularisation  C  pour  les  modèles  de 
régression linéaire et SVM que nous nous attacherons à optimiser.

## Selection de modèles
Par  défaut,  les  scores  calculés  pour  un  modèle  de  sklearn  sont  des  taux  de  bonne  classification 
(accuracy). Cette métrique n’est cependant pas adaptée à notre cas, puisqu’un classifieur  ne prédisant que la classe majoritaire (qui représente ici plus de 80% de nos données) aura une accuracy supérieure à 0.8... Nous choisissons donc d’optimiser le score F1 sur la classe minoritaire (Mitterrand). 

## Schéma expérimental
1. Première campagne d'expériences:
Nous effectuons en premier lieu grid search sur les pré-traitement ainsi que sur les modèle de machine learning décrits plus hauts. Le type de représentation des phrases (vectorisation par comptage ou par tf-idf) ainsi que les types de n-grams sont également des hyperparamètres à prendre en compte.

2. Deuxième campagne d'experiences:
Comme nous l’avons précisé plus haut, les données sont fortement déséquilibrées et la majorité d’entre  elles  appartient  à  la  classe  Chirac.  Nous  risquons  ainsi  d’apprendre  un  classifieur  ne 
prédisant presque que la classe majoritaire :
    - Sur-échantillonner la classe minoritaire, sous-échantilloner la classe minoritaire, ou faire un mélange des deux. Nos expériences n’ont pas été très concluantes, les résultats  obtenus sont moins bons que sur la base d’origine.
    - Changer la fonction de coût pour pénaliser plus fortement les mauvaises prédictions sur la classe  minoritaire.  Le  module  sklearn  propose pour  cela  pour  chacun  de  ses  modèle  un paramètre class_weight nous permettant d’attribuer un ratio de pénalité sur les différentes classes.

2. Troisième campagne d'experiences:
Utiliser les meilleurs modèles appris précédemment avec une technique d'apriori.
En effet, nous avons observé précédemment que les phrases des présidents étaient rangées par blocs dans 
la  base  d’apprentissage.  Nous  faisons  l’hypothèse  que  les  données  de  test  suivent  la  même 
tendance et nous nous proposons de procéder à un lissage des résultats afin d’obtenir également 
des blocs sur nos prédictions. C’est un choix justifiable puisque nos classifieurs auront tendance à 
moins bien prédire les phrases de la classe minoritaire, chaque prédiction -1 peut alors se révéler 
importante si elle n’est pas isolée. Nous procédons à trois lissages :
- Premier lissage:chaque  prédiction  devient  le  signe  de  la  somme  pondérée  de  son 
voisinage, en accordant un poids plus important aux voisins valant -1. Le nombre de voisins 
pris en compte est un hyperparamètre que nous fixons à 12 après optimisation par tests.
- Deuxième lissage: cette  fois-ci  nous  nous  intéressons  aux  labels  isolés  (ie  entourés  par 
deux labels de l’autre classe), ou aux séquences de labels différentes (-1,1,-1,1,-1). Dans le 
premier cas nous fixons la classe du label isolé aux labels qui l’entourent, dans le deuxième 
cas nous prenons le label majoritaire dans le voisinage en conflit 
- Troisième lissage: Nous remarquons un effet de bord sur les labels prédits en 
apprentissage :  il  arrive  que  les  séquences  de  -1  soient  plus  courtes  que  prévues  car  les 
labels  en  bordure  de  séquence,  bien  qu’originellement  prédites  comme  -1, ont été 
moyennées à 1. Nous proposons donc un dernier lissage qui consiste à regarder les voisins 
directs en début et en fin de séquences de -1, si leurs prédictions étaient originellement à -1 
(avant lissage). Si oui on met à -1 les suites de 1 originellement à -1 dans le voisinage direct 
des bords de séquences de -1.


## Campagnes d'expériences
1. Première campagne d'expériences:
Nous relevons d’abord les paramètres permettant d’obtenir les meilleures performances. Il est à noter que nous ne prenons pas directement la combinaison de paramètres donnant le meilleur score, mais étudions les 20 meilleures combinaisons pour convenir d’une  agrégation de ces paramètres qui nous parait optimale. Ainsi par exemple, les meilleures  combinaisons  ont  leurs  paramètres  stemming  à  True  et  no_stopwords  à False,  mais  un  grand  nombre  de  combinaisons  produisant  des  scores  similaires  (de  l’ordre  de  0.56-0.57)  ont  leurs paramètres stemming à False et no_stopwords à True. Nous choisissons de prendre ces derniers afin de réduire la dimensionnalité de nos matrices (suppression de stopwords).
Ainsi, nous décidons des paramètres suivants : 
- Pré-traitements : pas de mise en minuscule, suppression des chiffres et de la ponctuation, pas de normalisation, pas de stemming, suppression des stopwords.
- Représentation vectorielle : comptage, avec un n-gram range à (1,2) – prise en compte des unigrammes et bigrammes.
- Modèle : régression logistique avec C entre 10 et 100.  

2. Deuxième campagne d'experiences:
Comme nous l’avons précisé plus haut, les données sont fortement déséquilibrées et la majorité 
d’entre  elles  appartient  à  la  classe  Chirac.  Nous  risquons  ainsi  d’apprendre  un  classifieur  ne 
prédisant presque que la classe majoritaire.

Nous lançons une deuxième campagne d’expériences afin de déterminer les poids idéaux, en fixant 
certains  des  paramètres  déjà  optimisés  (les  pré-traitements).  Nous  laissons  la  représentation 
vectorielle  et  le  modèle  comme  hypermaramètres,  et  nous  trouvons  que  mettre  la  classe  -1 
(Mitterrand)  à  ~0.6  et  la  classe  1  à  ~0.4  nous  donne  les  meilleurs  résultats.  Cette  fois-ci  la 
représentation vectorielle à préférer est le tf-idf, toujours avec un modèle de régression logistique et 
un C plus faible. Nous le fixons à 20.