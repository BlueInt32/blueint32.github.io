---
title: SQL - Window functions et aggregation functions
date: 2021-07-26 12:11:20
tags: sql
---

AJA que je n'avais rien écouté pendant le cours sur les bases de données 🤨 : je n'avais jamais entendu parler des "window functions", qui sont un peu les cousines des aggregation functions. EN SQL il est courant d'utiliser des aggregation functions par exemple pour compter les lignes d'une table avec la méthode `COUNT()`.

Les window function permettent de faire à peu près la même chose, sauf qu'elles agissent a posteriori et peuvent permettre d'éviter de tomber dans l'utilisation de requêtes imbriquées qui sont plutôt moins performantes.

Dans ce billet, pour introduire la notion de window function, je vais présenter un problème que j'ai rencontré dans un projet perso. Je vais tout d'abord décrire le jeu de données utilisé, mon approche initiale et l'approche [qui m'a été soufflée par un chic type sur stackoverflow](https://stackoverflow.com/questions/68525988/how-to-avoid-using-a-subquery-for-exclusion-in-a-many-to-many-sql-model). Malgré toute la justesse de sa réponse, elle était quand même plutôt expédiée (note : insérer ici un commentaire sur la course à la réputation dans le site stackoverflow) ; cela m'a amené à mener ma petit enquête et ce billet est né.

## Le contexte
Notre blog de vidéos rigolotes nécéssite trois tables SQL afin de représenter le fait que les `posts` créés sont associés à des `tags`. 

![](diagram.png)

Par exemple, pour créer un billet de blog "Chicken flying over a cucumber", je vais créer une entrée dans la table `posts`, trois entrées dans la table `tags` ("chicken", "funny", "cucumber") et trois entrées dans une table d'association `poststags` connectant le tout. 



 ℹ️ Une table d'association est ici nécéssaire pour représenter une relation de type "many-to-many" entre `posts` et `tags` car un post peut avoir plusieurs tags et un tag peut décorer plusieurs posts.


Pendant plusieurs mois, je remplis mon blog de supers vidéos, tout va bien même si mes choix éditoriaux ne m'amènent que peu de visites mais ce n'est pas le sujet de cet article.

Plus tard dans le développement de mon blog, je voudrais faire des statistiques, par exemple "Quel est le nombre d'articles de blog ne comportant aucun chat et n'étant pas drôle non plus ? ", autrement dit, il s'agit de compter le nombre de `posts` n'ayant aucune association avec les `tags` "cat" et "funny".

**Mon objectif est donc de pouvoir compter le nombre de posts ne contenant pas certains tags.**

## Les données
**Posts**

| Id | Title | Content |
| - | - | - |
| 1 | Chicken flying over a cucumber | ... |
| 2 | Cat eating a cucumber | ... |
| 3 | Cucumber wants to take revenge | ... |
| 4 | Snail sad because no cucumber left | ... |


**Tags**

| Id | Label |
| - | - |
| 1 | cat |
| 2 | funny |
| 3 | snail |
| 4 | cucumber |
| 5 | chicken |


**PostsTags**

| Id | PostId | TagId |
| - | - | - |
| 1 | 1	| 2 |
| 2 | 1	| 4 |
| 3 | 1	| 5 |
| 4 | 2	| 1 |
| 5 | 2	| 2 |
| 6 | 2	| 4 |
| 7 | 3	| 1 |
| 8 | 3	| 4 |
| 9 | 4	| 3 |

On voit par exemple que "Cat eating a cucumber" est une vidéo avec les tags "cat", "cucumber" et "funny" (oui, c'est rigolo).

### Résolution du problème avec l'approche "SQL newbie" (requête imbriquée)

Je rappelle mon problème : je cherche à compter le nombre de posts n'ayant ni le tag "cat" ni le tag "funny".

La première considération, c'est que l'ensemble de l'information nécéssaire pour répondre à cette question est contenue dans la table PostsTags. Je n'ai pas besoin de considérer les autres tables pour répondre.

Première étape, isoler les posts contenant soit un tag "cat" soit un tag "funny" (il s'agit bien d'un OR ici, car je veux à terme la négation de ce jeu de données, qui sera un NAND entre tous les tags à exclure). Pour cela on utilise l'opérateur `in`.

Voici la requête : 

```SQL
select postid 
from poststags 
where TagId in (1, 2)
group by postid
```

Ce qui donne : 

| Id |
| - |
| 1 |
| 2 |
| 3 |

En effet, tous les posts parlent de chat ou sont drôles, sauf le post 4.

Ensuite, c'est tout simple en utilisant `not in` (c'est à dire une requête imbriquée), je n'ai plus qu'à compter l'ensemble des posts ne se trouvant pas dans ce premier résultat : 


```SQL
select count(p.Id)
from posts p
where p.Id not in (
	select postid 
	from poststags 
	where TagId in (1, 2)
	group by postid)
```

Ça fonctionne, mais quand la requête imbriquée a beaucoup de résultats, la performance peut en prendre un coup.

### Résolution du problème avec l'approche "SQL professional" ("aggregation function" et "window function")

De la même manière, je vais procéder par étapes simples. 

1. Tout d'abord, on va chercher à obtenir pour chaque association dans `posttags` si le tag est "cat" ou "funny". Pour cela on va encore utiliser l'opérateur `in`, mais cette fois-ci dans la clause select. L'idée est de préparer le terrain pour faire une somme des booléens obtenus.

```SQL
select postId, TagId in (1, 2) as tagIsCatOrFunny
from poststags
```
| PostId | tagIsCatOrFunny | 
| -	| - |
| 1	| 1 |
| 1	| 0 |
| 1	| 0 |
| 2	| 1 |
| 2	| 1 |
| 2	| 0 |
| 3	| 1 |
| 3	| 0 |
| 4	| 0 |

2. On voit que chaque résultat précédent représente une association post-tag et qu'il va falloir grouper par postid pour aggréger intelligemment la valeur `tagIsCatOrFunny` : on va utiliser `group by` et la fonction d'aggrégation `SUM` pour obtenir pour chaque postid la somme du nombre de fois que le tag "chat" ou "funny" est représenté pour un post. Il est à noter ici que `SUM` pourrait être utilisé sans le `group by` mais cela nous retournerait une ligne unique avec le nombre total d'occurence d'associations dont le tag est "cat" ou "funny", ce qui ne nous intéresse pas.

```SQL
select postid, SUM(tagid IN (1, 2)) as occurencesOfCatOrFunny
from poststags
group by postid
```

| PostId | tagIsCatOrFunny | 
| -	| - |
| 1	| 1 |
| 2	| 2 |
| 3	| 1 |
| 4	| 0 |

On constate bien que par exemple, le post 2 "Cat eating a cucumber" est bien à la fois "funny" et contenant un chat.

3. On sait qu'à terme, on veut le nombre de fois que cette table contient la valeur 0 pour `tagIsCatOrFunny`, ajoutons donc simplement `= 0` à notre colonne de somme : 

```SQL
select postid, SUM(tagid IN (1, 2)) = 0 as hasNoOccurencesOfCatOrFunny
from poststags
group by postid
```

| PostId | tagIsCatOrFunny | 
| -	| - |
| 1	| 0 |
| 2	| 0 |
| 3	| 0 |
| 4	| 1 |

4. Il ne "reste plus qu'à" faire la somme de l'ensemble des valeurs de cette colonne pour obtenir le nombre final. Ça se complique un peu car il n'est pas permis d'aggréger directement le résultat d'une aggrégation en SQL (c'est pour cela qu'on voit très souvent des fonctions imbriquées pour traiter ce genre de situation). Or les window functions peuvent venir à la rescousse, car elles sont calculées de façon indépendante et après la ou les fonctions d'aggrégation. Le marqueur d'une window function est l'utilisation du mot clef `OVER` : 

```SQL
select postId, SUM(SUM(tagid IN (1, 2)) = 0) OVER()
from poststags
group by postid
```
Sans rentrer trop dans les détails (voir les liens en fin d'article pour plus d'info), il faut savoir que les window functions sont assez proches des fonctions d'aggrégation, mais une grande différence est qu'elles maintiennent les lignes sur lesquelles elles agissent (les fonctions d'aggrégation vont au contraire combiner les lignes pour chaque groupe). Par ailleurs, la définition de la "window frame" dans le `OVER()` détermine sur quel set de lignes le fonction s'applique, en l'occurence toutes les lignes ici, car la clause `OVER()` est vide.

On obtient le résultat suivant :

| PostId | targetValue |
| -	| - |
| 1	| 1 |
| 2	| 1 |
| 3	| 1 |
| 4	| 1 |

Pour chaque ligne de la table intermédiaire précédente, on a calculé "la somme des sommes". Et comme la window function maintient les lignes de la table sur laquelle elle est appliquée, on obtient le résultat autant de fois qu'il y a de posts. 

5. Sachant que chaque ligne aura la même valeur de somme, on peut simplement ajouter `distinct` pour récupérer une ligne unique (on en profite pour retirer le postId qui n'aura plus de sens) : 

```SQL
select distinct SUM(SUM(tagid IN (1, 2)) = 0)  OVER() as finalResult
from poststags
group by postid
```

Et voilà, on obtient notre résultat sans utiliser de requête imbriquée !


| targetValue | 
| - |
| 1 |

Il ne me reste plus qu'à mesurer à quel point cette technique est plus performante que l'utilisation de requête imbriquées mais cet article est déjà long !
 
---

[Voici une page](https://learnsql.com/blog/window-functions-vs-aggregate-functions/) expliquant très clairement la différence entre les aggregation functions et les window functions 

Enfin, [une vidéo de chat mangeant un concombre](https://www.youtube.com/watch?v=QnnqEnbYBDk) 

<style>
table {
	max-width: 40%;
}
</style>