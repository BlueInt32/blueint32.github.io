---
title: Injection du nom de la branche git active dans bash
date: 2021-05-05 19:31:54
tags:
  - git
  - bash
---

AJA que je pouvais économiser 4 secondes à chaque fois que je veux `push` une branche à distance !

En effet, dans le cadre profesionnel, je suis souvent amené à créer des noms de branches du genre :

```bash
$ git checkout -d JIRA-14523-nom-de-nouvelle-branche-tres-tres-long
```

Pour créer la branche, je n'ai pas le choix, je dois tout écrire au moins une fois.

Mais à la fin de la journée, je trouve pénible de devoir le retaper, typiquement lorsque je dois `push` ma branche pour faire la pull-request :

```bash
$ git push -u origin nom-de-nouvelle-branche-tres-tres-long
```

Dans git bash, le nom de la branche courante est affiché dans le prompt donc je peux toujours la copier/coller, mais je préfère garder les mains sur le clavier quand j'utilise le terminal.

Pour régler ce grave problème 😏, on peut créer un alias dans le fichier `.bashrc` du compte de la machine :

```bash
alias cb='git symbolic-ref HEAD --short'
```

Ce qui permet de récupérer le nom de la branche courante sans le retaper :

```shell
$ cb
JIRA-14523-nom-de-nouvelle-branche-tres-tres-long
```

En revanche, il n'est pas possible d'injecter tel quel cet alias dans une commande git car git penserait qu'il s'agit d'un nom de branche et va remonter une erreur. Pour palier à cela, il suffit d'entourer l'alias avec des \`backquotes\` :

```bash
$ git push -u origin `cb`
```

Et voilà, plus besoin de prendre la souris pour copier le nom de la branche !
