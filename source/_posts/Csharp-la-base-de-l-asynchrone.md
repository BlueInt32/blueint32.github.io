---
title: 'C# : la base de l''asynchrone'
date: 2021-06-14 07:01:14
tags: 
- C#
---

AJA un peu plus précisément comment fonctionnait le système de gestion des tâches asynchrones en C#. J'ai découvert [une page de documentation microsoft](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/async/) vraiment bien écrite pour découvrir (ou redécouvrir) le sujet en détail. 

L'article est fondé sur l'analogie de la préparation d'un petit déjeuner : certaines des tâches ne nécessitent pas la présence constante d'un cuistot, par exemple pendant que les tartines cuisent dans le grille-pain. On comprend intuitivement que personne de sain d'esprit ne resterait planté devant le grille-pain alors qu'il est possible de cuire les oeufs et de verser le jus d'orange en attendant. On parle de tâche asynchrone.

En .net, ce concept de tâche asynchrone est implémenté à l'aide de l'objet `Task`. Il représente non pas l'action de faire quelque chose (comme le ferait une fonction quelconque) mais plutôt le fait qu'une action est en cours, il s'agit donc d'une représentation de son status. Cela correspond pratiquement à la notion de promesse en JS.

Concrètement, il peut s'agir d'un appel à un service externe. Voici un exemple particulièrement utile : 

```csharp
	public static async Task<ImportantData> GetDataAsync(int taskNumber)
	{
		return await Task.Run(() =>
		{
			Console.WriteLine("\tLong task start " + taskNumber);
			Thread.Sleep(1000);
			Console.WriteLine("\tTick " + taskNumber);
			Thread.Sleep(1000);
			Console.WriteLine("\tTick " + taskNumber);
			Thread.Sleep(1000);
			Console.WriteLine("\tLong task finish " + taskNumber);
			return new ImportantData();
		});
	}
```

Un utilisateur de cette méthode asynchrone pourra adopter plusieurs stratégies, plus ou moins efficaces. 

Tout d'abord il est possible d'appeler cette méthode sans syntaxe particulière comme ceci : 
```csharp
	static async Task Main(string[] args)
	{
		Console.WriteLine("Program start");
		var gettingImportantData1 = SlowService.GetDataAsync(1);
		Console.WriteLine("Synchronous task");
		Console.WriteLine("Program end");
	}
```

Ici, l'éxecution ne va pas se bloquer pour attendre `GetDataAsync`. Cela veut dire que le programme va se terminer avant même que la tâche ait effectué son premier "tick" : 

```
Program start
        Long task start 1
Synchronous task
Program end
```

Autre possibilité : attendre.
```csharp
	static async Task Main(string[] args)
	{
		Console.WriteLine("Program start");
		var gettingImportantData1 = await SlowService.GetDataAsync(1);
		Console.WriteLine("Synchronous task");
		Console.WriteLine("Program end");
	}
```
Dans ce cas, le programme va suspendre son execution et attendre la fin de la tâche longue avant de reprendre : on note cependant que la tâche synchrône est toujours effectuée après la tâche longue.
```
Program start
        Long task start 1
        Tick 1
        Tick 1
        Long task finish 1
Synchronous task
Program end
```

⚠️ Ici, il est important de noter que pendant l'exécution de la tâche longue (avec `await`), la CLR relâche le thread. Dans un contexte web ou client WPF par exemple, cela signifie que d'autres actions utilisateurs pourront être prises en charge par ce thread en attendant. Ce n'est pas du tout négligeable pour la scalabilité.

Toujours est-il que notre programme en tant que tel n'est pas très optimisé : en effet, je préfèrerais largement gagner du temps sur l'éxecution de ma tâche synchrone et l'effectuer en parallèle de la tâche longue. Et qui sait, peut-être voudrais-je appeler deux fois ma tâche longue avec d'autres paramètres !

C'est très simple, il suffit d'ordonnancer les appels de façon intuitive par rapport aux tâches : 

```csharp
	static async Task Main(string[] args)
	{
		Console.WriteLine("Program start");
		var gettingImportantData1 = SlowService.GetDataAsync(1);
		Console.WriteLine("Synchronous task");
		await gettingImportantData1;
		Console.WriteLine("Program end");
	}
```

On voit ici qu'il y a une différence entre démarrer une tâche asynchrône, et forcer l'attente de la fin de son exécution. L'objet `Task` permet de garder la main sur cela et d'avoir enfin un programme performant :

```
Program start
        Long task start 1
Synchronous task
        Tick 1
        Tick 1
        Long task finish 1
Program end
```

Et comme je l'avais envisagé, je peux appeler ma tâche longue plusieurs fois en parallèle : 

```csharp
	static async Task Main(string[] args)
	{
		Console.WriteLine("Program start");
		var gettingImportantData1 = SlowService.GetDataAsync(1);
		var gettingImportantData2 = SlowService.GetDataAsync(2);
		Console.WriteLine("Synchronous task");
		await gettingImportantData1;
		await gettingImportantData2;
		Console.WriteLine("Program end");
	}
```

Et ainsi obtenir le parallelisme attendu :
```
Program start
Synchronous task
        Long task start 1
        Long task start 2
        Tick 1
        Tick 2
        Tick 1
        Tick 2
        Long task finish 1
        Long task finish 2
Program end
```