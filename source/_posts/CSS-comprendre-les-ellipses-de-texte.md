---
title: "CSS : comprendre les ellipses de texte"
date: 2021-05-15 09:04:22
tags:
  - html
  - css
---

AJA comment fonctionnaient vraiment les ellipses de texte en CSS. A priori, on pourrait croire qu'il suffirait d'une ligne de CSS pour les mettre en place. En réalité, c'est un tout petit peu plus compliqué que ça.

En effet, il s'agit de spécifier au navigateur la chose suivante : "si ce texte est trop long, il ne doit pas dépasser de son conteneur et afficher trois petits points juste avant de dépasser".

Il n'en est rien !

Tout d'abord, voyons comment le texte se comporte lorsqu'aucune règle particulière n'est appliquée :

![](example-1.png)

On constate que, par défaut, le moteur CSS applique un "wrapping", qui donne au texte la propriété d'aller automatiquement à la ligne lorsqu'il rencontre les bords du conteneur.

Or, notre objectif est que le texte n'aille pas à la ligne mais qu'il soit caché. Il faut donc tout d'abord désactiver ce "wrapping" :

```css
white-space: nowrap;
```

![](example-2.png)

Ensuite, il semble clair que tout ce qui dépasse doit être rabotté ! Cela n'est manifestement pas le cas par défaut, il y a donc une nouvelle règle CSS à ajouter à notre code :

```css
white-space: nowrap;
overflow: hidden;
```

![](example-3.png)

Apparement, le moteur CSS ne veut pas appliquer l'ellipse par défaut. En effet, son comportement par défaut est "clip", ce qui signifie que le texte sera coupé sans autre forme de procès. C'est peu élégant. Appliquons notre ellipse ici :

```css
white-space: nowrap;
overflow: hidden;
text-overflow: ellipsis;
```

![](example-4.png)

Et voilà ! 😅

Ce qu'il faut retenir de tout ça, c'est que cette dernière règle seule ne se "met en route" que dans le contexte où le texte va sortir de son conteneur (`white-space: nowrap;`) et où ce dépassement est caché (`overflow: hidden`).

Le code des exemples se trouve dans ce [codepen](https://codepen.io/BlueInt32/pen/GRWZVaR).

Sinon, vous trouverez plus de détail sur `text-overflow` dans l'excellente documentation [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/CSS/text-overflow).
