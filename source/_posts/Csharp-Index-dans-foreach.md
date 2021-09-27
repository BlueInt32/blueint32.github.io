---
title: 'C#: astuce avec foreach'
date: 2021-09-27 08:57:57
tags: 
- C#
---

AJA que l'on pouvait obtenir l'index dans une boucle foreach d'une façon assez pratique sans avoir à incrémenter un compteur à la main (ce qui est vulgaire 😏). La fonction Select de Linq possède en effet [un overload](https://docs.microsoft.com/en-us/dotnet/api/system.linq.enumerable.select?view=net-5.0#System_Linq_Enumerable_Select__2_System_Collections_Generic_IEnumerable___0__System_Func___0_System_Int32___1__) qui prend une lambda prenant en charge l'index de boucle.

Avant C#7:

```csharp
foreach(var item in inputList.Select((wheel, i) => new { wheel, i})) {
	Console.WriteLine($"Wheel n°{item.i + 1} : {item.wheel.friction}");
}
```

Mais ce n'est pas super car on se trimballe un `item` dont on se passerait bien.

Heureusement, depuis C#7 (avec [la déconstruction de Tuples](https://docs.microsoft.com/en-us/dotnet/csharp/fundamentals/functional/deconstruct)), on peut directement assigner les variables :

```csharp
foreach((Wheel wheel, int i) in inputList.Select((value, i) => (wheel, i))) {
	Console.WriteLine($"Numero {i + 1} : {wheel.friction}");
}
```