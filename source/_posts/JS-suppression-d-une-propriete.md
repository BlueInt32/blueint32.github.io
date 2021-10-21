---
title: 'JS: Syntaxe cryptique pour supprimer une propriété d''un objet'
date: 2021-10-21 17:11:06
tags: JS
---

AJA qu'il existait une manière assez intriguante de retirer une propriété d'un objet en JS : 

```JS
	let obj = { prop1: 'hey', prop2: 'ho' };
	// ...
	obj = (({ prop2, ...o }) => o)(obj);
	// équivaut à 
	delete obj.prop2;
```

Si on regarde bien ce "one-liner", on a une lambda qui pourrait être extraite comme ceci : 

```JS
	let obj = { prop1: 'hey', prop2: 'ho' };
	// ...
	obj = prop2Remover(obj);

	function prop2Remover({ prop2, ...o }) {
		return o;
	}

```

On voit une utilisation plutôt élégante et combinée de l'[object destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) et de la syntaxe [spread](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax), que l'on pourrait encore décortiquer davantage :

```JS
	let obj = { prop1: 'hey', prop2: 'ho' };
	// ...
	obj = prop2Remover(obj);

	function prop2Remover(obj) {
		// le pattern matching va chercher la propriété prop2 dans obj 
		// et le spread operator va assigner tout le reste de obj à o !
		const { prop2, ...o } = obj;
		return o;
	}

```

Cette syntaxe peut paraître un peu cuistre par rapport au `delete`, mais on peut quand même y trouver un intérêt. Par exemple si l'on veut assigner l'objet "nettoyé" en une seule instruction. De plus, l'opérateur spread effectue une nouvelle copie ce qui n'est pas à négliger dans certains contextes par exemple vuex ou redux.

```JS
let obj = { prop1: 'hey', prop2: 'ho' };
// une seule instruction pour 'clean up' l'objet obj et l'assigner dans otherObj
const otherObj = {
	otherProp1: 'lol',
	otherProp2: (({ prop2, ...o }) => o)(obj)
}

```

Mais bon, autrement, c'est vraiment pour frimer ! 😃
