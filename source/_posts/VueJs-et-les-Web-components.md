---
title: Les web components et les frameworks JS
date: 2021-05-08 07:29:50
tags:
  - vuejs
  - html
  - web components
---

AJA la distinction entre [un composant VueJs](https://vuejs.org/v2/guide/components.html) et un [web component](https://developer.mozilla.org/en-US/docs/Web/Web_Components).

Quand on démarre un projet en VueJs (ou React ou Angular), les composants que l'on crée sont écrits dans la syntaxe propre à ce framework. C'est efficace si l'on travaille toujours dans ce framework, on peut par exemple réutiliser [notre module VueSwitch](https://vuejsexamples.com/tag/switch/) entre plusieurs projets VueJs. Mais ces composants ne sont pas directement réutilisables dans d'autres projets n'utilisant pas VueJs. C'est un problème que les web components natifs peuvent résoudre.

En effet, les web components constituent une technologie native des navigateurs web et ne dépendent pas d'un framework javascript particulier pour fonctionner. Ils peuvent encapsuler leur code HTML, JS, CSS propres, en isolation (c'est à dire qu'il n'y aura par exemple pas de conflit entre le CSS défini dans ce web component et le CSS de la page qui l'héberge).

Il n'est pas forcément très pratique de définir un web component à partir de rien : toute la logique doit être développée sur place et cela peut s'avérer fastidieux en mode "Vanilla JS", a fortiori sans l'assistance d'un framework tel que VueJs embarquant des utilitaires pour la réactivité et le concept de cycle de vie, par exemple.

Enfin, le CLI de VueJs embarque [la possibilité d'extraire un web component à partir d'un composant VueJs](https://cli.vuejs.org/guide/build-targets.html#web-component) mais ce composant aura toujours une dépendance sur le framework VueJs, ce qui perd beaucoup de son intérêt.

Chez Github, ils ont fait le choix de [développer directement en natif](https://github.blog/2021-05-04-how-we-use-web-components-at-github/), ce qui ne les enferme pas trop dans un paradigme de programmation, mais à quel prix ?
