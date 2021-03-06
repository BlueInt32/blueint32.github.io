---
title: "git add -N : marquer des fichiers pour ajout"
date: 2021-05-12 08:56:51
tags: git
---

AJA que l'on pouvait spécifier à git qu'on _prévoyait d'ajouter_ des fichiers sans forcément les ajouter tout de suite à l'index !

Mais pourquoi voudrait-on faire ça ? Eh bien il peut être embêtant dans git de faire des différentiels lorsque certains fichiers viennent d'être rajoutés, car ceux-ci seront tout simplement ignorés par git tant qu'il n'aura pas été mis au courant que ces fichiers ont un rôle dans la base de code (pour autant que git sache, on pourrait très bien vouloir ajouter ces fichiers dans le `.gitignore`).

Jusqu'ici, je détournais ce comportement en ajoutant tous les fichiers avec

```bash
$ git add .
```

Ainsi, les nouveaux fichiers apparaissaient dans le diff-tool en utilisant le flag `--staged` :

```bash
$ git diff-tool --staged
```

Et je pouvais noter les fichiers que je voulais réellement inclure dans mon commit, avant de faire `git reset`... Bref, pas idéal. L'ennui avec cette approche, c'est que l'opération naturelle qui consiste à ajouter certains fichiers au fur à mesure de l'analyse du différentiel n'est plus possible car tous les fichiers sont déjà dans l'index...

Mais ça, c'était avant. Il est possible de spécifier à git que des fichiers vont être ajoutés, sans les ajouter concrètement tout de suite à l'index :

```bash
$ git add --intent-to-add .
 ou
$ git add -N .
```

Les nouveaux fichiers ont alors bien une entrée dans l'index, sans contenu effectif : le diff-tool les verra sans qu'ils soient encore concrètement dans l'index. C'est du temps de gagné ! ![](joy.png)

Pour plus d'info sur les options de `git add`dans la doc officielle [ici](https://git-scm.com/docs/git-add).
