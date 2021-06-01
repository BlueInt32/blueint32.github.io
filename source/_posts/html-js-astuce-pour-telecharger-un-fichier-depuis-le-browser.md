---
title: "HTML/JS : astuce pour générer un fichier téléchargeable depuis le browser"
date: 2021-06-01 08:58:08
tags:
  - javascript
  - html
---

AJA que les hyperliens HTML pouvaient spécifier un attribut `download` permettant de forcer le téléchargement d'un fichier depuis un lien donné.

Certes, les navigateurs ont tendance à outrepasser ce comportement pour beaucoup de types de fichiers par défaut (ils vont par exemple afficher directement une image même si le lien a cet attribut).

Cependant, il est possible d'utiliser cette technique pour générer un téléchargement en créant un hyperlien à la volée ("sur la mouche", comme on dit dans le milieu).

```html
<html>
  <button onclick="clickHandler()">Clique moi</button>
  <script>
    function clickHandler() {
      const data =
        "More than cleverness, we need kindness and gentleness. Charlie Chaplin";
      const fileURL = window.URL.createObjectURL(new Blob([data]));

      const fileLink = document.createElement("a");

      fileLink.href = fileURL;
      fileLink.setAttribute("download", "charlie.txt");
      document.body.appendChild(fileLink);
      fileLink.click();
      document.body.removeChild(fileLink);
    }
  </script>
</html>
```
Si par exemple des données JSON sont retournées par une API, il est ainsi parfaitement possible de créer un fichier pour le rendre téléchargeable à partir de la chaîne de caractères.

Par ailleurs, on voit que la fonction utilise un [Blob](https://developer.mozilla.org/en-US/docs/Web/API/Blob) dont les mystères restent encore à découvrir 🙂.

En tout cas, c'est malin !
