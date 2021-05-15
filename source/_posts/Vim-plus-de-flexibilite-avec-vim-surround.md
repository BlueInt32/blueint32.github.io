---
title: "Vim : plus de flexibilité avec vim-surround"
date: 2021-05-15 08:19:31
tags:
  - vim
  - vscode
---

AJA que le plugin Vim de VsCode proposait d'activer [vim-surround](https://github.com/VSCodeVim/Vim#vim-surround), qui est à l'origine un [plugin de Vim](https://github.com/tpope/vim-surround) particulièrement pratique. Il fait économiser beaucoup de manipulations.

Voici un exemple de texte :

```
Texte à manipuler avec vim.
```

Sans vim-surround, si on veut mettre les mot `manipuler avec vim` entre guillemets, en supposant que le curseur se trouve au début de la ligne, la manipulation est la suivante :

```
wwi"<esc>3ea"
```

Avec vim-surround, cela devient plus idiomatique :

```
wwys3w"
```

Le principe est de n'écrire qu'une fois le caractère englobant, et de dire à vim-surround comment atteindre la fin de l'expression à entourer avec un déplacement (ici `3w`)

```
ys3w"
```

pourrait être traduit en :

```
You Surround 3 Words with "
```

C'est déjà une belle économie. Mais je n'aime personnellement pas beaucoup compter les mots pour les mouvements car ce n'est pas très fiable dans du code source. J'utilise plutôt des sélections en mode visuel (potentiellement en utilisant la souris).

Heureusement, vim-surround permet d'être actionné à partir d'une sélection avec `S` (s majuscule) !

vim-surround fait selon moi vraiment la différence pour remplacer des caractères déjà existant.

Reprenons notre texte modifié :

```
Texte à "manipuler avec vim".
```

Si je veux remplacer les guillemets par des parenthèses, la manipulation sans vim-surround est encore une fois fastidieuse :

```
wwr(eeelr)
```

Avec vim-surround :

```
wwcs")
```

où `cs")` pourrait être traduit en

```
Change Surround " to )
```

On obtient alors directement

```
Texte à (manipuler avec vim).
```

Plutôt pratique !

Le petit bonus sympa, c'est qu'en utilisant les caractères ouvrant `(` ou `[` dans l'expression de remplacement, vim-surround va automatiquement insérer des espaces à l'intérieur. Ainsi la commande suivante :

```
wwcs"(
```

produira le texte suivant :

```
Texte à ( manipuler avec vim ).
```

Vim, c'est la vie.
