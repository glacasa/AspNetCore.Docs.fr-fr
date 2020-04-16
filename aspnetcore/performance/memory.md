---
title: Gestion de la mémoire et modèles dans ASP.NET Core
author: rick-anderson
description: Découvrez comment la mémoire est gérée dans ASP.NET Core et comment fonctionne le collecteur d’ordures (GC).
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: performance/memory
ms.openlocfilehash: b2af9cb567cdb1d7b2d0942601fcc3ebd999a5d9
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440946"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a>Gestion de la mémoire et collecte des ordures (GC) dans ASP.NET Core

Par [Sébastien Ros](https://github.com/sebastienros) et Rick [Anderson](https://twitter.com/RickAndMSFT)

La gestion de la mémoire est complexe, même dans un cadre géré comme .NET. L’analyse et la compréhension des problèmes de mémoire peuvent être difficiles. Cet article :

* A été motivé par de nombreuses *fuites* de mémoire et *GC ne fonctionne pas* les problèmes. La plupart de ces problèmes ont été causés par le fait de ne pas comprendre comment fonctionne la consommation de mémoire dans .NET Core, ou de ne pas comprendre comment il est mesuré.
* Démontre l’utilisation problématique de la mémoire, et suggère des approches alternatives.

## <a name="how-garbage-collection-gc-works-in-net-core"></a>Fonctionnement de la collecte des ordures (GC) dans .NET Core

Le GC alloue des segments de tas où chaque segment est une gamme contigu de mémoire. Les objets placés dans le tas sont classés en l’une des 3 générations : 0, 1 ou 2. La génération détermine la fréquence à laquelle le GC tente de libérer de la mémoire sur des objets gérés qui ne sont plus référencés par l’application. Les générations numérotées inférieures sont GC’d plus fréquemment.

Les objets sont déplacés d’une génération à l’autre en fonction de leur vie. Comme les objets vivent plus longtemps, ils sont déplacés dans une génération supérieure. Comme mentionné précédemment, les générations supérieures sont GC’d moins souvent. Les objets de courte durée restent toujours dans la génération 0. Par exemple, les objets qui sont référencés pendant la durée d’une demande web sont de courte durée. Les [singletons de niveau d’application](xref:fundamentals/dependency-injection#service-lifetimes) migrent généralement vers la génération 2.

Lorsqu’une application ASP.NET Core démarre, le GC :

* Réserve un peu de mémoire pour les segments de tas initiaux.
* Engage une petite partie de mémoire lorsque le temps d’exécution est chargé.

Les allocations de mémoire précédentes sont effectuées pour des raisons de performance. L’avantage de performance vient des segments de tas dans la mémoire contigu.

### <a name="call-gccollect"></a>Appelez GC. Recueillir

Appel [GC. Recueillir](xref:System.GC.Collect*) explicitement:

* Ne doit **pas** être fait par la production ASP.NET applications Core.
* Est utile lors de l’étude des fuites de mémoire.
* Lors de l’enquête, vérifie que le GC a supprimé tous les objets suspendus de la mémoire afin que la mémoire puisse être mesurée.

## <a name="analyzing-the-memory-usage-of-an-app"></a>Analyse de l’utilisation de la mémoire d’une application

Des outils dédiés peuvent aider à analyser l’utilisation de la mémoire :

- Compter les références d’objets
- Mesurer l’impact de la CCG sur l’utilisation du processeur
- Mesurer l’espace mémoire utilisé pour chaque génération

Utilisez les outils suivants pour analyser l’utilisation de la mémoire :

* [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Peut être utilisé sur les machines de production.
* [Analyser l’utilisation de la mémoire sans le débbugger Visual Studio](/visualstudio/profiling/memory-usage-without-debugging2)
* [Profiler l’utilisation de la mémoire dans Visual Studio](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a>Détection des problèmes de mémoire

Task Manager peut être utilisé pour avoir une idée de la quantité de mémoire qu’une application ASP.NET utilise. La valeur de mémoire de Task Manager :

* Représente la quantité de mémoire utilisée par le processus ASP.NET.
* Inclut les objets vivants de l’application et d’autres consommateurs de mémoire tels que l’utilisation de la mémoire native.

Si la valeur de mémoire task Manager augmente indéfiniment et ne s’aplatit jamais, l’application a une fuite de mémoire. Les sections suivantes démontrent et expliquent plusieurs modèles d’utilisation de la mémoire.

## <a name="sample-display-memory-usage-app"></a>Exemple d’application d’utilisation de la mémoire d’affichage

[L’application d’échantillon MemoryLeak](https://github.com/sebastienros/memoryleak) est disponible sur GitHub. L’application MemoryLeak :

* Inclut un contrôleur diagnostique qui recueille la mémoire en temps réel et les données GC pour l’application.
* A une page Index qui affiche la mémoire et les données GC. La page Index est actualisée chaque seconde.
* Contient un contrôleur API qui fournit divers modèles de charge de mémoire.
* N’est pas un outil pris en charge, cependant, il peut être utilisé pour afficher des modèles d’utilisation de la mémoire des applications ASP.NET Core.

Exécutez MemoryLeak. La mémoire allouée augmente lentement jusqu’à ce qu’un GC se produise. La mémoire augmente parce que l’outil alloue un objet personnalisé pour capturer les données. L’image suivante montre la page MemoryLeak Index lorsqu’un GC Gen 0 se produit. Le graphique affiche 0 RPS (demandes par seconde) parce qu’aucun critère d’évaluation de l’API du contrôleur aPI n’a été appelé.

![graphique précédent](memory/_static/0RPS.png)

Le graphique affiche deux valeurs pour l’utilisation de la mémoire :

- Alloué : la quantité de mémoire occupée par des objets gérés
- [Ensemble](/windows/win32/memory/working-set)de travail : L’ensemble de pages dans l’espace d’adresse virtuel du processus qui résident actuellement dans la mémoire physique. L’ensemble de travail affiché est la même valeur Task Manager affiche.

### <a name="transient-objects"></a>Objets transitoires

L’API suivante crée une instance de 10 KB String et la renvoie au client. À chaque demande, un nouvel objet est attribué en mémoire et écrit à la réponse. Les cordes sont stockées sous forme de caractères UTF-16 en .NET de sorte que chaque personnage prend 2 octets dans la mémoire.

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

Le graphique suivant est généré avec une charge relativement faible pour montrer comment les allocations de mémoire sont affectées par le GC.

![graphique précédent](memory/_static/bigstring.png)

Le graphique précédent montre :

* 4K RPS (Demandes par seconde).
* Les collections de génération 0 GC ont lieu environ toutes les deux secondes.
* L’ensemble de travail est constant à environ 500 Mo.
* Le processeur est de 12%.
* La consommation et la libération de mémoire (par GC) sont stables.

Le graphique suivant est pris au débit maximum qui peut être manipulé par la machine.

![graphique précédent](memory/_static/bigstring2.png)

Le graphique précédent montre :

* 22K RPS
* Les collections de génération 0 GC ont lieu plusieurs fois par seconde.
* Les collections de génération 1 sont déclenchées parce que l’application a alloué beaucoup plus de mémoire par seconde.
* L’ensemble de travail est constant à environ 500 Mo.
* Le processeur est de 33%.
* La consommation et la libération de mémoire (par GC) sont stables.
* Le CPU (33%) n’est pas surutilisé, c’est pourquoi la collecte des ordures peut suivre un nombre élevé d’allocations.

### <a name="workstation-gc-vs-server-gc"></a>Workstation GC vs Server GC

Le collecteur d’ordures .NET a deux modes différents:

* **Workstation GC**: Optimisé pour le bureau.
* **Serveur GC**. Le GC par défaut pour ASP.NET applications Core. Optimisé pour le serveur.

Le mode GC peut être défini explicitement dans le fichier du projet ou dans le fichier *runtimeconfig.json* de l’application publiée. La balisage `ServerGarbageCollection` suivante montre l’établissement dans le dossier du projet :

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

Pour `ServerGarbageCollection` modifier le fichier du projet, l’application doit être reconstruite.

**Note:** La collecte des ordures du serveur **n’est pas** disponible sur les machines d’un seul cœur. Pour plus d’informations, consultez <xref:System.Runtime.GCSettings.IsServerGC>.

L’image suivante montre le profil de mémoire sous un RPS 5K à l’aide du Workstation GC.

![graphique précédent](memory/_static/workstation.png)

Les différences entre ce graphique et la version serveur sont significatives :

- L’ensemble de travail passe de 500 Mo à 70 Mo.
- Le GC fait des collections de génération 0 plusieurs fois par seconde au lieu de toutes les deux secondes.
- GC passe de 300 Mo à 10 Mo.

Sur un environnement de serveur web typique, l’utilisation du processeur est plus importante que la mémoire, donc le Server GC est mieux. Si l’utilisation de la mémoire est élevée et que l’utilisation du processeur est relativement faible, le Workstation GC pourrait être plus performant. Par exemple, la haute densité hébergeant plusieurs applications Web où la mémoire est rare.

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a>GC à l’aide de Docker et de petits conteneurs

Lorsque plusieurs applications conteneurisées sont en cours d’exécution sur une machine, Workstation GC peut être plus préformant que Server GC. Pour plus d’informations, voir [Running with Server GC in a Small Container](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) and Running with Server [GC in a Small Container Scenario Part 1 - Hard Limit for the GC Heap](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).

### <a name="persistent-object-references"></a>Références d’objets persistants

Le GC ne peut pas libérer les objets qui sont référencés. Les objets référencés mais qui n’ont plus besoin entraînent une fuite de mémoire. Si l’application alloue fréquemment des objets et ne les libère pas après qu’ils ne soient plus nécessaires, l’utilisation de la mémoire augmentera avec le temps.

L’API suivante crée une instance de 10 KB String et la renvoie au client. La différence avec l’exemple précédent est que cette instance est référencée par un membre statique, ce qui signifie qu’il n’est jamais disponible pour la collecte.

```csharp
private static ConcurrentBag<string> _staticStrings = new ConcurrentBag<string>();

[HttpGet("staticstring")]
public ActionResult<string> GetStaticString()
{
    var bigString = new String('x', 10 * 1024);
    _staticStrings.Add(bigString);
    return bigString;
}
```

Le code précédent :

* Est un exemple d’une fuite de mémoire typique.
* Avec les appels fréquents, provoque des augmentations de la mémoire d’application jusqu’à ce que le processus se bloque à une `OutOfMemory` exception près.

![graphique précédent](memory/_static/eternal.png)

Dans l’image précédente:

* L’essai `/api/staticstring` de charge le point de terminaison provoque une augmentation linéaire de la mémoire.
* Le GC tente de libérer la mémoire à mesure que la pression de la mémoire augmente, en appelant une collection de génération 2.
* Le GC ne peut pas libérer la mémoire fuite. Les ensembles alloués et de travail augmentent avec le temps.

Certains scénarios, tels que la mise en cache, nécessitent des références d’objets à tenir jusqu’à ce que la pression de mémoire les oblige à être libérés. La <xref:System.WeakReference> classe peut être utilisée pour ce type de code de mise en cache. Un `WeakReference` objet est recueilli sous la pression de la mémoire. La mise <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> en `WeakReference`œuvre par défaut des utilisations .

### <a name="native-memory"></a>Mémoire autochtone

Certains objets .NET Core s’appuient sur la mémoire indigène. La mémoire autochtone ne peut **pas** être recueillie par le GC. L’objet .NET utilisant la mémoire indigène doit le libérer à l’aide de code natif.

.NET fournit <xref:System.IDisposable> l’interface pour permettre aux développeurs de libérer la mémoire native. Même <xref:System.IDisposable.Dispose*> s’il n’est `Dispose` pas appelé, les classes correctement mises en œuvre appellent lorsque le [finalisateur s’exécute.](/dotnet/csharp/programming-guide/classes-and-structs/destructors)

Examinons le code ci-dessous.

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) est une classe gérée, de sorte que toute instance sera recueillie à la fin de la demande.

L’image suivante montre le profil `fileprovider` de mémoire tout en invoquant l’API en permanence.

![graphique précédent](memory/_static/fileprovider.png)

Le graphique précédent montre un problème évident avec la mise en œuvre de cette classe, car il ne cesse d’augmenter l’utilisation de la mémoire. Il s’agit d’un problème connu qui fait l’objet d’un suivi dans [ce dossier](https://github.com/dotnet/aspnetcore/issues/3110).

La même fuite pourrait se produire dans le code utilisateur, par l’un des suivants:

* Ne pas libérer la classe correctement.
* Oubliant d’invoquer la `Dispose`méthode des objets dépendants qui doivent être éliminés.

### <a name="large-objects-heap"></a>Grand tas d’objets

L’allocation fréquente de mémoire/cycles libres peut fragmenter la mémoire, en particulier lors de l’attribution de gros morceaux de mémoire. Les objets sont attribués dans des blocs de mémoire contigus. Pour atténuer la fragmentation, lorsque le GC libère la mémoire, il essaie de le défragmenter. Ce processus est appelé **compactage**. Compaction implique le déplacement d’objets. Le déplacement de gros objets impose une pénalité de performance. Pour cette raison, le GC crée une zone de mémoire spéciale pour _les grands_ objets, appelé le tas de [gros objets](/dotnet/standard/garbage-collection/large-object-heap) (LOH). Les objets de plus de 85 000 octets (environ 83 KB) sont :

* Placé sur le LOH.
* Pas compacté.
* Recueillis au cours de la génération 2 GCs.

Lorsque le LOH est plein, le GC déclenchera une collection de génération 2. Collections de génération 2 :

* Sont intrinsèquement lents.
* En outre, encourir le coût de déclencher une collection sur toutes les autres générations.

Le code suivant compacte immédiatement le LOH :

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

Voir <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> pour plus d’informations sur le compactage du LOH.

Dans les conteneurs utilisant .NET Core 3.0 et plus tard, le LOH est automatiquement compacté.

L’API suivante qui illustre ce comportement :

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

Le graphique suivant montre le `/api/loh/84975` profil de mémoire d’appeler le point de terminaison, sous la charge maximale:

![graphique précédent](memory/_static/loh1.png)

Le graphique suivant montre le `/api/loh/84976` profil de mémoire d’appeler le point de terminaison, en allouant *un seul byte de plus*:

![graphique précédent](memory/_static/loh2.png)

Remarque : `byte[]` La structure a des octets aériens. C’est pourquoi 84 976 octets déclenchent la limite de 85 000.

Comparaison des deux graphiques précédents :

* L’ensemble de travail est similaire pour les deux scénarios, environ 450 Mo.
* Les demandes sous LOH (84 975 octets) montrent principalement des collections de génération 0.
* Les demandes sur LOH génèrent des collections de génération 2 constantes. Les collections de génération 2 sont chères. Plus de processeur est nécessaire et le débit baisse de près de 50%.

Les gros objets temporaires sont particulièrement problématiques parce qu’ils causent des OGM gen2.

Pour une performance maximale, l’utilisation d’objets voluminés doit être réduite au minimum. Si possible, divisez de gros objets. Par exemple, [Response Caching](xref:performance/caching/response) middleware dans ASP.NET Core divise les entrées de cache en blocs de moins de 85 000 octets.

Les liens suivants montrent l’approche ASP.NET Core pour garder les objets sous la limite LOH :

* [ResponseCaching/Streams/StreamUtilities.cs](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [ResponseCaching/MemoryResponseCache.cs](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

Pour plus d'informations, consultez les pages suivantes :

* [Grand tas d’objets découvert](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Grand tas d’objets](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a>HttpClient

Une utilisation <xref:System.Net.Http.HttpClient> incorrecte peut entraîner une fuite de ressources. Ressources système, telles que les connexions de base de données, les prises, les poignées de fichiers, etc. :

* Sont plus rares que la mémoire.
* Sont plus problématiques lorsqu’ils fuient que la mémoire.

Expérimentés développeurs .NET <xref:System.IDisposable.Dispose*> savent faire <xref:System.IDisposable>appel à des objets qui implémenter . Le fait de `IDisposable` ne pas éliminer les objets qui implémentent entraîne généralement une fuite de mémoire ou des fuites de ressources système.

`HttpClient`met `IDisposable`en œuvre, mais ne doit **pas** être éliminé sur chaque invocation. Il `HttpClient` faut plutôt réutiliser.

Le critère d’évaluation suivant `HttpClient` crée et dispose d’une nouvelle instance sur chaque demande :

```csharp
[HttpGet("httpclient1")]
public async Task<int> GetHttpClient1(string url)
{
    using (var httpClient = new HttpClient())
    {
        var result = await httpClient.GetAsync(url);
        return (int)result.StatusCode;
    }
}
```

Sous charge, les messages d’erreur suivants sont enregistrés :

```
fail: Microsoft.AspNetCore.Server.Kestrel[13]
      Connection id "0HLG70PBE1CR1", Request id "0HLG70PBE1CR1:00000031":
      An unhandled exception was thrown by the application.
System.Net.Http.HttpRequestException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted --->
    System.Net.Sockets.SocketException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted
   at System.Net.Http.ConnectHelper.ConnectAsync(String host, Int32 port,
    CancellationToken cancellationToken)
```

Même si `HttpClient` les instances sont éliminées, la connexion réseau réelle prend un certain temps à être libérée par le système d’exploitation. En créant continuellement de nouvelles connexions, _l’épuisement des ports_ se produit. Chaque connexion client nécessite son propre port client.

Une façon de prévenir l’épuisement portuaire `HttpClient` est de réutiliser le même cas :

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

L’instance `HttpClient` est libérée lorsque l’application s’arrête. Cet exemple montre que toutes les ressources jetables ne doivent pas être éliminées après chaque utilisation.

Voir ce qui suit pour une meilleure `HttpClient` façon de gérer la durée de vie d’une instance:

* [HttpClient et gestion de la durée de vie](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [BLOG d’usine HTTPClient](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a>Mise en commun d’objets

L’exemple précédent `HttpClient` a montré comment l’instance peut être rendue statique et réutilisée par toutes les demandes. La réutilisation empêche de manquer de ressources.

Mise en commun des objets :

* Utilise le modèle de réutilisation.
* Est conçu pour les objets qui sont coûteux à créer.

Une piscine est une collection d’objets pré-initialisés qui peuvent être réservés et libérés sur les fils. Les pools peuvent définir des règles d’attribution telles que des limites, des tailles prédéfinies ou un taux de croissance.

Le paquet NuGet [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contient des classes qui aident à gérer ces pools.

Le point de terminaison `byte` suivant de l’API instantanément un tampon qui est rempli de nombres aléatoires sur chaque demande:

```csharp
        [HttpGet("array/{size}")]
        public byte[] GetArray(int size)
        {
            var random = new Random();
            var array = new byte[size];
            random.NextBytes(array);

            return array;
        }
```

L’affichage graphique suivant appelant l’API précédente avec une charge modérée :

![graphique précédent](memory/_static/array.png)

Dans le tableau précédent, les collections de génération 0 se produisent environ une fois par seconde.

Le code précédent peut être optimisé `byte` en mettant en commun le tampon en utilisant [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1). Une instance statique est réutilisée à travers les demandes.

Ce qui est différent avec cette approche, c’est qu’un objet mis en commun est retourné de l’API. Cela signifie:

* L’objet est hors de votre contrôle dès que vous revenez de la méthode.
* Vous ne pouvez pas libérer l’objet.

Pour configurer l’élimination de l’objet :

* Encapsulez le tableau mis en commun dans un objet jetable.
* Enregistrez l’objet mis en commun avec [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).

`RegisterForDispose`s’occupera `Dispose`d’appeler l’objet cible afin qu’il ne soit publié que lorsque la demande HTTP est terminée.

```csharp
private static ArrayPool<byte> _arrayPool = ArrayPool<byte>.Create();

private class PooledArray : IDisposable
{
    public byte[] Array { get; private set; }

    public PooledArray(int size)
    {
        Array = _arrayPool.Rent(size);
    }

    public void Dispose()
    {
        _arrayPool.Return(Array);
    }
}

[HttpGet("pooledarray/{size}")]
public byte[] GetPooledArray(int size)
{
    var pooledArray = new PooledArray(size);

    var random = new Random();
    random.NextBytes(pooledArray.Array);

    HttpContext.Response.RegisterForDispose(pooledArray);

    return pooledArray.Array;
}
```

L’application de la même charge que la version non mise en commun se traduit par le graphique suivant :

![graphique précédent](memory/_static/pooledarray.png)

La principale différence est attribuée aux octets, et par conséquent beaucoup moins de collections de génération 0.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Garbage collection](/dotnet/standard/garbage-collection/)
* [Comprendre différents modes GC avec Concurrency Visualizer](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [Grand tas d’objets découvert](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Grand tas d’objets](/dotnet/standard/garbage-collection/large-object-heap)
