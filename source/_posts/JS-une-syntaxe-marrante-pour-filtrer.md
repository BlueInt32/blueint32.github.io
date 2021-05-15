---
title: "JS : une syntaxe marrante pour filtrer"
date: 2021-05-15 17:01:58
tags: JS
---

AJA une syntaxe intéressante pour filtrer facilement un tableau en fonction de la "truthiness" de ses valeurs.

Imaginons le tableau suivant :

```js
const myArray = ["mon", NaN, "beau", null, "tableau", ""];
```

Pour filtrer ce tableau, j'aurais utilisé le code suivant :

```js
const cleanArray = myArray.filter((i) => i);
```

Car [filter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) évalue la truthiness de la lambda pour chaque élément, cela fonctionne.

Or [sur stackoverflow](https://stackoverflow.com/a/19903063/1199535), j'ai lu la réponse suivante :

```js
const cleanArray = myArray.filter(Boolean);
```

Comment ça ? 🤔 J'ai tout d'abord cru qu'il s'agissait d'une syntaxe bizarre que je ne connaissais pas.

Mais en fait, en JS, `Boolean` n'est pas seulement un type, c'est également un constructeur, ou encore une fonction. Et il se trouve que l'on peut construire un booléen en lui fournissant n'importe quoi en entrée, ce qui donnera à ce booléen la truthiness de la valeur fournie.

![](example-1.png)

En somme, les trois syntaxes suivantes sont équivalentes :

```js
const cleanArray1 = myArray.filter((i) => i);
const cleanArray2 = myArray.filter((i) => Boolean(i));
const cleanArray3 = myArray.filter(Boolean);
```

Après... Entre nous, à part pour briller en société, la syntaxe ne me parait pas beaucoup plus lisible, mais c'était marrant de découvrir cette manière de procéder.
