---
title: Sucre syntaxique des constructeurs Typescript
date: 2021-05-04 05:32:01
tags: typescript
---

AJA que l'on pouvait écrire des constructeurs typescript qui automatisaient la creation de variables locales, cela s'appelle "Parameter properties".

Plutôt que d'écrire :

```typescript
class Car {
  private brand: string;

  constructor(brand: string) {
    this.brand = brand;
  }
}
```

on peut écrire

```typescript
class Car {
  constructor(private brand: string) {}
}
```

Plus d'informations sur les parameter properties [ici](https://www.typescriptlang.org/docs/handbook/2/classes.html#parameter-properties).
