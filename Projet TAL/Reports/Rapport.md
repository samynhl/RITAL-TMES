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
8. Difficultés rencontrées et idées d'ouverture.

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

## Campagnes d'expériences

## Difficultés rencontrées et idées d'ouverture