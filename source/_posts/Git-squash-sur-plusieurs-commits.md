---
title: Git squash sur plusieurs commits avec cherry-pick
date: 2021-05-05 19:18:13
tags: git
---

AJA que l'on pouvait appliquer les changements d'une série de commits sans les enregistrer, pour faire un genre de squash à la volée.

Le scénario typique pour faire cette manip est le suivant. Vous avez une série de commits sur votre branche `main`, et vous voulez appliquer l'ensemble des patchs sur une branche de prod pour un hotfix avec un unique commit qui réunit tous les changements.

Sur la branche main :

```
commit 1 -> commit 2 -> commit 3
316e63c     ab0f801     3ae6aa6
```

Mais vous voulez obtenir sur la branche `HF` :

```
commits 1 + 2 + 3
xxxxxxxx
```

La commande git [cherry-pick](https://git-scm.com/docs/git-cherry-pick) permet d'appliquer les changements d'un set de commits sans les enregistrer, avec le flag `-n` :

```shell
$ git cherry-pick -n 316e63c ab0f801 3ae6aa6
```

Les changements sont dans le stage de git, il ne reste plus qu'à faire le commit avec un nouveau message probablement plus approprié pour une branche de hotfix.
