---
title: "C# : Syntaxe alternative pour les tests d'exception"
date: 2021-05-12 08:36:56
tags: C#
---

AJA qu'il existait une alternative pour tester les exceptions en C#. Depuis toujours j'utilisais la syntaxe suivante :

```csharp
[TestMethod]
[ExpectedException(typeof(DivideByZeroException))]
public void Division_ShouldThrowIfDividedByZero()
{
    var a = 1;
    var b = 0;
    var c = a / b;
}
```

Cette syntaxe est assez limitée car elle ne permet pas d'effectuer d'assertions sur l'exception levée pendant l'exécution du test.

Certes, on peut insérer un bloc try/catch et effectuer des assertions comme ceci:

```csharp
[TestMethod]
[ExpectedException(typeof(DivideByZeroException))]
public void Division_ShouldThrowIfDividedByZero()
{
    try
    {
        var a = 1;
        var b = 0;
        var c = a / b;
    }
    catch(Exception e)
    {
        Assert.AreEqual("Attempted to divide by zero.", e.Message);
        throw; // throw nécéssaire pour que le décorateur Expected soit honoré
    }
}
```

Mais ce n'est pas très élégant 😢.

Or il existe une autre syntaxe bien plus pratique :

```csharp
[TestMethod]
public void Division_ShouldThrowIfDividedByZero()
{
    var ex = Assert.ThrowsException<DivideByZeroException>(() =>
    {
        var a = 1;
        var b = 0;
        var c = a / b;
    });
    Assert.AreEqual("Attempted to divide by zero.", ex.Message);
}
```

La lisibilité des tests est améliorée, les code reviews sont plus sereines et le monde se porte mieux. 🌈
