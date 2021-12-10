---
title: 'CSS : margin, flex et sélecteur :only-child'
date: 2021-10-27 15:48:17
tags: css
---

AJA qu'il existait un lien et des règles spécifiques d'interaction entre `display: flex;` et `margin:auto;` en CSS - alors qu'a priori on pourrait penser que ça ne va pas du tout fonctionner ensemble. Et puis JA aussi qu'il existait un selecteur css `:only-child`, ce qui est pratique mais plus anecdotique.

Donnons-nous un exemple concret pour mettre tout cela en pratique.

Supposons que vous voulions créer un footer de page contenant des boutons "Previous" et "Next", par exemple pour une appli avec un wizard de création d'objet en plusieurs étapes.
Je veux afficher la bouton "Previous" complètement à gauche, et le bouton "Next" complètement à droite, comme ceci : 

![](default.png)

Pour cela, on utilise la structure HTML suivante (simplifiée).

```HTML
<div class="container">
  <div class="panel"></div>
  <div class="panel footer">
    <input type="button" value="Previous" />
    <input type="button" value="Next" />
  </div>
</div>
```

Et le css suivant pour le footer :

```CSS
.footer {
  display: flex;
  justify-content: space-between;
  
  border-bottom-right-radius: 10px;
  border-bottom-left-radius: 10px;
  border-top: 1px solid #255f7e;
}
```

Super ! 

Sauf qu'un problème se pose pour la première étape, dans laquelle il n'y a pas de bouton "Previous". 

```HTML
<div class="container">
  <div class="panel"></div>
  <div class="panel footer">
    <!--     <input type="button" value="Previous" /> -->
    <input type="button" value="Next" />
  </div>
</div>
```

Mon bouton "Next" se retrouve à gauche à cause du comportement par défaut de flex et du `justify-content: space-between;` :


![](issue.png)


Pour régler ce problème, on va utiliser la capacité de margin combinée avec flex. 

Il faut savoir que l'utilisation d'une règle `margin: auto;` sur un item se trouvant dans un context "flex" va étendre la marge de cet item pour occuper toute la place disponible dans le conteneur, dans la direction spécifiée. 
Cela veut dire que si on applique `margin-left: auto;` sur notre bouton "Next", une marge de la largeur restante du conteneur va être placée à sa gauche, revenant à le placer sur la droite : 

```CSS
input[type=button] {
  border: none;
  box-shadow: 3px 3px 0px #255f7e;
  border-radius: 3px;
  background: #00a8ff;
  color: white;
  
  margin-left: auto;
}
```

![](fix-1.png)

C'est bien pratique ! Cela permet aussi de centrer des éléments, car si on ne donne pas de direction, la marge va être partagée dans les directions opposables et centrer l'élément naturellement. [Plus d'infos ici.](https://css-tricks.com/the-peculiar-magic-of-flexbox-and-auto-margins/)

Mais si on remet notre bouton "Previous", on obtient un nouveau probleme - les deux boutons partagent l'espace horizontal disponible du conteneur pour leur marge de gauche : 

![](issue-2.png)

Il suffit donc d'appliquer le selecteur CSS `:only-child` et de n'appliquer la marge que là : 

```CSS
input[type=button]:only-child {
  margin-left: auto;
}
```

Et voilà !😃

![](fix-2.png)

Et ne me remerciez pas pour la beauté du design, c'est cadeau !

[Le code-pen pour faire joujou](https://codepen.io/BlueInt32/pen/ZEJKgXw)
